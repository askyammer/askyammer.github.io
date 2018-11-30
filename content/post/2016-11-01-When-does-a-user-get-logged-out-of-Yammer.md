---
title: When Does a User Get Logged Out of Yammer?
subtitle: By Inbar Cizer Kobrinsky
date: 2016-11-01
tags: ["Authentication", "Logging In", "Suspended"]
---
When a user logs in to Yammer on the web, mobile application or authenticate with a Yammer app, they get a token. The token for the Yammer mobile application and to a Yammer app will be valid as long as the user is active (and they didn’t revoke access to the app). On the web the session has the lifetime of the browser (See Session timeouts for Office 365 this article for more details).

Customers often wants to make sure that users can’t post to Yammer once they left the organization. Another related request is to understand why does a user got logged out of Yammer. This post describes the different scenarios in which Yammer will destroy the user’s token, which will results in the user getting logged out of Yammer.

### User is suspended in Yammer
When a user is suspended in Yammer, all of their tokens are destroyed. Suspended user will be in this state for 90 days and then will be deleted.

The user can activate their accounts only if they still have access to it. For example – if the user is using Office 365 credentials they still need to be able to access their Office 365 account in order to activate their Yammer account. Even if the user has activated their account, they will still need to obtain a new token for each of the application they used by authenticating again on each of them.

#### Users are suspended in Yammer in the following scenarios:

* **Office 365 account is deleted** – This scenario is relevant for Yammer users that uses Office 365 credentials to login to Yammer (mapped account). Changes in the Office 365 account are synced to Yammer and so does the user’s status. When the user is deleted in Office 365, the Yammer user will get suspended.

> More information: Manage Yammer users across their life cycle from Office 365 

* **Yammer license is removed from an Office 365 account** – This scenario is also relevant for mapped Yammer accounts. When a licenses is removed in Office 365 - the user in Yammer is suspended.

>More information: Manage Yammer user licenses in Office 365.  

* **Verified Admin action** – User can be suspended by a Yammer verified admin from the “Remove user” page (under Yammer Network Admin). Users can also be suspended by Verified admin as a result of “Suspend” action in bulk update. 

>More information: bulk update and deactivating users in Yammer. 

* **Yammer DSync (legacy tool)** – Users can also be suspended if you are still using the legacy Yammer DSync tool to sync users from OnPrem AD to Yammer. When a user is deleted or Disabled in AD - Yammer DSync will suspend the user.

>NOTE: Yammer DSync will be deprecated on December 1st 2016. More info: Plan for Yammer SSO and DSync deprecation. 

* **A user can suspend other users** - by clicking on “If <user> is no longer a part of the <network> click here”. This suspension will destroy the user’s tokens. If the user is still an active user, they can login to Yammer and reactive the account. 

### Tokens are destroyed but the user is still active

There are a few scenarios where the tokens are destroyed but user is still active. In order to obtain a new token the user needs to authenticate again with Yammer.

* **Logging out all users from the network** – When a verified admin makes the configuration changes to disable Yammer SSO and enable ‘Enforce Office 365 identity’, they have the option to log out all the users from the network. Taking this action will destroy the tokens for all users in the network from all their devices and applications. We recommend to use this setting when disabling Yammer SSO in order to make sure that the users in the network will have to login again to Yammer, this time with their Office 365 credentials. This is not a mandatory setting.In order to log out all the users from the network, verified admin can take the following steps: Login to Yammer as a verified admin and go to the Network Admin and then go to the Security settings page. Under the Security settings, mark the checkbox next to Enforce Office 365 identity. When promote, mark the setting next to Logout all users from the network. 

* **User is blocked in Office 365** – This scenario is relevant for Yammer users that uses Office 365 credentials to login to Yammer (mapped users). If you use Azure AD connect to sync users from OnPrem AD to Office 365, when a user is disabled in AD they will be blocked in Office 365. Office 365 users can also be blocked from the portal.When a user is blocked in Office 365, in Yammer we will destroy all tokens, but the user will remain active in Yammer. The user must be able to login to Office 365 in order to login to Yammer; As long as the user is blocked – they won't be able to login.  

* **User mapping process** – I have mentioned above a few scenarios that are relevant for Yammer users that uses Office 365 credentials to login to Yammer. For existing Yammer users (that weren’t created from Office 365), before a user in Yammer can use their Office 365 credentials, we need to connect the Yammer account to the Office 365 account (mapping process). Information about how we connect these accounts can be found in the article below (under “How it works” section). For this post, it is important to remember that as part of the mapping process we also destroy all of the user’s token, so they have to login again for all the apps that they were using prior to the mapping. This is a one time process.

>Information about how we connect the Office 365 account to the Yammer account can be found here: Office 365 sign-in for Yammer.  

* **User action** – A user can also revoke access for apps they have approved before. This action is in the app level and doesn’t destroy all tokens. Login to Yammer and go to the user’s profile. Click on Edit Settings and select the My Applications tab. Under my application, the user will see the list of apps they have approved. The user then click on “Remove Access” for a specific app. 

* **Network is disabled as part of deprovision process** – Yammer follows the Office 365 data deletion guidelines described below. If the Yammer subscription has expired at first step the Yammer network will be disabled and the tokens of all users in the network will be destroyed. If the network is later enabled because there is a new valid Yammer subscription available in the tenant, users will need to authenticate again for all their apps.
More information about the Office 365 data deletion process: What happens to my data and access when my Office 365 for business subscription ends? 

## About the Author
**Inbar Cizer Kobrinsky** is a Support Escalation Engineer on the Yammer Support Escalation team.