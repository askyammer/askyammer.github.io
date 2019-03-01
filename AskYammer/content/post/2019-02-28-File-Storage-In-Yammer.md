---
title: "File Storage in Yammer"
subtitle: By Alex Blaine
date: 2019-02-28T19:08:33-06:00
tags: ["API", "PowerShell", "Files"]
---

With our upcoming move to [store Yammer files in SharePoint](https://support.office.com/en-us/article/how-do-i-tell-where-my-yammer-files-are-being-stored-fadfdefa-e00d-40b6-94cb-a9ddb171a443), we've received requests from admins to help audit the files currently stored in Yammer.  While we don't expect file storage quotas in SharePoint to be an issue, we understand that Yammer admins would like some additional clarity about how much file storage is being used in Yammer.  The below PowerShell script will generate a .csv file showing how much data is stored in each Yammer group as well as the total of all files in the Yammer network.

### Prerequesites

In order to utilize this script, you'll need to provide some additional information:

1. A Verified Admin's OAuth token, stored in the $oauthToken variable at the top of the script.  If you're unfamiliar with this concept or need a refresher on how to generate one of these, please see the Authorizing an API call section of my [Yammer API Primer](https://askyammer.github.io/post/2017-09-05-yammer-api-primer/).
2. The Files.csv file from a [data export of your network](https://docs.microsoft.com/en-us/yammer/manage-security-and-compliance/export-yammer-enterprise-data#export-yammer-network-data-by-date-range-and-network).  This script will only calculate the size of files listed in this export, so ensure the date range used encapsulates the lifetime of your Yammer network.  For the purposes of this script, it's recommended that you do not include attachments or external networks in the export
3. An up to date version of PowerShell.  This script was written and tested on PowerShell version 5.1.17763.316.  You can determine your PowerShell version by outputting the [$PSVersionTable.PSVersion](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-6#psversiontable) variable

### How to use this script

Start by copying the text in the script below and pasting it into a .ps1 file you create on your hard drive.  You'll then need to populate the $filesCsvPath, $outCsvPath, and $oauthToken variables at the top of the script.  The $filesCsvPath variable should contain the path to the Files.csv file you extracted from a data export.  The $outCsvPath should contain the path and name of the file to which you'd like the script's output written.  Keep in mind if you provide a path to an existing file, that file will be overwritten by this script so please enter a path with a unique file name.

### How this script works

This script will first test that you have provided a valid Verified Admin token and then loop through each line in your Files.csv file.  It will make an API call to the file_api_url in each row (appending .json to the end to receive a .json response rather than .xml).  It pulls the file size in bytes from the file metadata returned by this API call and adds it to the total file size of the group to which the file belongs.  If the script encounters a file in a group it hasn't seen before, it'll create a new entry for that group and start totalling the size of files in that group.  Private Messages and All Company are special cases in that they don't have group IDs and won't be stored in SharePoint, so files in those categories are separated out into their own "groups."  At the end of the script, it will convert the byte totals to a more human readable file size (in KB, MB, GB) and output its findings both to the PowerShell window and to a .csv file located at the path you've provided.

### The Script

````PowerShell
# Get the total size of files stored in your Yammer network, separated by group
# All Company and Private Messages will stay in Yammer file storage and will be grouped together
# First enable Private Content Mode, export network data over the life of your network, and
# Pull out the Files.csv from that export.  Add your Admin token to $oauthToken below and
# the path to Files.csv in $filesCsvPath
# We'll be outputting the data to screen and to a .csv so enter the desired path to the output
# file in $outCsvPath 

$filesCsvPath = 'C:\Path\To\Files.csv'
$outCsvPath = 'C:\Path\To\CSVOutput.csv'
$oauthToken = ''

# Test $oauthToken
try {
    $currentUser = Invoke-WebRequest -Uri 'https://www.yammer.com/api/v1/users/current.json' `
                                     -Headers @{'Authorization' = "Bearer $($oauthToken)"}
} catch {
    $errorText = "Could not verify OAuth Token.  " `
                + "Received response code: $($_.Exception.Response.StatusCode.Value__)" `
                + "with description: $($_.Exception.Response.StatusDescription)"
    Write-Output $errorText
    Read-Host -Prompt "Press Enter to exit"
    Return
}

# Convert the .json response into an Object
$userObject = ConvertFrom-Json $currentUser.Content 
if( $userObject.verified_admin -ne 'true' ) {
    Write-Output "$($userObject.email) is not a Verified Admin.  Please provide a VA token"
    Read-Host -Prompt "Press Enter to exit"
    Return
}

# Initialize array to hold each group's file size
$groupFileSizeTotal = @{}

# Test opening Files.csv
try {
    $filesCsv = Import-Csv $filesCsvPath
} catch {
    Write-Output "Could not load files CSV.  Please ensure you've provided the correct path."
    Read-Host -Prompt "Press Enter to exit"
    Return
}

# Loop through Files.csv, make API call, check for rate limiting, add file size to group total
$filesCsv | ForEach-Object {
    do {
        $retryCall = $false
        # Make sure we omit deleted files as this will return a 404
        if (-not $_.deleted_at) { 
            #Store group_id and name for file size array index
            $groupIndex = "$($_.group_id) - $($_.group_name)" 
            $inPM = $_.in_private_conversation
            # Append .json to end of API URL for .json payload
            $uri = "$($_.file_api_url).json" 
            try {
                $response = Invoke-WebRequest -Uri $uri `
                                              -Headers @{'Authorization' = "Bearer $($oauthToken)"}
            } catch {
                if( $_.Exception.Response.StatusCode.Value__ -eq 429 `
                -or $_.Exception.Response.StatusCode.Value__ -eq 503 ) {
                    $retryCall = $true
                }
                $errorText = "Could not retrieve File Metadata.  " `
                            + "Received response code: $($_.Exception.Response.StatusCode.Value__) " `
                            + "with description: $($_.Exception.Response.StatusDescription)"
                Write-Output $errorText
            }
            if ($retryCall) {
                # If we get rate limited or service unavailable, back off for 15 seconds and retry
                $errorText = 'We got rate limited or service was temporarily unavailable, ' `
                            + 'sleeping for 15 seconds'
                Write-Output $errorText
                Start-Sleep -Seconds 15 
            } elseif ($response.StatusCode -eq 200) {
                # Convert the .json response into an Object we can use to pull the size
                $responseObject = ConvertFrom-Json $response.Content 
                # If this file isn't associated with a group ID
                if ($groupIndex -eq ' - ') { 
                    if ($inPM -eq 'TRUE') {
                        # If we haven't seen any Private Message files before
                        if(-not $groupFileSizeTotal['Private Messages']) { 
                            # Initialize PM entry and store file size in it
                            $groupFileSizeTotal['Private Messages'] = $responseObject.files.size 
                        } else {
                            # Add to Private Messages total
                            $groupFileSizeTotal['Private Messages'] += $responseObject.files.size 
                        }
                    } else {
                        # If we haven't seen any All Company files before
                        if(-not $groupFileSizeTotal['All Company']) { 
                            # Initialize All Company entry and store file size in it
                            $groupFileSizeTotal['All Company'] = $responseObject.files.size 
                        } else {
                            # Add to All Company total
                            $groupFileSizeTotal['All Company'] += $responseObject.files.size 
                        }
                    }
                } else {
                    # If we haven't seen this group before
                    if(-not $groupFileSizeTotal[$groupIndex]) { 
                        # Initialize this group's entry and store current file's size in it
                        $groupFileSizeTotal[$groupIndex] = $responseObject.files.size 
                    } else {
                        # Add the file size of the current file to its group's total
                        $groupFileSizeTotal[$groupIndex] += $responseObject.files.size 
                    }
                }
            }
        }
    } while ($retryCall)
}

# Function to convert bytes to human readable size
Function HumanReadable($bytes) {
    if($bytes / 1tb -ge 1) {
        return "$($bytes / 1tb)TB"
    } elseif ($bytes / 1gb -ge 1) {
        return "$([math]::Round($bytes / 1gb,2))GB"
    } elseif ($bytes / 1mb -ge 1) {
        return "$([math]::Round($bytes / 1mb,2))MB"
    } elseif ($bytes / 1kb -ge 1) {
        return "$([math]::Round($bytes / 1kb,2))KB"
    } else {
        return "$($bytes)B"
    }
}

# Generate CSV Output
$csvOut = @()
# Variable to hold total file size in network
$totalFileSize = 0 
foreach($key in $groupFileSizeTotal.Keys) {
    $totalFileSize += $groupFileSizeTotal[$key]
    $csvOut += New-Object -TypeName PSobject -Property @{
        Group_ID_and_Name = $key
        TotalFileSize = HumanReadable($groupFileSizeTotal[$key])
    }
}

# Add a separator for Total File Size
$csvOut += New-Object -TypeName PSobject -Property @{
    Group_ID_and_Name = "--------"
    TotalFileSize = "--------"
}

# Add total file size
$csvOut += New-Object -TypeName PSobject -Property @{
    Group_ID_and_Name = "Total file size in network"
    TotalFileSize = HumanReadable($totalFileSize)
}

# Output to screen
Write-Output $csvOut

# Write result to .csv
$csvOut | Export-Csv -NoTypeInformation -Path $outCsvPath
Read-Host -Prompt "Press Enter to exit"
````

## About the Author
**Alex Blaine** is a Support Escalation Engineer and code monkey on the Yammer Support Escalation team.