<BR> 
## <a name="faq"></a>FAQ 
### <a name="how-much-do-reverse-dns-records-cost?"></a>How much do reverse DNS records cost?
They’re free!  There is no additional cost for reverse DNS records or queries.
### <a name="will-my-reverse-dns-records-resolve-from-the-internet?"></a>Will my reverse DNS records resolve from the internet?
Yes. Once you set the reverse DNS property for your Public IP Address, Azure manages all the DNS delegations and DNS zones required to ensure that reverse DNS record resolves for all internet users.
### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses?"></a>Will a default reverse DNS record be created for my Public IP Addresses?
No. Reverse DNS is an opt-in feature. No default reverse DNS records are created if you choose not to configure them.
### <a name="what-is-the-format-for-the-fully-qualified-domain-name-(fqdn)?"></a>What is the format for the fully-qualified domain name (FQDN)?
FQDNs are specified in forward order, and must be terminated by a dot (e.g., “app1.contoso.com.”).
### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-i’ve-specified-fail?"></a>What happens if the validation checks for the reverse DNS I’ve specified fail?
Where the validation for reverse DNS checks fail, the service management operation will fail. Please correct the reverse DNS value as required, and retry.
### <a name="can-i-manage-reverse-dns-for-my-azure-website?"></a>Can I manage reverse DNS for my Azure Website?
Reverse DNS is not supported for Azure Websites. Reverse DNS is supported for Azure Virtual Machines.
### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address?"></a>Can I configure multiple reverse DNS records for my Public IP Address?
No. Azure supports a single reverse DNS record for each Public IP Address. Each Public IP Address however can have their own reverse DNS record.
### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified?"></a>Can I configure a reverse DNS record for my Public IP Address without having a DomainNameLabel specified?
No. To leverage reverse DNS records for your Public IP Addresses, you must specify the DomainNameLabel property.
### <a name="can-i-host-the-arpa-zones-for-my-azure-assigned-ips-on-azure-dns-within-my-own-subscription,-or-on-my-own-authoritative-dns-servers?"></a>Can I host the ARPA zones for my Azure-assigned IPs on Azure DNS within my own subscription, or on my own authoritative DNS servers?
No. Azure does not support the onward delegation of ARPA zones. Azure hosts the ARPA zones for all available IPs, and enables customers to create reverse DNS records within these ARPA zones.
### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns?"></a>Can I host ARPA zones for my ISP-assigned IP blocks on Azure DNS?
No. Azure DNS does not currently support reverse DNS records in customers DNS zones.
### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services?"></a>Can I send emails to external domains from my Azure Compute services?
No. As per [here](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/), Azure Compute services do not support sending emails to external domains.


<!--HONumber=Oct16_HO2-->


