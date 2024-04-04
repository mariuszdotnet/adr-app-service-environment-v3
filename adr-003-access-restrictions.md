# Access Restrictions

## Prologue (Summary)

The following ADR reviews the use of [Access Restrictions](https://learn.microsoft.com/en-us/azure/app-service/overview-access-restrictions) with the ASEv3 deployed with the Internal Load Balancer also know as ASEv3 ILB or Internal ASEv3.

## Discussion (Context)

Access restrictions in App Service are equivalent to a **firewall** allowing you to block and filter traffic. Access restrictions apply to **inbound access only**. Most App Service pricing tiers also have the ability to add private endpoints to the app, which is another entry point to the app. **Access restrictions don't apply to traffic entering through a private endpoint**. For all apps hosted on App Service, the default entry point is publicly available. **The only exception is apps hosted in ILB App Service Environment where the default entry point is internal to the virtual network**.

## Solution (Decision)

  1. **Access Restrictions** are enable at an individual app level.
  2. For details of the **access restrictions** functionality see the [official documentation](https://learn.microsoft.com/en-us/azure/app-service/overview-access-restrictions#how-it-works).
  3. For details on setting up **access restrictions** see the [official documentation](https://learn.microsoft.com/en-us/azure/app-service/app-service-ip-restrictions?tabs=azurecli).

## Consequences (Results)

  1. Access restrictions **do not** apply to traffic entering through a **private endpoint**
  2. When a **private endpoint** is configured, by default the service enables **Deny All**  on **access restrictions** making the private endpoint the only ingress for the app. In other words the ILB ingress is blocked by **access restrictions** for all traffic and the private endpoint becomes the only ingress.

## Observations (Suggestions)

- Managing ingress for apps hosted on an ILB ASEv3 can be achieved in one of two ways.
  - At an app level with **access restrictions** enabled.
  - At an app level with **Network Security Groups** (NSGs) applied to the ASEv3 subnet. See [ports and network restrictions](https://learn.microsoft.com/en-us/azure/app-service/environment/networking#ports-and-network-restrictions).

- The ingress is meant to control **North/South** traffic to the platform, it is not intended not is it possible to control **East/West** traffic, sometimes referred to as **"micro-segmentation"**.

- It is not possible to control the ingress of **East/West** traffic between the apps on ASEv3 due to the dynamic nature of IP allocation to the backend in an App Service Plan and management operations such as auto-scaling and auto-healing.
