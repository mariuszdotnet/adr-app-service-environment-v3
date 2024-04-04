# Availability Zones

## Prologue (Summary)

The following ADR reviews the use of the [Availability Zones](https://learn.microsoft.com/en-us/azure/reliability/reliability-app-service?tabs=graph%2Ccli#availability-zone-support) with the ASEv3 deployed with the Internal Load Balancer also know as ASEv3 ILB or Internal ASEv3.

## Discussion (Context)

Azure App Service can be deployed across [availability zones (AZ)](https://learn.microsoft.com/en-us/azure/reliability/availability-zones-overview?tabs=azure-cli) to help you achieve resiliency and reliability for your business-critical workloads. This architecture is also known as zone redundancy.

When you configure App Service as zone redundant, the platform automatically spreads the instances of the Azure App Service plan across three zones in the selected region.

Instance spreading with a zone-redundant deployment is determined inside the following rules, even as the app scales in and out:

The minimum App Service Plan instance count is three.
If you specify a capacity larger than three, and the number of instances is divisible by three, the instances are spread evenly.
Any instance counts beyond 3*N are spread across the remaining one or two zones.

Availability zone support is a property of the App Service plan. App Service plans can be created on managed multi-tenant environment or dedicated environment using App Service Environment v3.

## Solution (Decision)

  1. With the ILB ASEv3 the **availability zone support** is enabled at deployment time of the ASEv3.  
  2. AZs for ASEv3 are only available in regions that support [availability zones](https://learn.microsoft.com/en-us/azure/reliability/reliability-app-service?tabs=graph%2Ccli#availability-zone-support).
  3. For details on setting up **availability zones** see the [official documentation](https://learn.microsoft.com/en-us/azure/app-service/environment/creation).

## Consequences (Results)

  1. Due to the increased system need, the minimum charge for a zone redundant ASEv3 is **nine instances**. If you've fewer than this number of instances, the difference is charged as Windows I1v2. If you've nine or more instances, there's no added charge to have a zone redundant App Service Environment.

  2. Zone redundancy can only be enabled at deployment time, this can not be changed after.  
  
  3. All App Service Plans (ASP) and the corresponding apps deployed on the ASPs will benefit from the **zone redundancy**

  4. All supported plans for ASEv3 ILB deployments support **zone redundancy** as long as the region supports "availability zones."

## Observations (Suggestions)

- As long as you plan on deploying at least 9 instance across all ASPs on the ASEv3 it is beneficial to enable **zone redundancy** without any additional cost.

- If regional **High availability** is a requirement for your application workloads **zone redundancy** should be a default position even with the potential additional cost implications.

- At this time [April, 2024](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services?lang=1) there is are no additional financially backed SLAs for **zone redundancy** deployments for ASEv3. The default 99.95% SLA applies.
