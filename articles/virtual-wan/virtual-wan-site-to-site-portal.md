---
title: Azure Virtual WAN:サイト間接続の作成
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのサイト間 VPN 接続を作成する方法を学習します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223809"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用してサイト間接続を作成する

このチュートリアルでは、Virtual WAN を使用して IPsec/IKE (IKEv1 と IKEv2) VPN 接続経由で Azure のリソースに接続する方法を示します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想 WAN を作成する
> * ハブを作成する
> * サイトを作成する
> * サイトをハブに接続する
> * VPN サイトをハブに接続する
> * VNet をハブに接続する
> * 構成ファイルをダウンロードする
> * 仮想 WAN を表示する

> [!NOTE]
> 通常、多くのサイトがある場合は、[Virtual WAN パートナー](https://aka.ms/virtualwan)を利用してこの構成を作成します。 ただし、ネットワークに慣れていて、独自の VPN デバイスの構成に熟練している場合は、この構成を自分で作成することができます。
>

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* 接続先の仮想ネットワークが用意されていること。 オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認してください。 Azure portal で仮想ネットワークを作成するには、[クイックスタート](../virtual-network/quick-create-portal.md)を参照してください。

* 仮想ネットワークに仮想ネットワーク ゲートウェイが存在しないこと。 仮想ネットワークにゲートウェイ (VPN または ExpressRoute のどちらか) が存在する場合は、すべてのゲートウェイを削除する必要があります。 代わりに、この構成では、仮想ネットワークが Virtual WAN ハブ ゲートウェイに接続されている必要があります。

* ハブ リージョンの IP アドレス範囲を取得します。 このハブは、Virtual WAN によって作成および使用される仮想ネットワークです。 ハブに指定するアドレス範囲が、接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>仮想 WAN を作成する

ブラウザーから Azure portal に移動し、Azure アカウントでサインインします。

1. [仮想 WAN] ページに移動します。 ポータルで **+ [リソースの作成]** をクリックします。 検索ボックスに「**Virtual WAN**」と入力し、Enter キーを押します。
2. 結果から **[Virtual WAN]** を選択します。 [Virtual WAN] (仮想 WAN) ページで、 **[作成]** をクリックして [WAN の作成] ページを開きます。
3. **[WAN の作成]** ページの **[基本]** タブで、次のフィールドに入力します。

   ![Virtual WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **[サブスクリプション]** - 使用するサブスクリプションを選択します。
   * **[リソース グループ]** - 新規作成するか、または既存のものを使用します。
   * **[リソース グループの場所]** - ドロップダウンからリソースの場所を選択します。 WAN はグローバルなリソースであり、特定のリージョンに存在するものではありません。 ただし、作成する WAN リソースをより簡単に管理および検索するために、リージョンを選択する必要があります。
   * **[名前]** - WAN に付ける名前を入力します。
   * **種類:** Basic または Standard。 Basic WAN を作成する場合は、Basic ハブのみを作成できます。 Basic ハブでは、VPN サイト間接続のみが可能です。
4. フィールドへの入力を完了したら、 **[確認および作成]** を選択します。
5. 検証に合格したら、 **[作成]** を選択して仮想 WAN を作成します。

## <a name="create-a-hub"></a><a name="hub"></a>ハブを作成する

ハブは、サイト間、ExpressRoute、またはポイント対サイト機能のためのゲートウェイを含めることができる仮想ネットワークです。 ハブが作成されると、サイトをアタッチしていない場合でも、ハブに対して課金されます。 仮想ハブ内にサイト間 VPN ゲートウェイを作成するには 30 分かかります。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>サイトを作成する

これで、この物理的な場所に対応するサイトを作成する準備ができました。 物理的な場所に合わせて必要な数のサイトを作成します。 たとえば、NY、ロンドン、および LA にブランチ オフィスがある場合は、3 つの別個のサイトを作成します。 これらのサイトには、オンプレミス VPN デバイスのエンドポイントが含まれています。 仮想 WAN 内の仮想ハブあたり最大 1000 個のサイトを作成できます。 複数のハブがある場合は、それらの各ハブあたり 1000 個作成できます。 仮想 WAN パートナー (リンクを挿入) の CPE デバイスがある場合は、そのパートナーに Azure へのオートメーションについて確認してください。 通常、オートメーションとは、単純なクリック操作で、大規模なブランチ情報を Azure にエクスポートし、CPE から Azure Virtual WAN VPN ゲートウェイへの接続を設定できることを意味します。 詳細については、[Azure から CPE パートナーへのオートメーション ガイダンス](virtual-wan-configure-automation-providers.md)を参照してください。

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>VPN サイトをハブに接続する

この手順では、VPN サイトをハブに接続します。

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>VNet をハブに接続する

この手順では、ハブと VNet の間の接続を作成します。 接続する VNet ごとにこれらの手順を繰り返します。

1. 仮想 WAN のページで、 **[仮想ネットワーク接続]** をクリックします。
2. 仮想ネットワーク接続のページで、 **[+ 接続の追加]** をクリックします。
3. **[接続の追加]** ページで、次のフィールドに入力します。

    * **[接続名]** - 接続に名前を付けます。
    * **[ハブ]** - この接続に関連付けるハブを選択します。
    * **[サブスクリプション]** - サブスクリプションを確認します。
    * **[仮想ネットワーク]** - このハブに接続する仮想ネットワークを選択します。 仮想ネットワークに既存の仮想ネットワーク ゲートウェイを設定することはできません。
4. **[OK]** をクリックして、仮想ネットワーク接続を作成します。

## <a name="download-vpn-configuration"></a><a name="device"></a>VPN 構成をダウンロードする

オンプレミス VPN デバイスを構成するには、VPN デバイス構成を使用します。

1. 仮想 WAN のページで、 **[概要]** をクリックします。
2. **[Hub ->VPNSite] (ハブから VPNSite)** ページの上部にある **[Download VPN config] (VPN 構成のダウンロード)** をクリックします。Azure により、リソース グループ "microsoft-network-[location]" にストレージ アカウントが作成されます。ここで、location は WAN の場所です。 VPN デバイスに構成を適用した後は、このストレージ アカウントを削除できます。
3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。
4. オンプレミスの VPN デバイスに構成を適用します。

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN デバイス構成ファイルについて

デバイス構成ファイルには、オンプレミスの VPN デバイスを構成するときに使用する構成が含まれています。 このファイルを表示すると、次の情報を確認できます。

* **vpnSiteConfiguration -** このセクションは、仮想 WAN に接続するサイトとして設定されたデバイスの詳細を示します。 ブランチ デバイスの名前とパブリック IP アドレスが含まれています。
* **vpnSiteConnections -** このセクションには、次の設定に関する情報が含まれています。

    * 仮想ハブ VNet の**アドレス空間**<br>例:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * ハブに接続されている VNet の**アドレス空間**<br>例:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 仮想ハブ vpngateway の **IP アドレス**。 vpngateway の各接続はアクティブ/アクティブ構成の 2 つのトンネルで構成されているため、このファイルには両方の IP アドレスが示されています。 この例では、サイトごとに "Instance0" と "Instance1" が表示されています。<br>例:

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
                  "10.3.0.0/16"
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

### <a name="configuring-your-vpn-device"></a>VPN デバイスの構成

>[!NOTE]
> Virtual WAN パートナー ソリューションを使用して作業している場合、VPN デバイスの構成が自動的に行われます。 デバイス コントローラーは Azure から構成ファイルを取得し、デバイスに適用して Azure への接続を設定します。 これは、VPN デバイスを手動で構成する方法を知る必要がないことを意味します。
>

デバイスを構成する手順が必要な場合は、[VPN デバイス構成スクリプトのページ](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)の手順を使用できます。このとき、次の点に注意してください。

* VPN デバイスのページに書かれている手順は Virtual WAN 用ではありませんが、構成ファイルの Virtual WAN の値を使用して VPN デバイスを手動で構成することができます。 
* VPN Gateway 用のダウンロード可能なデバイス構成スクリプトは、構成が異なるため、Virtual WAN では機能しません。
* 新しい仮想 WAN は、IKEv1 と IKEv2 の両方をサポートできます。
* Virtual WAN では、ポリシー ベースとルート ベースの両方の VPN デバイスとデバイスの手順を使用できます。

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. **[概要]** ページで、マップ上の各ポイントはハブを表します。 任意のポイントにマウス ポインターを置くと、ハブの正常性の概要、接続の状態、および入出力バイト数が表示されます。
3. ハブと接続のセクションでは、ハブの状態、VPN サイトなどを表示できます。特定のハブ名をクリックすると、VPN サイトに移動して追加の詳細を表示できます。

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
