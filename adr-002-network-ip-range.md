# Network IP Range

## Prologue (Summary)

The following ADR reviews the [networking requirements](https://learn.microsoft.com/en-us/azure/app-service/environment/networking) from a subnet perspective with the ASEv3 deployed with the Internal Load Balancer also know as ASEv3 ILB or Internal ASEv3.

## Discussion (Context)

The **subnet** size which you assign to your ASEv3 deployment will have direct implications to the number of apps you'll be able to deploy into the ASEv3.  The subnet ip range is used for two main purposes, the apps deployed into the ASEv3 and the management operations at the data plane such as scaling and auto recover/healing. For completed details see the [official documentation](https://learn.microsoft.com/en-us/azure/app-service/environment/networking).

## Solution (Decision)

  1. You must delegate the subnet to **Microsoft.Web/hostingEnvironments**, and the subnet must be empty.

  2. The smallest possible subnet that is support by ASEv3 is a **/27 address space (32 addresses)**.  This is only recommended for PoC scenarios.

## Consequences (Results)

  1. The size of the subnet can affect the scaling limits of the App Service Plan instances within the ASEv3.  Therefore it has a upper limit on the number of instance an App Service Plan can have and the number of Apps that should be deployed onto it.

  2. If you run out of addresses within your subnet, you can be restricted from scaling out your App Service Plans in the ASEv3. Another possibility is that you can experience increased latency during intensive traffic load, if Microsoft isn't able to scale the supporting infrastructure.

## Observations (Suggestions)

- Drawing from extensive, practical experience with the ASEv3 platform and following the product group's density guidance, we've provided a table below. This table recommends CIDR block sizes and the total number of apps that should be hosted on one or more App Service Plans.

| **Size** | **CIDR Block Size** | **IPs** | **Usable IPs** | **Max Scale** | **Notes** |
| -------- | ------------------- | ---- | -------------- | ------------- | ----------|
| **Small** | /26 | 64 | 59 | 40 | |
| **Large** | /24 | 256 | 251 | 80 |  |
| **Medium** | /25 | 128 | 123 | 200 | recommended for usual production workloads |
| **Extra Large** | /23 | 512 | 507 | 200 | recommended for workloads with frequent up/down scale operations |

- Other restrictions apply for number of usable IPs.  Please see [official documentation](https://learn.microsoft.com/en-us/azure/app-service/environment/networking#subnet-requirements) for details.
- For details on App density on an App Service Plan see the [official guidance](https://learn.microsoft.com/en-us/azure/app-service/overview-hosting-plans#should-i-put-an-app-in-a-new-plan-or-an-existing-plan).
