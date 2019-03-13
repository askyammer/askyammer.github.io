---
title: Yammer API Primer
subtitle: By Alex Blaine
date: 2017-09-05
tags: ["API", "PowerShell"]
---

While most day to day tasks can be accomplished in the Yammer web app, utilizing our APIs can open up administrative and automation options previously very time consuming.  If you’ve never worked with an API before, this can seem like a daunting task.  With this article, I hope to demystify APIs and illustrate how anyone can utilize them to perform tasks like obtaining, posting, and deleting messages.

### Parts of an API call

There are 3 main parts of an API call:

1. The endpoint URL. A list of our supported API endpoints, their URLs and the input they take can be found at https://developer.yammer.com/docs/rest-api-rate-limits.
2. The authorization headers – This is akin to logging into the API. It tells the API who is making the call and that you’re authorized to make those calls.
3. Any data passed to that API endpoint – For example a user id or a page number. Each endpoint’s documentation will describe the data it can consume when generating its output.

### Authorizing an API call
Every API call must be authorized before it will return data. To authorize an API call, an authorization header with the OAuth token is sent stating who is making the call.  To obtain this OAuth token, first register an app in Yammer, detailed at https://developer.yammer.com/docs/app-registration.  If you are unsure what to put as the Website and Redirect URI, just put an https:// address to your company’s website for the time being.  Once the app is registered, click “Generate a developer token for this application” and save this OAuth token.  It will be used it to authorize your API calls and won’t expire until something is explicitly done to revoke it.

 
![App Registration](/img/2017-08-05 - Yammer API Primer/App_Registration.png)
<center>*App Registration Example*</center>
![Obtaining an OAuth Token](/img/2017-08-05 - Yammer API Primer/Obtain_OAuth.png)
<center>*Obtaining an OAuth Token Example*</center>

Once you have obtained this OAuth token, you need to pass it to the API in a header.  This header looks like “Authorization: Bearer OAUTH_TOKEN” in plain text, but an example of passing this in PowerShell can be found below.

### Using PowerShell to call Yammer APIs
To make API calls in PowerShell, the Invoke-RestMethod cmdlet documented at https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-restmethod?view=powershell-5.1 will be used.  Here is a sample script that calls the messages.json endpoint and dumps its response: its response:

````PowerShell
$uri = "https://www.yammer.com/api/v1/messages.json"
$token = "YOUR_OAUTH_TOKEN_HERE"
$headers = @{ "Authorization" = "Bearer " + $token }
$response = Invoke-RestMethod -Method Get -Uri $uri -Header $headers
Write-Output $response
````
### Passing data to an API endpoint

There are 3 main ways to pass data to an API endpoint.

1. In the endpoint, itself. For example, to get info on a user by their user_id (documented at https://developer.yammer.com/docs/usersidjson), you call the endpoint https://www.yammer.com/api/v1/users/186029475.json where 186029475 is the user_id
2. In query string variables, like page numbers. From https://developer.yammer.com/docs/usersin_groupidjson, this might look like https://www.yammer.com/api/v1/users/in_group/5678.json?page=2 where the ? separates the endpoint URL from its query string variables.  To pass multiple variables, you separate them with an & like https://www.yammer.com/api/v1/users/in_group/5678.json?page=2&reverse=TRUE
3. In the body of the message, specifically when using the POST method

For 1 and 2 above, you change nothing about your API calls except the actual URL you pass. 3 is a little more complicated and I’ll illustrate its use with an example.  In this example, you will be posting a message to a specific group.  You’ll need a group_id to do this and you can locate one by navigating to a group in Yammer and look for the “feedId=####” in the URL and the #### is your group_id.

### POST in PowerShell

To POST data to an API, information like the body of the message and the group to post into must be sent to the API.  In this example, a format called json (JavaScript Object Notation) is used to pass this data.  First, the content-type header stating the data is being sent as json is added.  Next, a $jsonbody variable is created to hold the json data.  The -Method Post is used instead of Get and add the -Body $jsonbody is added to the Invoke-RestMethod call.
````PowerShell
$uri = "https://www.yammer.com/api/v1/messages.json"
$token = "YOUR_OAUTH_TOKEN"

$headers = @{
"Authorization" = "Bearer " + $token
"content-type" = "application/json"
}

$jsonbody = '{
"body": "Hello World!",
"group_id": 8510571
}'

$response = Invoke-RestMethod -Method Post -Uri $uri -Header $headers -Body $jsonbody
````

You could also pass the body data as unencoded text in which case you would change the content-type and body.  This would look like:

````PowerShell
$uri = "https://www.yammer.com/api/v1/messages.json"
$token = "YOUR_OAUTH_TOKEN"

$headers = @{
"Authorization" = "Bearer " + $token
"content-type" = " application/x-www-form-urlencoded"
}

$formbody = “body=Hello+World!&group_id=8510871”
$response = Invoke-RestMethod -Method Post -Uri $uri -Header $headers -Body $formbody
````

### Deleting in PowerShell

Just like creating and reading resources via our API, messages can be deleted by users authorized to do so.  Users are authorized to delete their own messages, group admins are authorized to delete messages in groups where they’re an admin, and verified admins can delete any posts they can see 

