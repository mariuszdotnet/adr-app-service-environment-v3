# Custom Domain Suffix

## Prologue (Summary)

The following ADR review the use of [Custom Domain Suffix](https://learn.microsoft.com/en-us/azure/app-service/environment/how-to-custom-domain-suffix?pivots=experience-azp). The DNS settings for your App Service Environment's default domain suffix don't restrict your apps to only being accessible by those names. Custom domain suffix is an internal load balancer (ILB) App Service Environment feature that allows you to use your own domain suffix to access the apps in your App Service Environment.

## Discussion (Context)

The custom domain suffix defines a root domain that can be used by the App Service Environment. For ILB App Service Environments, the default root domain is **appserviceenvironment.net**. However, since an ILB App Service Environment is internal to a customer's virtual network, customers can use a root domain in addition to the default one that makes sense for use within a company's internal virtual network.

For example, a hypothetical Kaitek Corporation might use a default root domain of internal.kaitek.com for apps that are intended to only be resolvable and accessible within Kaitek's virtual network. An app in this virtual network could be reached by accessing APP-NAME.internal.kaitek.com.

## Solution (Decision)

  1. Only supported on the ILB variation of App Service Environment v3.

  2. An [Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/) is required for the custom domain SSL certificate.  The Azure Key Vault that has the certificate must be publicly accessible to fetch the certificate.

  3. Valid SSL/TLS certificate must be stored in an Azure Key Vault in .PFX format.

  4. A [system assigned or user assigned identity](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview) is used to authenticate against the Azure Key Vault where the SSL/TLS certificate is stored. If you don't currently have a managed identity associated with your App Service Environment, you'll need to configure one.

  5. Details on DNS requirements can be found [here](https://learn.microsoft.com/en-us/azure/app-service/environment/how-to-custom-domain-suffix?pivots=experience-azp#dns-configuration).

  6. For official requirements and details of the feature see the [official documentation](https://learn.microsoft.com/en-us/azure/app-service/environment/how-to-custom-domain-suffix?pivots=experience-azp).

## Consequences (Results)

  1. Since an ILB App Service Environment is internal to a customer's virtual network, customers can use a root domain in addition to the default one that makes sense for use within a company's internal virtual network.

  2. This feature acts as [*white labeling*](https://en.wikipedia.org/wiki/White-label_product) of the DNS for the platform and easy management of DNS for the apps deployed.

  3. This feature is different from a custom domain binding on an App Service. For more information on custom domain bindings, see [Map an existing custom DNS name to Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-domain?tabs=root%2Cazurecli).

## Observations (Suggestions)

- This feature is great for internal Line of Business applications that will not use the **custom domain bindings**, as it provides a *white labelled* DNS at scale minimizing operational overhead.

- If you'll be using **custom domain bindings** for your apps the management overhead (SSL, DNS etc.) and complexity do not justify the use of **Custom Domain Suffix**.
