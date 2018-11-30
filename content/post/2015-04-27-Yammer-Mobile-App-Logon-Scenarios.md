---
title: Yammer Mobile App Logon Scenarios
subtitle: By Alex Blaine
date: 2015-04-27
tags: ["Mobile", "Login"]
---

Yammer offers several different options when it comes to logging in so
you can choose the one that’s best for your organization.  As such,
there are many different login flows you may see depending on which
method is employed on your network.  Below, I have detailed each of
these flows so you can know what to expect for your configuration (note:
Screenshots taken on iOS).

### Office 365 sign-in for Yammer

If you're using Office 365 sign-in for Yammer (more info available
[here](/controlpanel/blogs/posteditor.aspx/1.%20Enter%20your%20email%20address%20in%20the%20first%20page%20and%20hit%20Log%20In%20\(password%20not%20required%20on%20this%20page\))),
you'll see the following flow:

1. Enter your email address in the first page and hit Log In (password
not required on this page)

![Mobile 1](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile1.png)

2. The app will redirect you to the Office 365 sign-in page.  Here, you
will enter your Office 365 credentials (both username and password).  If
your Office 365 tenant uses SSO, you'll be directed to that login page
instead.

![Mobile 2](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile2.png)

### Single Sign-On (SSO)

If your Yammer network is configured to use Single Sign-On with a third
party IdP or if you’re using ADFS without Windows Authentication, the
flow is as following (note, some niche cases may differ slightly on the
last step):

1. Enter your email address in the first page and hit Log In (password
not required on this page)

![Mobile 3](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile3.png)

2. The app will show a page indicating that it’s redirecting to your
IdP 

![Mobile 4](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile4.png)

3. You will be presented with a web page with forms prompting you for
your username and password.  This page’s appearance depends on the IdP
used or customizations applied.

### Single Sign-On with ADFS using Windows Authentication

If your Yammer network is configured to use Single Sign-On and you’re
using ADFS configured with Windows Authentication as your IdP, you’ll be
presented with a unique flow with a modal dialog box.  The flow proceeds
as follows:

1. Enter your email address in the first page and hit Log In (password
not required on this page)

![Mobile 5](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile5.png)

2. The app will show a page indicating that it’s redirecting to your
IdP

![Mobile 6](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile6.png)

3. You will be presented with a dialog box asking for your username and
password, which will then log you in

![Mobile 7](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile7.png)

### Yammer Authentication

Yammer Authentication is the basic form of authentication and is used
when none of the other options are present for the network or particular
user account logging in.  It consists of just your normal Email and
Password combination and can be entered at the login page as shown
below:

![Mobile 8](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile8.png)
### Temporary Password

Sometimes a login process fails due to specific IdP configurations.  If
this ends up being the case, we offer a Temporary Password system to
help you log in until the failure is resolved.  To obtain and use a
temporary password:

1. On a computer, log into Yammer
2. At the top right, click the ellipses (…) button and then the Apps
link in the menu that comes up
![Mobile 9](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile9.png)
3. Click on the Android, iPhone, or iPad logo in the list shown (not
the Learn More button)
![Mobile 10](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile10.png)
4. At the bottom left of the page that follows is listed your temporary
password along with your service account’s email address (and an
expiration time)
5. Use your email and this password log into the first screen of the
Yammer app 
![Mobile 11](/img/Yammer%20Mobile%20App%20Login%20Scenarios/Mobile11.png)

<br>
## About the writer
**Alex Blaine** is a Support Escalation Engineer and code monkey on the Yammer Support Escalation team.