---
title: Authorizing the Yammer JS SDK
subtitle: By Alex Blaine
date: 2016-11-04
tags: ["SDK", "JavaScript"]
---

Under normal circumstances, users will have to create a Yammer session before using the JS SDK in scenarios like apps integrated on intranet pages.  If you’re able to implement both server and client side scripting and pull the current user’s email address, you can use this guide to preauthorize a Yammer session so apps utilizing the JS SDK will be as seamless as possible.

### Prerequisites and Assumptions

You have created an app in Yammer and properly configured the JavaScript Origins
You have an app utilizing the Yammer JS SDK with users manually authenticating as per our documentation at https://developer.yammer.com/docs/js-sdk
You can utilize both client and server side scripting. The language and method of implementing the server-side scripting component is entirely dependent on your specific environment and configuration.
You can obtain the current user’s email address from the server’s side
 

### Procedures

Obtain a Verified Admin token for your application in one of the following 2 ways
Create the app with a Verified Admin account and then in the app’s Basic Info page, click “Generate a developer token for this application.” Note that you’ll need to use this app’s info in the JS SDK and any subsequent calls.
Use the process outlined at https://developer.yammer.com/docs/test-token with a Verified Admin account to get an OAuth token for that VA account. Note that you must use the app info used to generate this token in all future steps.
Obtain the current user’s email address in the server-side script.
Using the VA token obtained in step 1 to authenticate, pass the user’s email address to our Get User by Email Address endpoint documented at https://developer.yammer.com/docs/usersby_emailjsonemailuserdomaincom, and then process the response
If the call to the API endpoint returns a 200 OK response, first check the “state” field to make sure the user is “active” and if so, store the “id” field that’s returned and go to step 4
If the call returns a 404 or a state other than “active,” direct the user to finish creating and activating their account however you like.
Once you have the user’s ID, you can pass it to our Impersonation endpoint to obtain a pre-authorized OAuth token for that user. This endpoint is documented at https://developer.yammer.com/docs/impersonation and must use the VA token obtained in step 1 to authorize the call, and the consumer_key of your JS SDK app.
You now have an OAuth token for the current user. When generating the code being passed to the browser, have the client side JS SDK code first call yam.platform.getLoginStatus and if there’s no active session and you have a token from step 4, pass that token to yam.platform.setAuthToken($tokenFromStep4, optional_callback_function_if_desired(response)).  If you don’t have a valid token, direct the user to finish setting up their Yammer account.
Continue making JS SDK calls as you normally would, without needing the user to authenticate.
 

### Pseudocode

> Note: This code should live on the page where you’re hosting the Yammer JS SDK app.  This code should be rewritten in your server side scripting language and curl library’s format.  This code should go right before your JS SDK Code.

 
````JavaScript
<% // Start server side code
/* You’ll first need to get a Verified Admin OAuth token for your app.  I’m storing it in $theToken here */

/*
You will also need a way to obtain the email address of the current user.  This will be different depending
on where you’re hosting the Yammer app, but the most common way is to query the environment hosting the app
to get the logged on user’s email address.  I’m storing it in $theUserEmail here
*/

 

$theToken = “123-AoieuarKhwarIalIHY39A”;        // Store VA Token

$theUserEmail = getAuthenticatedUserEmail();    // Store user’s email

 

/* Get User ID */

$curl = newCurlObject();                        // This will be specific to your language and curl implementation

$curl.setHeader(“Authorization: Bearer “ + $theToken); // Sets the auth header

$theEndpoint = “https://www.yammer.com/api/v1/users/by_email.json?email=” + $theUserEmail;

$response = $curl.execute($theEndpoint);        // Make a call to the API endpoint with the email address appended

$theCode = $curl.getStatusCode();               // Get the response code from the API call

if($theCode == 404)

  $goodUser = false;                              // This user can’t be impersonated

 

$theUserData = json_decode($response);          // Get the json data into an array

if($theUserData["state"] == "active") {

  $goodUser = true;                               // This is an active user

  $theUserID = $theUserData["id"];                // Store the user’s ID

} else {

  $goodUser = false;                              // This user isn’t active and can’t be impersonated

}

/* We now know if the user is an active Yammer user and if so, their User ID */


/* Get impersonated OAuth token for the user */

$clientId = "your_client_id";                   // Obtained from the Basic Info section of your app under Client ID

$curl = newCurlObject();                        // This will be specific to your language and curl implementation

$curl.setHeader("Authorization: Bearer " + $theToken);   // Sets the auth header

$theEndpoint = "https://www.yammer.com/api/v1/oauth/tokens.json?user_id=" + $theUserID + "&consumer_key=" + $clientId;

$response = $curl.execute($theEndpoint);        // Make a call to the Impersonation API endpoint with the user id

$theCode = $curl.getStatusCode();               // Get the response code from the API call

if($theCode == 200) {

  $theTokenData = json_decode($response);         // Get the json data into an array

  $theUserToken = $theTokenData["token"];         // Store the impersonated token

}

 

/*
At this point you have the info you need from the server side scripting and you need to inject it
into the code sent to the browser.  Ending server side code
*/
%>
 

<script type="text/javascript" data-app-id="YOUR-APP-CLIENT-ID" src="https://c64.assets-yammer.com/assets/platform_js_sdk.js"></script>
<script>

yam.platform.getLoginStatus(function (response) {

  if (response.authResponse) {

    // Do nothing, the user is already authenticated

  } else {

    <% // Back to server side code

    if($goodUser) {

      print "yam.platform.setAuthToken(" + $theUserToken + ")"; // Set the token we obtained

      // Continue with the rest of your JS SDK calls in your app

    } else {

      print "Please <a href='yammer.com'>activate your Yammer account</a> to use this app";

    }

  // End server side code
  %>

  }

});
````

### More information

* [Yammer JS SDK documentation] (https://developer.yammer.com/docs/js-sdk)
* [Find user by email API documentation] (https://developer.yammer.com/docs/usersby_emailjsonemailuserdomaincom)
* [Impersonation API documentation] (https://developer.yammer.com/docs/impersonation)


## About the Author
**Alex Blaine** is a Support Escalation Engineer and code monkey on the Yammer Support Escalation team.