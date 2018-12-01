---
title: Find Office 365 users with Yammer licenses
subtitle: By Inbar Cizer Kobrinsky
date: 2016-10-25
tags: ["licenses", "powershell"]
---

Many times, we see customers that would like to know how many of their users have license for a specific Office 365 service. This post focuses on how to locate the users with Yammer licenses using PowerShell, it is based on this TechNet article: View account license and service details with Office 365 PowerShell with some changes regarding Yammer.

### Before you begin

The procedures in this topic require you to connect to Office 365 PowerShell. For instructions, see Connect to Office 365 PowerShell.

It is important to remember that Yammer license can assigned by the “Yammer Enterprise” subscription (Yammer standalone subscription) or as part of another subscription that includes Yammer (such as Office 365 Enterprise E3). In my Office 365 tenant, I have Yammer as part of the Office 365 Enterprise E3 subscription. If you have multiple subscription that include Yammer, make sure you are running this procedure for all of the subscriptions. To locate list of subscriptions available on your tenant use:

````PowerShell
Get-MsolAccountSku
````
In order to locate the users that have Yammer license assigned, you will need to first find the “LicenseIndexNumber” and “ServiceIndexNumber” in your tenant. These values can be different from tenant to tenant based on the subscriptions assigned to the tenant.

### Locate LicenseIndexNumber and ServiceIndexNumber

Find the “LicenseIndexNumber” by running the following command:

````PowerShell
Get-MsolUser -UserPrincipalName <UPN>| Format-List DisplayName,Licenses
````
20160914-115702
In my tenant, I’m looking for the location of the “yeslogin:ENTERPRISEPACK” (the E3 subscription). As you can see above, LicenseIndexNumber in my tenant will be 0 because it is located in the first index of the array.

Find the “ServiceIndexNumber” by running the following:

````PowerShell
(Get-MsolUser -UserPrincipalName <UPN>).Licenses[<LicenseIndexNumber>].ServiceStatus
````
20160914-120729
As you can see, I have replaced “LicenseIndexNumber” with 0 (based on the results of the previous command). In my tenant ServiceIndexNumber will be 3 because it is located in the fourth index of the array.

### Find users with Yammer licenses

Once you have the above information, you can run the following command to locate all users with Yammer licenses:

````PowerShell
Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses[<LicenseIndexNumber>].ServiceStatus[<ServiceIndexNumber>].ProvisioningStatus -ne "Disabled" -and $_.Licenses[0].ServiceStatus[9].ProvisioningStatus -ne $null }
````

This is an example of one of the users that was return in the above command. From the E3 subscription, Only Yammer is assigned to this user.

20160914-121819

### More information
How to audit Yammer users in networks connected to Office 365

## About the author
**Inbar Cizer Kobrinsky** is a Support Escalation Engineer on the Yammer Support Escalation team.