> Note: For Verified Admins to delete messages in private groups / messages to which they don’t belong, they must first enable Private Content Mode). 

In this example you will be deleting a message with a specific ID (1234) as documented at https://developer.yammer.com/docs/messagesid.  You’ll need the specific message ID to be deleted which is most often found in a Data Export.  The biggest change you’ll notice is the -Method changes to “Delete”.

````PowerShell
$uri = "https://www.yammer.com/api/v1/messages/1234"
$token = "YOUR_OAUTH_TOKEN"
$headers = @{ "Authorization" = "Bearer " + $token }
$response = Invoke-RestMethod -Method Delete -Uri $uri -Header $headers
````

### Troubleshooting
When API calls don’t give the expected results, the two best troubleshooting resources available are Fiddler and the Yammer Developer Center.  If you haven’t installed Fiddler already, you can obtain it from https://telerik.com/download/fiddler.  To start, open Fiddler and hit F12 (or click “Capturing” in the bottom left of the window) to make it stop capturing traffic in the left pane.  Duplicate the failing API call in the Fiddler Composer and execute it:

![GET Example](/img/2017-08-05 - Yammer API Primer/API_Fiddler.png)

![POST Example](/img/2017-08-05 - Yammer API Primer/API_Fiddler_Composer.png)

Double click the line that is generated in the left pane (the API call’s “session” in Fiddler) and the inspectors will show up:

![Fiddler Inspector](/img/2017-08-05 - Yammer API Primer/API_Fiddler_Headers.png)

Make sure Headers is clicked on the bottom pane and review the response code and reason listed under Response Headers:

* “HTTP/1.1 401 Unauthorized” could indicate either your OAuth token is invalid or you’re trying to do something you’re not allowed to do (like post to a private group to which you don’t belong)
* “HTTP/1.1 403 Forbidden” could indicate you’re making calls from a blacklisted IP address
* “HTTP/1.1 400 Bad Request” indicates the request isn’t formatted properly. For example, if the content-type header is missing when posting a message
* “HTTP/1.1 500 Internal Server Error” indicates you sent something in the API request that Yammer didn’t know how to handle


>**Note:** The Try It Out section has been removed from our developer page so this section is not currently relevant. This note will be removed if the Try It Out section is added back to our developer docs.

Once you’ve made sure you’re authorized to perform the action you’re trying to perform, the next step is to make sure you’ve formatted your API call properly.  For this, head to the Yammer Developer Center at https://developer.yammer.com/docs/rest-api-rate-limits and find the API being used.  As an example, let’s pretend you don’t know how to post a message to a group (which was covered above).  Note that when creating a resource (like a new post in Yammer), you use the POST method so find the POST message endpoint page located at https://developer.yammer.com/docs/messages-json-post and scroll to the bottom where it says Try It Out.  You know the body is going to be “Hello World!” and the group is going to be 8510571, so input that into the form and click Try It! (you might first have to authorize the Yammer Developer Center app on your Yammer account):

![Try it Out](/img/2017-08-05 - Yammer API Primer/API_Tryitout.png)

It will then show you the exact request that was made to the API and the response.  The relevant part of this is shown here:

![Try it Out Response](/img/2017-08-05 - Yammer API Primer/API_Tryitout_response.png)

You’ll note that things like the Accept, accept-encoding, content-length, and direct_to_user_ids get added automatically.  You don’t have to add these to your call to be successful but adding them for good measure is still a good idea.  Compare these values to what you’re passing in your code and see what is different.  If you’re unable to identify the problem, open a support case for further assistance. You also see that the status code for a successful call is 201 Created.  You won’t use this in your call, but you can use that to test for a successful call in your code.

### Next Steps

Armed with these tools, you should be able to implement things otherwise not possible like implementing a data retention policy where every month you check for messages that fit some specific criteria (for instance if they are more than a year old) and purge them based on your security needs.

A “reply bot” could also be implemented that watches a group and automatically replies to new posts based on your own criteria.  If you have a customer support group and see people frequently posting without ticket numbers, you could have the reply bot check for that and reply if it doesn’t find a valid number letting them know what they’ve missed.

When designing these apps, part of the difficulty is identifying the correct endpoints to do what you need.  The only endpoints Yammer supports are listed at https://developer.yammer.com/docs/rest-api-rate-limits so I’ll look through that list to find what I need.  For example, if I wanted to leave a group, I’d look for group based endpoints. I see GET /messages/in_group/…, POST /group_memberships.json, DELETE /group_memberships.json, and GET /users/in_group/…  Based on those group endpoints, you could deduce if you want to delete your membership to a group, you’d use the DELETE /group_memberships.json endpoint.  Looking at its documentation at https://developer.yammer.com/docs/group_membershipsjsongroup_idid-1, you’ll see it leaves a group.

I’ll be writing more blog posts with ideas and examples of how to implement our APIs to perform specific tasks, so stay tuned for those as well.

## About the Author
**Alex Blaine** is a Support Escalation Engineer and code monkey on the Yammer Support Escalation team.