---
title: Virtual WAN パートナー向けの Azure Virtual WAN の自動化の構成 |Microsoft Docs
description: この記事では、ソフトウェア定義の接続ソリューション パートナーが Azure Virtual WAN 自動化を設定できるようにします。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: a1ff4364e394b3807cf767722ee934ae024399b0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114346"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Virtual WAN パートナー向けの Virtual WAN の自動化の構成 (プレビュー)

この記事では、Azure Virtual WAN のブランチ デバイス (お客様のオンプレミスの VPN デバイスまたは SDWAN) を接続および構成する自動化環境を設定する方法を理解できます。 IPsec/IKEv2 経由で VPN 接続に対応できるブランチ デバイスを提供するプロバイダーの場合は、この記事が適しています。

通常、ソフトウェア定義の接続ソリューションでは、コントローラーまたはデバイス プロビジョニング センターを使用してブランチ デバイスを管理します。 コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの SDWAN または VPN デバイスが必要です。

##  <a name="access"></a>アクセス制御

お客様は、デバイスの UI で仮想 WAN の適切なアクセス制御を設定できる必要があります。 Azure サービス プリンシパルを使用することをお勧めします。 サービス プリンシパルに基づくアクセスは、ブランチ情報をアップロードするために適した認証をデバイス コント ローラーに提供します。

##  <a name="site"></a>ブランチの情報のアップロード

ブランチ (オンプレミス サイト) の情報を Azure にアップロードするユーザー エクスペリエンスをデザインします。 **VPNSite** の [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) を使用して、仮想 WAN でサイト情報を作成することができます。 すべてのブランチ SDWAN/VPN デバイスを提供したり、適切なデバイスのカスタマイズを選択したりすることができます。

##  <a name="hub"></a>ハブとサービス

ブランチ デバイスを Azure にアップロードすると、お客様は通常、Azure portal でハブのリージョンやサービスを選択します。これにより、ハブ仮想ネットワークおよびハブ内の VPN エンドポイントを作成するための一連の操作が開始されます。 VPN ゲートウェイは、帯域幅や接続ニーズに基づいて適切なサイズに設定されるスケーラブルなゲートウェイです。

## <a name="device"></a>デバイス構成

この手順で、プロバイダーを使用していないお客様が、手動で Azure の構成をダウンロードしてオンプレミス SDWAN/VPN デバイスに適用します。 プロバイダーは、この手順を自動化する必要があります。 コント ローラーは、**GetVpnConfiguration** REST API を呼び出して、Azure の構成をダウンロードすることができます。これは、通常は次のようなファイルになります。

**構成メモ**

  * Azure Vnet が仮想ハブに接続されている場合は、ConnectedSubnets として表示されます。
  * VPN 接続では、ルート ベースの構成と IKEv2 を使用します。

### <a name="understanding-the-device-configuration-file"></a>デバイス構成ファイルについて

デバイス構成ファイルには、オンプレミスの VPN デバイスを構成するときに使用する構成が含まれています。 このファイルを表示すると、次の情報を確認できます。

* **vpnSiteConfiguration -** このセクションは、仮想 WAN に接続するサイトとして設定されたデバイスの詳細を示します。 ブランチ デバイスの名前とパブリック IP アドレスが含まれています。
* **vpnSiteConnections -** このセクションには、次の情報が含まれています。

    * 仮想ハブ VNet の**アドレス空間**<br>例:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * ハブに接続されている VNet の**アドレス空間**<br>例:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * 仮想ハブ vpngateway の **IP アドレス**。 vpngateway にはアクティブ/アクティブ構成の 2 つのトンネルで構成される接続があるため、このファイルには両方の IP アドレスが示されています。 この例では、サイトごとに "Instance0" と "Instance1" が表示されています。<br>例:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP、事前共有キーなどの、**vpngateway 接続構成の詳細**。PSK は、自動的に生成される事前共有キーです。 カスタム PSK の [概要] ページで接続をいつでも編集できます。
  
### <a name="example-device-configuration-file"></a>デバイス構成ファイルの例

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="default"></a>既定のポリシー

### <a name="initiator"></a>イニシエーター

次のセクションでは、Azure がトンネルのイニシエーターになっているときに、サポートされているポリシーの組み合わせを一覧表示します。

**フェーズ 1**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2
* 3DES、SHA1、DH_GROUP_2
* 3DES、SHA_256、DH_GROUP_2

**フェーズ 2**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* CBC_3DES、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE
* CBC_3DES、SHA_256、PFS_NONE


### <a name="responder"></a>応答側

次のセクションでは、Azure がトンネルの応答側になっているときに、サポートされているポリシーの組み合わせを一覧表示します。

**フェーズ 1**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2
* 3DES、SHA1、DH_GROUP_2
* 3DES、SHA_256、DH_GROUP_2

**フェーズ 2**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* CBC_3DES、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE
* CBC_3DES、SHA_256、PFS_NONE
* CBC_DES、SHA_1、PFS_NONE 
* AES_256、SHA_1、PFS_1
* AES_256、SHA_1、PFS_2
* AES_256、SHA_1、PFS_14
* AES_128、SHA_1、PFS_1
* AES_128、SHA_1、PFS_2
* AES_128、SHA_1、PFS_14
* CBC_3DES、SHA_1、PFS_1
* CBC_3DES、SHA_1、PFS_2
* CBC_3DES、SHA_256、PFS_2
* AES_256、SHA_256、PFS_1
* AES_256、SHA_256、PFS_2
* AES_256、SHA_256、PFS_14
* AES_256、SHA_1、PFS_24
* AES_256、SHA_256、PFS_24
* AES_128、SHA_256、PFS_NONE
* AES_128、SHA_256、PFS_1
* AES_128、SHA_256、PFS_2
* AES_128、SHA_256、PFS_14
* CBC_3DES、SHA_1、PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>仮想ハブ vpngateway ポリシーとオンプレミス SDWAN/VPN デバイスまたは SD-WAN 構成とが、すべて一致している必要はありますか。

ご使用のオンプレミス SDWAN/VPN デバイスまたは SD-WAN 構成は、Azure IPsec/IKE ポリシーで指定した次のアルゴリズムおよびパラメーターと一致している (または含んでいる) 必要があります。 SA の有効期間は、ローカルの指定のみとなります。一致している必要はありません。

* IKE 暗号化アルゴリズム
* IKE 整合性アルゴリズム
* DH グループ
* IPsec 暗号化アルゴリズム
* IPsec 整合性アルゴリズム
* PFS グループ

## <a name="feedback"></a>プレビューのフィードバック

お客様からのフィードバックをお待ちしています。 Virtual WAN に関する問題を報告したり (肯定的、否定的を問わず) フィードバックを送信したりするには、<azurevirtualwan@microsoft.com> までメールをお送りください。 件名の “[ ]” には、お客様の会社名を入力してください。 また、問題を報告する場合は、サブスクリプション ID も入力してください。

## <a name="next-steps"></a>次の手順

仮想 WAN の詳細については、「[About Azure Virtual WAN](virtual-wan-about.md)」(Azure Virtual WAN について) および「[Azure Virtual WAN FAQ](virtual-wan-faq.md)」(Azure Virtual WAN のよくあるご質問) を参照してください。
