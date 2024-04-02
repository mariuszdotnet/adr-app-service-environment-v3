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
  2. The ingress provided by the ILB is block and and private endpoint VIP becomes the only ingress point for all traffic to the app including administrative operations like deployment and the kudu console.
  3. A Private dedicated DNS Zone has to be created for the private endpoint
  4. For every private endpoint a CNAME record has to be added to the ASEv3 Private DNS Zone in the form of "appname.privatelink.asename.appserviceenvironment.net".

