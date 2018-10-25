---
title: Azure Virtual WAN パートナー | Microsoft Docs
description: パートナーは、この記事を参考にして、Azure Virtual WAN 自動化を設定できます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: a4664e628af5824b7b197cbdb5c5af602a3a4476
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958688"
---
# <a name="virtual-wan-partners"></a>Virtual WAN パートナー

この記事では、Azure Virtual WAN のブランチ デバイス (お客様のオンプレミスの VPN デバイスまたは SDWAN CPE) を接続および構成する自動化環境を設定する方法を理解できます。 IPsec/IKEv2 または IPsec/IKEv1 経由で VPN 接続に対応できるブランチ デバイスを提供するプロバイダーの場合は、この記事が適しています。

ブランチ デバイス (お客様のオンプレミス VPN デバイスまたは SDWAN CPE) は通常、プロビジョニングするコントローラー/デバイス ダッシュボードを使用します。 多くの場合、SD-WAN ソリューションの管理者は、管理コンソールを使用して、ネットワークに接続する前にデバイスを事前プロビジョニングすることができます。 この VPN 対応デバイスは、コントローラーからコントロール プレーンのロジックを取得します。 VPN デバイスまたは SD-WAN コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 この種類の接続では、オンプレミスのデバイスに外部接続用パブリック IP アドレスを割り当てる必要があります。

## <a name ="before"></a>自動化を始める前に

