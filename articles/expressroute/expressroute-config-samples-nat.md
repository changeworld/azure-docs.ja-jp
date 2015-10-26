<properties
   pageTitle="ExpressRoute の顧客のルーター構成のサンプル |Microsoft Azure"
   description="このページでは、Cisco と Juniper のルーターのルーター構成のサンプルを示します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2015"
   ms.author="cherylmc"/>

# NAT をセットアップして管理するためのルーター構成のサンプル

このページでは、Cisco ASA と Juniper MX シリーズ ルーターの NAT 構成のサンプルを示します。これらはガイダンスとしてのみ使用することを目的としたサンプルであり、現状のまま使用することはできません。ベンダーと協力して、ネットワークに適した構成を考えてください。

>[AZURE.IMPORTANT]このページのサンプルは、ガイダンスとしてのみ使用することを目的としています。ベンダーの販売/技術チームおよび自社のネットワーク チームと協力して、ニーズに対応する適切な構成を考える必要があります。Microsoft では、このページに示す構成に関連する問題には対応できません。サポートの問題については、デバイス ベンダーに問い合わせる必要があります。

次のルーター構成のサンプルは、Azure パブリック ピアリングと Microsoft ピアリングに適用されます。Azure プライベート ピアリングの NAT は構成しないでください。詳細については、「[ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」および「[ExpressRoute NAT の要件](expressroute-nat.md)」をご覧ください。

**注:** インターネットと ExpressRoute への接続には個別の NAT IP プールを使用する必要があります。インターネットと ExpressRoute で同じ NAT IP プールを使用すると、非対称ルーティングになり、接続が失われます。

## Cisco ASA ファイアウォール

### 顧客ネットワークから Microsoft へのトラフィックのための PAT 構成

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range



## Juniper MX シリーズ ルーター 

### 1\.クラスターの冗長イーサネット インターフェイスの作成

	interfaces {
	    reth0 {
	        description "To Internal Network";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 1;
	        }
	        unit 100 {
	            vlan-id 100;
	            family inet {
	                address <IP_Address/Subnet_mask>;
	            }
	        }
	    }
	    reth1 {
	        description "To Microsoft via Edge Router";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 2;
	        }
	        unit 100 {
	            description "To Microsoft via Edge Router";
	            vlan-id 100;
	            family inet {
	                address <IP_Address/Subnet_mask>;
	            }
	        }
	    }
	}


### 2\.2 つのセキュリティ ゾーンの作成

 - 内部ネットワークには信頼ゾーンを作成し、エッジ ルーターに接続する外部ネットワークには非信頼ゾーンを作成します。
 - 適切なインターフェイスをゾーンに割り当てます。
 - インターフェイス上のサービスを許可します。


	security { zones { security-zone Trust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth0.100; } } security-zone Untrust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth1.100; } } } }


### 3\.ゾーン間のセキュリティ ポリシーの作成
 
	security {
	    policies {
	        from-zone Trust to-zone Untrust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	        from-zone Untrust to-zone Trust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	    }
	}


### 4\.NAT ポリシーの構成
 - 2 つの NAT プールを作成します。1 つは Microsoft に送信される NAT トラフィックに使用され、もう 1 つは Microsoft から顧客への NAT トラフィックに使用されます。
 - それぞれのトラフィックの NAT 規則の作成

		security {
		    nat {
		        source {
		            pool SNAT_To_ExpressRoute {
		                routing-instance {
		                    External_ExpressRoute;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            pool SNAT_From_ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External_ExpressRoute;
		                rule SNAT_Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_To_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound_NAT {
		                from routing-instance External_ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT_In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_From_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		        }
		    }
		}


### 5\.各方向でプレフィックスを選択してアドバタイズする BGP の構成

[ルーティング構成のサンプル](expressroute-config-samples-routing.md)に関するページのサンプルをご覧ください。

### 6\.ポリシーの作成

	routing-options {
	    	      autonomous-system <Customer_ASN>;
	}
	policy-options {
	    prefix-list Microsoft_Prefixes {
	        <IP_Address/Subnet_Mask;
	        <IP_Address/Subnet_Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise_NAT_Pools {
	        from {
	            protocol static;
	            route-filter <NAT_Pool_Address/Subnet_mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept_from_Microsoft {
	        term 1 {
	            from {
	                instance External_ExpressRoute;
	                prefix-list-filter Microsoft_Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept_from_Internal {
	        term no-private {
	            from {
	                instance Internal;
	                prefix-list-filter private-ranges orlonger;
	            }
	            then reject;
	        }
	        term bgp {
	            from {
	                instance Internal;
	                protocol bgp;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	}
	routing-instances {
	    Internal {
	        instance-type virtual-router;
	        interface reth0.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_ASN_1>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	    External_ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Internal;
	        }
	        protocols {
	            bgp {
	                group edge_router {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_Public_ASN>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	}

## 次のステップ

詳細については、[ExpressRoute の FAQ](expressroute-faqs.md) を参照してください。

<!---HONumber=Oct15_HO3-->