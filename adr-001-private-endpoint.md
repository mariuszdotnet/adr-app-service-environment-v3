# Private Endpoint

## Prologue (Summary)

The following ADR reviews the use of [private endpoint](https://learn.microsoft.com/en-us/azure/app-service/environment/networking#private-endpoint) with the ASEv3 deployed with the Internal Load Balancer also know as ASEv3 ILB or Internal ASEv3.

## Discussion (Context)

If your ASEv3 ILB is deployed with an internal virtual IP (VIP) address, the inbound address for all the apps will be an address in the App Service Environment subnet. The ingress point for all the apps will be the ILB virtual IP (VIP).  Therefore, the ASEv3 ILB is by default design for internal line-of-business applications providing network isolation at the ASEv3 subnet.  In addition to the ILB ingress the ASEv3 allows a [private endpoint](https://learn.microsoft.com/en-us/azure/app-service/overview-private-endpoint) to be configured at an individual app level.

## Solution (Decision)

  1. Enable [private endpoint](https://learn.microsoft.com/en-us/azure/app-service/overview-private-endpoint#app-service-environment-v3-special-consideration) for ASEv3 resource.
  2. Add a private endpoint to the app hosted on the ILB ASEv3.

## Consequences (Results)

  1. The addition of a private endpoint at an app level enables a dedicated ingress point, a private virtual IP (VIP) sourced from a different subnet then the ASEv3 subnet.

  2. The ingress provided by the ILB is blocked and and private endpoint VIP becomes the only ingress point for all traffic to the app including administrative operations like deployment and the kudu console.  The blocking of the ILB ingress is done by configuring the **Access Restrictions** to **Disabled** in **Direct network access** section.

  3. A Private dedicated DNS Zone has to be created for the private endpoint as per [official documentation](https://learn.microsoft.com/en-us/azure/app-service/overview-private-endpoint#dns).

  4. For every private endpoint a CNAME record has to be added to the ASEv3 Private DNS Zone in the form of **appname.privatelink.asename.appserviceenvironment.net**.

  5. NSG rules can be applied to the subnet where the private endpoint (PEP)is sourced to block inbound access aka North/South traffic (ingress only from outside of the ASEv3).  The PEP can not be used for *"micro-segmentation"*, the blocking of East/West traffic within the ASEv3 subnet between the apps hosted on the ASEv3.  This is due to the dynamic nature of the IP assignment to the apps by ASEv3.  Application Security Groups also do not work in this scenario for the same reason.

  6. The **Access Restrictions** in **Direct network access** sections of the **Network Settings** only apply to the ILB of the ASEv3 and do not apply to the private endpoints.

  7. The use of private endpoints also incurs additional [ingress charges](https://azure.microsoft.com/en-us/pricing/details/private-link/) which are not present with the use of the ILB with ASEv3.

  8. Custom domains assigned to apps with private endpoints on ILB ASEv3 do not need (bypass) the [external domain verification](https://learn.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-domain?tabs=root%2Cazurecli#3-validate-and-complete).

## Observations (Suggestions)

- The use of **private endpoints** has no clear benefits over the **ILB** provided with an **Internal ASEv3** deployment.

- North/South traffic, specifically ingress can be managed with NSG at the ASEv3 subnet level.

- East/West traffic can not be controlled in both scenarios (PEP/ILB) due to the dynamic nature of IP assignment to the apps hosted in the App Service Plan on the ASEv3.

- PEP provide a unique ingress end-point per application.

- PEP could be usefull in scenarios where exposing a Web/Function App to a network which is not peered to the network the ASEv3 ILB is deployed on.
