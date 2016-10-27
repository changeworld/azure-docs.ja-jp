<BR> 
## <a name="faq"></a>FAQ 
### <a name="how-much-do-reverse-dns-records-cost?"></a>How much do reverse DNS records cost?
They’re free!  There is no additional cost for reverse DNS records or queries.
### <a name="will-my-reverse-dns-records-resolve-from-the-internet?"></a>Will my reverse DNS records resolve from the internet?
Yes. Once you set the reverse DNS property for your Cloud Service, Azure manages all the DNS delegations and DNS zones required to ensure that reverse DNS record resolves for all internet users.
### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services?"></a>Will a default reverse DNS record be created for my Cloud Services?
No. Reverse DNS is an opt-in feature. No default reverse DNS records are created if you choose not to configure them.
### <a name="what-is-the-format-for-the-fully-qualified-domain-name-(fqdn)?"></a>What is the format for the fully-qualified domain name (FQDN)?
FQDNs are specified in forward order, and must be terminated by a dot (e.g., “app1.contoso.com.”).
### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-i’ve-specified-fail?"></a>What happens if the validation checks for the reverse DNS I’ve specified fail?
Where the validation for reverse DNS checks fail, the service management operation will fail. Please correct the reverse DNS value as required, and retry.
### <a name="can-i-manage-reverse-dns-for-my-azure-website?"></a>Can I manage reverse DNS for my Azure Website?
Reverse DNS is not supported for Azure Websites. Reverse DNS is supported for Azure PaaS roles and IaaS virtual machines.
### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service?"></a>Can I configure multiple reverse DNS records for my Cloud Service?
No. Azure supports a single reverse DNS record for each Azure Cloud Service. Each Azure Cloud Service however can have their own reverse DNS record.
### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services?"></a>Can I send emails to external domains from my Azure Compute services?
No. As per [here](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/), Azure Compute services do not support sending emails to external domains.


<!--HONumber=Oct16_HO2-->


