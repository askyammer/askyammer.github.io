---
title: "Posting Message With Attachment via API"
subtitle: By Alex Blaine
date: 2019-03-05T18:19:27-06:00
tags: ["API", "PowerShell", "Attachment", "Post", "Message"]
---

Posting messages to Yammer via API is a relatively simple process.  When you want to add file attachments into the mix, things get more complicated as you have to utilize multi-part form data to post the message.  I'll be focusing on posting a message to Yammer with an attachment in a single API call.  Keep in mind this method only supports attachments up to 25MB in size.  If you need to post files larger than 25MB, you'll need to use the Yammer web, desktop, or mobile clients for this.  For reference, our documentation for the endpoint I'll be utilizing is located at [https://developer.yammer.com/docs/messages-json-post](https://developer.yammer.com/docs/messages-json-post).

### Prerequesites

This document assumes you have the following:

1. A basic understanding of how to use Yammer APIs including obtaining an OAuth token.  If you're not familiar with this, please familiarize yourself with my [Yammer API Primer](https://askyammer.github.io/post/2017-09-05-yammer-api-primer/).
2. A file you'd like to attach to a Yammer message.
3. An up to date version of PowerShell.  This script was written and tested on PowerShell version 5.1.17763.316.  You can determine your PowerShell version by outputting the [$PSVersionTable.PSVersion](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-6#psversiontable) variable.

### How to use this script

While the script can be used as-is to post a message to Yammer with an attachment, it's intent is to illustrate using the .Net library to prepare and execute a multi-part form data API call.  For this script to work, you'll need to populate the $token variable with the OAuth token you'll be using, the $message variable with the text you want in the body of your Yammer post, $InFile with the path to the file you're attaching, and $groupID to the group ID you're posting to.  In your own usage, you might choose to omit the group ID or pass other paramaters instead of / in addition to the group_id.  You can see the parameters this endpoint accepts in [its documentation](https://developer.yammer.com/docs/messages-json-post).

### How this script works

The script starts by importing the System.Net.Http and System.Web libraries which contain the cmdlets used to generate and make the API call.  After having the necessary variables defined, it starts building the http multi-part form data content by initializing the $content variable.  Next, it adds the body parameter to be passed to the API endpoint and then the group_id parameter.  Notice that for both these variables, it surrounds them in quotes.  After this, it gets the Mime type of the file (based off its file extension), the data from the file, and the file name from the file.  It uses these to create and add the attachment parameter to be passed to the endpoint.

Once the $content variable is properly formatted, the REST client ($httpClient) is set up.  The authorization headers are added to the client, and then the API uri and the prepared $content is passed to the REST client during an Async call.  The script then disposes of the resources it used to keep them from staying in memory.

### The Script

````PowerShell
# Import libraries used in this script

Add-Type -AssemblyName System.Net.Http
Add-Type -AssemblyName System.Web

# Set up variables used in the rest of this script

# Set API URI and OAuth Token
$uri = 'https://www.yammer.com/api/v1/messages.json'
$token = ''

# Set the text of the post, the file you'd like to attach, and the groupid you're posting to
$message = ''
$InFile = ''
$groupID = ''


# Start building the multipart/form-data content

$content = New-Object System.Net.Http.MultipartFormDataContent

# Add Body form-data

$messageDispositionValue = `
        New-Object System.Net.Http.Headers.ContentDispositionHeaderValue "form-data"
$messageDispositionValue.Name = """body"""

$messageContent = New-Object System.Net.Http.StringContent $message
$messageContent.Headers.ContentDisposition = $messageDispositionValue
$content.Add($messageContent)

# Add GroupID in which to post

$groupDispositionValue = `
        New-Object System.Net.Http.Headers.ContentDispositionHeaderValue "form-data"
$groupDispositionValue.Name = """group_id"""

$groupContent = New-Object System.Net.Http.StringContent $groupID
$groupContent.Headers.ContentDisposition = $groupDispositionValue
$content.Add($groupContent)

# Add the attachment to the post

# Get Content-Type and File Contents of the file to attach
$ContentType = [System.Web.MimeMapping]::GetMimeMapping($InFile)
$FileStream = [System.IO.FileStream]::new($InFile, [System.IO.FileMode]::Open)
$attachmentContent = [System.Net.Http.StreamContent]::new($FileStream)

$attachmentDispositionValue = `
        New-Object System.Net.Http.Headers.ContentDispositionHeaderValue "form-data"
$attachmentDispositionValue.Name = "attachment1"
$FileName = Split-Path $InFile -leaf
$attachmentDispositionValue.FileName = $FileName

$attachmentContent.Headers.ContentDisposition = $attachmentDispositionValue
$attachmentContent.Headers.ContentType = $ContentType
$content.Add($attachmentContent)


# Set up and authorize the REST client

$httpClientHandler = New-Object System.Net.Http.HttpClientHandler
$httpClient = New-Object System.Net.Http.Httpclient $httpClientHandler
$httpClient.DefaultRequestHeaders.Add(“Authorization”, “Bearer ” + $token)


# Send the REST call to the API endpoint

$response = $httpClient.PostAsync($uri, $content).Result


# Clean up our resources

if($null -ne $httpClient)
{
    $httpClient.Dispose()
}

if($null -ne $response)
{
    $response.Dispose()
}
````

## About the Author
**Alex Blaine** is a Support Escalation Engineer and code monkey on the Yammer Support Escalation team.