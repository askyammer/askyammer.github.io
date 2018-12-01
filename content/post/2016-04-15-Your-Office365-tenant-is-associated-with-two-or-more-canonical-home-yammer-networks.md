---
title: Your Office 365 tenant is associated with two or more canonical (home) Yammer networks
subtitle: By Inbar Cizer Kobrinsky
date: 2016-04-15
tags: ["Tenant", "Migration"]
---

Have you recently received an email from Yammer stating that your Office 365 tenant is associated with two or more Yammer networks? If you are wondering what that means and what you can do – we are here to help.

A Yammer network can be associated with an Office 365 tenant in several ways:

* One Office 365 tenant associated with one Yammer network (1 tenant: 1 network)
* One Office 365 tenant associated with many Yammer networks (1 tenant: many networks)
* Many Office 365 tenants associated with one Yammer network (many tenants: 1 network)

This document explains in more detail the implications of each of these configurations. For more information on moving from a 1 tenant: many networks configuration to a 1 tenant :1 network state, please see our documentation located here: [1 Tenant : Many Networks Documentation](https://support.office.com/en-us/article/About-Yammer-networks-and-Office-365-tenants-85516b0c-79ad-4483-8534-4477c8d26e1a?ui=en-US&rs=en-US&ad=US)

We will use the following example to explain the configuration:

The Office 365 tenant has 4 domains:

* Contoso.onmicrosoft.com
* Contoso.com
* TailSpin.com
* Fabrikam.com

Tied to this tenant are two Yammer networks and a domain not associated with a Yammer network. The networks can be either enterprise or free.

* Network 1 has contoso.com and contoso.onmicrosoft.com.
* Network 2 has TailSpin.com.
* Fabrikam.com was not added to any of these networks.

### Starting configuration

![Starting Configuration](/img/2016-04-15 - Network Migration/Starting_Configuration.png)
<center>_A Tenant that has more than one Yammer networks_</center>

From our experience, many times customers are not aware of additional networks or these networks are not in use. If you don’t want to keep the networks separated – you can proceed with the steps described below.

### Using the Network Migration portal to move to a 1:1 configuration

Merging your networks is a relatively easy task. As first step, you would need to understand which networks are associated with your tenant. You can use the Network Migration portal built into Yammer to identify other networks tied to your tenant and merge them into your primary Yammer network. For more information on this, please see https://aka.ms/YamNetMig for our documentation on this feature.

In this case, you will login in to network 1 and go to the network migration page.

The first step of the wizard will list all the domains associated with the network and the domains that are verified on the Office 365 tenant but not added to the Yammer network. Some of these domains can be associated with other Yammer networks and some can be domains that were not added to any Yammer network. In our case, TailSpin.com is a Yammer network and Fabrikam.com is not associated with any Yammer network.

![Network Migration step 1](/img/2016-04-15 - Network Migration/Network_Migration_1.jpg)
<center>_Network Migration Step 1_</center>

The second step of the wizard will tell which of these domains is associated with a network and can be merged. In this case, only TailSpin.com can be merged.

![Network Migration step 2](/img/2016-04-15 - Network Migration/Network_Migration_2.jpg)
<center>_Network Migration Step 2_</center>

Before you continue with the merge – If you would like to understand what content and users are in this network - you can sign up to the network with email address of the domain associated with the network. If you would like to export the data – contact support. Once you are ready to merge – proceed to step 3. Repeat this step for every network you would like to merge.

Once you merged all the networks into one network – your configuration looks as follows:

![Network Merged](/img/2016-04-15 - Network Migration/Network_Merged.png)
<center>_Network Migrated_</center>

Once you merged all the networks available, you can trigger a synchronization of any additional domain from Office 365 to Yammer. In our case, Fabrikam.com is not linked to any network. The next time a domain change will happen in Office 365 (Domain is added or removed or default domain is set) – it will trigger a synchronization of the domains to Yammer. At this point fabrikam.com will be added to your Yammer network.

### Relevant Documentation
[1 Tenant : Many Networks Documentation](https://support.office.com/en-us/article/About-Yammer-networks-and-Office-365-tenants-85516b0c-79ad-4483-8534-4477c8d26e1a?ui=en-US&rs=en-US&ad=US)

<br>
## About the Author
**Inbar Cizer Kobrinsky** is a Support Escalation Engineer on the Yammer Support Escalation team.

