---
title: Join External Networks on Yammer
subtitle: By Inbar Cizer Kobrinsky
date: 2016-07-21
tags: ["External"]
---

As a Verified Admin of a canonical Yammer network, you can decide if users in your network can join other external networks. For example, as an admin you can approve your user’s requests to join external networks.

![ ](/img/2016-07-21 - Join External Networks on Yammer/External_Network_Settings.png)

In this post we will describe the approval process for a user who wants to join an external network of another organization, when user's home network admin has set above mentioned setting to require the admin’s approval.
The user Gal from the Contoso network, wants to join Fabrikam External network – They request an invite.

![ ](/img/2016-07-21 - Join External Networks on Yammer/Request_to_join_external_network.png)

The request is sent to the external network’s verified admin. Under the cog menu, the admin of “Fabrikan External” can see “Pending requests”. The Pending request page will include Gal’s request to join the external network.

![ ](/img/2016-07-21 - Join External Networks on Yammer/Pending_Requests.png)

![ ](/img/2016-07-21 - Join External Networks on Yammer/Requests.png)

When the admin of the external network approves this request, a pending account on the network is created for the user.

> More information about how to understand the different user state and read the export report can be found here: https://blogs.technet.microsoft.com/askyammer/2016/04/28/interpreting-a-yammer-user-export/

In the next step, the user (Gal), will need to request approval from the admin of his home network. The user needs to login to his home network. Under the menu they will see “Pending Network Invites”.

![ ](/img/2016-07-21 - Join External Networks on Yammer/Pending_Network_Invites.png)

![ ](/img/2016-07-21 - Join External Networks on Yammer/Invitations.png)

Once the user requests an approval, the request is sent to the admin of their home network (Contoso.com).

![ ](/img/2016-07-21 - Join External Networks on Yammer/Pending_Requests2.png) 

![ ](/img/2016-07-21 - Join External Networks on Yammer/Approve_Requests.png)

Once the request is approved, the user’s account on the external network will become active.

## About the author
**Inbar Cizer Kobrinsky** is a Support Escalation Engineer on the Yammer Support Escalation team. 

{{<mermaid>}}
  graph LR
      You --- Me
      Me-->C[Boop]
      Me-->D(fa:fa-spinner);
