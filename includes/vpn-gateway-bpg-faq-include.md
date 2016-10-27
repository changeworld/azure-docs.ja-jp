### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus?"></a>Is BGP supported on all Azure VPN Gateway SKUs?

No, BGP is supported on Azure **Standard** and **HighPerformance** VPN gateways. **Basic** SKU is NOT supported.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways?"></a>Can I use BGP with Azure Policy-Based VPN gateways?

No, BGP is supported on Route-Based VPN gateways only.

### <a name="can-i-use-private-asns-(autonomous-system-numbers)?"></a>Can I use private ASNs (Autonomous System Numbers)?

Yes, you can use your own public ASNs or private ASNs for both your on-premises networks and Azure virtual networks.

#### <a name="are-there-asns-reserved-by-azure?"></a>Are there ASNs reserved by Azure?

Yes, the following ASNs are reserved by Azure for both internal and external peerings:

- Public ASNs: 8075, 8076, 12076
- Private ASNs: 65515, 65517, 65518, 65519, 65520

You cannot specify these ASNs for your on premises VPN devices when connecting to Azure VPN gateways.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets?"></a>Can I use the same ASN for both on-premises VPN networks and Azure VNets?

No, you must assign different ASNs between your on-premises networks and your Azure VNets if you are connecting them together with BGP. Azure VPN Gateways have a default ASN of 65515 assigned, whether BGP is enabled for not for your cross-premises connectivity. You can override this default by assigning a different ASN when creating the VPN gateway, or change the ASN after the gateway is created. You will need to assign your on-premises ASNs to the corresponding Azure Local Network Gateways.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me?"></a>What address prefixes will Azure VPN gateways advertise to me?

Azure VPN gateway will advertise the following routes to your on-premises BGP devices:

- Your VNet address prefixes
- Address prefixes for each Local Network Gateways connected to the Azure VPN gateway
- Routes learned from other BGP peering sessions connected to the Azure VPN gateway, **except default route or routes overlapped with any VNet prefix**.

#### <a name="can-i-advertise-default-route-(0.0.0.0/0)-to-azure-vpn-gateways?"></a>Can I advertise default route (0.0.0.0/0) to Azure VPN gateways?

Not at this time.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes?"></a>Can I advertise the exact prefixes as my Virtual Network prefixes?

No, advertising the same prefixes as any one of your Virtual Network address prefixes will be blocked or filtered by the Azure platform. However you can advertise a prefix that is a superset of what you have inside your Virtual Network. For example, your Virtual Network could use the address space 10.10.0.0/16 and you could advertise 10.0.0.0/8.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections?"></a>Can I use BGP with my VNet-to-VNet connections?

Yes, you can use BGP for both cross-premises connections and VNet-to-VNet connections.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways?"></a>Can I mix BGP with non-BGP connections for my Azure VPN gateways?

Yes, you can mix both BGP and non-BGP connections for the same Azure VPN gateway.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing?"></a>Does Azure VPN gateway support BGP transit routing?

Yes, BGP transit routing is supported, with the exception that Azure VPN gateways will **NOT** advertise default routes to other BGP peers. To enable transit routing across multiple Azure VPN gateways, you must enable BGP on all intermediate VNet-to-VNet connections.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network?"></a>Can I have more than one tunnels between Azure VPN gateway and my on-premises network?

Yes, you can establish more than one S2S VPN tunnels between an Azure VPN gateway and your on-premises network. Please note that all these tunnels will be counted against the total number of tunnels for your Azure VPN gateways. For example, if you have two redundant tunnels between your Azure VPN gateway and one of your on-premises network, they will consume 2 tunnels out of the total quota for your Azure VPN gateway (10 for Standard and 30 for HighPerformance).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp?"></a>Can I have multiple tunnels between two Azure VNets with BGP?

No, redundant tunnels between a pair of virtual networks are not supported.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroute/s2s-vpn-co-existence-configuration?"></a>Can I use BGP for S2S VPN in an ExpressRoute/S2S VPN co-existence configuration?

Not at this time.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip?"></a>What address does Azure VPN gateway use for BGP Peer IP?

The Azure VPN gateway will allocate a single IP address from the GatewaySubnet range defined for the virtual network. By default, it is the second last address of the range. For example, if your GatewaySubnet is 10.12.255.0/27, ranging from 10.12.255.0 to 10.12.255.31, then the BGP Peer IP address on the Azure VPN gateway will be 10.12.255.30. You can find this information when you list the Azure VPN gateway information.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device?"></a>What are the requirements for the BGP Peer IP addresses on my VPN device?

Your on-premises BGP peer address **MUST NOT** be the same as the public IP address of your VPN device. Use a different IP address on the VPN device for your BGP Peer IP. It can be an address assigned to the loopback interface on the device. Specify this address in the corresponding Local Network Gateway representing the location.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp?"></a>What should I specify as my address prefixes for the Local Network Gateway when I use BGP?

Azure Local Network Gateway specifies the initial address prefixes for the on-premises network. With BGP, you must allocate the host prefix (/32 prefix) of your BGP Peer IP address as the address space for that on-premises network. If your BGP Peer IP is 10.52.255.254, you should specify "10.52.255.254/32" as the localNetworkAddressSpace of the Local Network Gateway representing this on-premises network. This is to ensure that the Azure VPN gateway establishes the BGP session through the S2S VPN tunnel.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session?"></a>What should I add to my on-premises VPN device for the BGP peering session?

You should add a host route of the Azure BGP Peer IP address on your VPN device pointing to the IPsec S2S VPN tunnel. For example, if the Azure VPN Peer IP is "10.12.255.30", you should add a host route for "10.12.255.30" with a nexthop interface of the matching IPsec tunnel interface on your VPN device.


<!--HONumber=Oct16_HO2-->