* デバイスが IPsec IKEv1/IKEv2 をサポートしていることを確認します。 「[IPsec 接続の既定のポリシー](#default)」をご覧ください。
* Azure Virtual WAN への接続の自動化に使用する [REST API](https://docs.microsoft.com/rest/api/azure/) を参照してください。
* Azure Virtual WAN のポータル エクスペリエンスをテストします。
* 次に、接続手順の自動化する部分を決定します。 少なくとも、次の部分を自動化することお勧めします。

  * Access Control
  * Azure Virtual WAN へのブランチ デバイス情報のアップロード
  * Azure の構成のダウンロードと、ブランチ デバイスから Azure Virtual WAN への接続の設定

* Azure Virtual WAN と共に予想される顧客エクスペリエンスを理解します。

  1. 通常、仮想 WAN のユーザーは Virtual WAN リソースを作成することによってプロセスを始めます。
  2. ユーザーは、Azure Virtual WAN にブランチの情報を書き込むため、オンプレミスのシステム (ブランチ コントローラーまたは VPN デバイスのプロビジョニング ソフトウェア) に対してサービス プリンシパル ベースのリソース グループのアクセスを設定します。
  3. ユーザーはこの時点で、UI にログインしてサービス プリンシパルの資格情報を設定する場合があります。 それが完了すると、コントローラーはお客様が提供する自動化を使用してブランチの情報をアップロードできるようになります。 Azure 側でこれを手作業で行う場合は、"サイトの作成" に相当します。
  4. サイト (デバイス ブランチ) の情報を Azure で使用できるようになった後、ユーザーはハブにサイトを関連付けます。 仮想ハブは、Microsoft のマネージド仮想ネットワークです。 ハブには、オンプレミス ネットワーク (vpnsite) からの接続を可能にするさまざまなサービス エンドポイントが含まれています。 ハブは、リージョン内のネットワークのコアです。 ハブは Azure リージョンごとに 1 つしか存在できず、このプロセスの間に VPN エンドポイント (vpngateway) がリージョン内に作成されます。 VPN ゲートウェイは、帯域幅や接続ニーズに基づいて適切なサイズに設定されるスケーラブルなゲートウェイです。 仮想ハブと vpngateway の作成を、ブランチ デバイス コントローラーのダッシュボードから自動化できます。
  5. 仮想ハブがサイトに関連付けられると、ユーザーが手動でダウンロードするための構成ファイルが生成されます。 この部分に自動化を使用して、ユーザー エクスペリエンスをシームレスにできます。 ユーザーが手動でダウンロードしてブランチ デバイスを構成する代わりに、自動化を設定して、UI で最小限のクリックスルー エクスペリエンスを提供することにより、共有キーの不一致、IPSec パラメーターの不一致、構成ファイルの読みやすさなどの一般的な接続の問題を軽減できます。
  6. ソリューションのこのステップの最後では、ブランチ デバイスと仮想ハブの間にシームレスなサイト対サイト接続が作成されます。 他のハブの間に追加の接続を設定することもできます。 各接続はアクティブ/アクティブ トンネルです。 ユーザーは、トンネルに対するリンクごとに異なる ISP を使用できます。

## <a name ="understand"></a>自動化の詳細を理解する


###  <a name="access"></a>アクセス制御

お客様は、デバイスの UI で仮想 WAN の適切なアクセス制御を設定できる必要があります。 Azure サービス プリンシパルを使用することをお勧めします。 サービス プリンシパルに基づくアクセスは、ブランチ情報をアップロードするために適した認証をデバイス コント ローラーに提供します。 詳しくは、「[サービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)」をご覧ください。 この機能は Azure Virtual WAN のオファリングには含まれませんが、Azure でアクセスを設定するときの一般的な手順を以下に示しておきます。この後、関連する詳細がデバイス管理ダッシュボードに入力されます

* オンプレミスのデバイス コントローラー用の Azure Active Directory アプリケーションを作成します。
* アプリケーション ID と認証キーを取得する
* テナント ID を取得する
* アプリケーションを "共同作成者" ロールに割り当てる

###  <a name="branch"></a>ブランチ デバイスの情報をアップロードする

ブランチ (オンプレミス サイト) の情報を Azure にアップロードするユーザー エクスペリエンスをデザインします。 VPNSite 用の [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) を使用して、Virtual WAN でサイト情報を作成することができます。 すべてのブランチ SDWAN/VPN デバイスを提供したり、適切なデバイスのカスタマイズを選択したりすることができます。


### <a name="device"></a>デバイス構成のダウンロードと接続

このステップでは、Azure の構成をダウンロードし、ブランチ デバイスから Azure Virtual WAN への接続を設定します。 この手順で、プロバイダーを使用していないお客様が、手動で Azure の構成をダウンロードしてオンプレミス SDWAN/VPN デバイスに適用します。 プロバイダーは、この手順を自動化する必要があります。 デバイス コントローラーは、"GetVpnConfiguration" REST API を呼び出して、Azure の構成をダウンロードすることができます。これは、通常は次のようなファイルになります。

**構成メモ**

  * Azure Vnet が仮想ハブに接続されている場合は、ConnectedSubnets として表示されます。
  * VPN 接続では、ルート ベースの構成と IKEv2/IKEv1 を使用します。

#### <a name="understanding-the-device-configuration-file"></a>デバイス構成ファイルについて

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
  
#### <a name="example-device-configuration-file"></a>デバイス構成ファイルの例

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

## <a name="default"></a> IPsec 接続の既定のポリシー

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

ご使用のオンプレミス SDWAN/VPN デバイスまたは SD-WAN 構成は、Azure IPsec/IKE ポリシーで指定した次のアルゴリズムおよびパラメーターと一致している (または含んでいる) 必要があります。

* IKE 暗号化アルゴリズム
* IKE 整合性アルゴリズム
* DH グループ
* IPsec 暗号化アルゴリズム
* IPsec 整合性アルゴリズム
* PFS グループ

## <a name="next-steps"></a>次の手順

仮想 WAN の詳細については、「[About Azure Virtual WAN](virtual-wan-about.md)」(Azure Virtual WAN について) および「[Azure Virtual WAN FAQ](virtual-wan-faq.md)」(Azure Virtual WAN のよくあるご質問) を参照してください。

その他の情報については、<azurevirtualwan@microsoft.com> までメールをお送りください。 件名の “[ ]” には、お客様の会社名を入力してください。
