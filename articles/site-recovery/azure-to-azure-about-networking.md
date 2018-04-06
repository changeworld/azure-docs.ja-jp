---
title: Azure Site Recovery を使用した Azure から Azure へのディザスター リカバリーのネットワークについて | Microsoft Docs
description: Azure Site Recovery を使用した Azure VM のレプリケーション用のネットワークの概要について示します。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/26/2018
ms.author: sujayt
ms.openlocfilehash: 48be55632d9c1bece3f1a6e4f9ac12a68f9cb7ab
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Azure から Azure へのレプリケーションのネットワークについて

>[!NOTE]
> Azure 仮想マシンの Site Recovery レプリケーションは現在プレビューの段階です。

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、リージョン間で Azure VM をレプリケートして復旧するときの、ネットワーク ガイダンスについて説明します。 

## <a name="before-you-start"></a>開始する前に

[このシナリオ](azure-to-azure-architecture.md)に対して、Site Recovery でどのようにディザスター リカバリーを提供するかについて説明します。

## <a name="typical-network-infrastructure"></a>一般的なネットワーク インフラストラクチャ

次の図は、Azure VM で実行しているアプリケーションの一般的な Azure 環境を示しています。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

オンプレミス ネットワークから Azure への接続に Azure ExpressRoute または VPN 接続を使用している場合、環境は次のようになります。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

通常、ネットワークは、ファイアウォールやネットワーク セキュリティ グループ (NSG) を使用して保護されます。 ファイアウォールでは、URL や IP ベースのホワイトリスト登録を使用して、ネットワーク接続を制御します。 NSG では、IP アドレスの範囲を使用するルールを指定して、ネットワーク接続を制御します。

>[!IMPORTANT]
> 認証済みプロキシを使用してネットワーク接続を制御することは、Site Recoery ではサポートされておらず、レプリケーションを有効にすることはできません。


## <a name="outbound-connectivity-for-urls"></a>URL に対する送信接続

送信接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、以下の Site Recovery の URL を許可してください。


**URL** | **詳細**  
--- | ---
*.blob.core.windows.net | VM からソース リージョンのキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。
login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証に必要です。
*.hypervrecoverymanager.windowsazure.com | VM から Site Recovery サービス通信を実行できるようにするために必要です。
*.servicebus.windows.net | VM から Site Recovery の監視および診断データを書き込むことができるようにするために必要です。

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP アドレス範囲に対する送信接続

IP ベースのファイアウォール プロキシ、または NSG ルールを使用して送信接続を制御している場合は、次の IP 範囲を許可する必要があります。

- ソースの場所に対応するすべての IP アドレス範囲 
    - [IP アドレスの範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)をダウンロードできます。
    - VM からキャッシュ ストレージ アカウントにデータを書き込むことができるように、これらのアドレスを許可する必要があります。
- Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP アドレスの範囲。
    - 今後、新しいアドレスが Office 365 の範囲に追加された場合は、新しい NSG ルールを作成する必要があります。
- Site Recovery サービス エンドポイントの IP アドレス。 これらは [XML ファイル](https://aka.ms/site-recovery-public-ips)で利用することができ、ターゲットの場所によって異なります。
-  NSG の必要なルールを自動的に作成するには、[こちらのスクリプトをダウンロードして使用](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)することができます。 
- 必要な NSG ルールをテスト NSG に作成し、問題がないことを確認してから、運用環境の NSG にルールを作成することをお勧めします。
- 必要な数の NSG ルールを作成するには、サブスクリプションがホワイトリストに登録されていることを確認します。 サブスクリプションで NSG ルールの制限値を増やすには、Azure サポートにお問い合わせください。

IP アドレスの範囲は次のとおりです。

>
   **ターゲット** | **Site Recovery IP** |  **Site Recovery 監視 IP**
   --- | --- | ---
   東アジア | 52.175.17.132 | 13.94.47.61
   東南アジア | 52.187.58.193 | 13.76.179.223
   インド中部 | 52.172.187.37 | 104.211.98.185
   インド南部 | 52.172.46.220 | 104.211.224.190
   米国中北部 | 23.96.195.247 | 168.62.249.226
   北ヨーロッパ | 40.69.212.238 | 52.169.18.8
   西ヨーロッパ | 52.166.13.64 | 40.68.93.145
   米国東部 | 13.82.88.226 | 104.45.147.24
   米国西部 | 40.83.179.48 | 104.40.26.199
   米国中南部 | 13.84.148.14 | 104.210.146.250
   米国中央部 | 40.69.144.231 | 52.165.34.144
   米国東部 2 | 52.184.158.163 | 40.79.44.59
   東日本 | 52.185.150.140 | 138.91.1.105
   西日本 | 52.175.146.69 | 138.91.17.38
   ブラジル南部 | 191.234.185.172 | 23.97.97.36
   オーストラリア東部 | 104.210.113.114 | 191.239.64.144
   オーストラリア南東部 | 13.70.159.158 | 191.239.160.45
   カナダ中部 | 52.228.36.192 | 40.85.226.62
   カナダ東部 | 52.229.125.98 | 40.86.225.142
   米国中西部 | 52.161.20.168 | 13.78.149.209
   米国西部 2 | 52.183.45.166 | 13.66.228.204
   英国西部 | 51.141.3.203 | 51.141.14.113
   英国南部 | 51.140.43.158 | 51.140.189.52
   英国南部 2 | 13.87.37.4| 13.87.34.139
   英国北部 | 51.142.209.167 | 13.87.102.68
   韓国中部 | 52.231.28.253 | 52.231.32.85
   韓国南部 | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>NSG 構成の例

この例は、レプリケートする VM に対して NSG ルールを構成する方法を示しています。 

- NSG ルールを使用して送信接続を制御している場合は、必要なすべての IP アドレスの範囲に対して "HTTPS 送信を許可" ルールを使用します。
- この例では、VM ソースの場所は "米国東部" で、ターゲットの場所は "米国中央部" であると仮定します。

### <a name="nsg-rules---east-us"></a>NSG ルール - 米国東部

1. [米国東部 IP アドレスの範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に対応するルールを作成します。 これは、VM からキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。
2. Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP アドレスの範囲に対して、ルールを作成します。
3. ターゲットの場所に対応するルールを作成します。

   **場所** | **Site Recovery IP アドレス** |  **Site Recovery 監視 IP アドレス**
    --- | --- | ---
   米国中央部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG ルール - 米国中央部 

フェールオーバー後にターゲット リージョンからソース リージョンへのレプリケーションが有効になるようにするには、次のルールが必要です。

* [米国中部 IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に対応するルール。 これらは、VM からキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。

* Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP 範囲に対するルール。

* ソースの場所に対応するルール:
    - 米国東部
    - Site Recovery IP アドレス: 13.82.88.226
    - Site Recovery 監視 IP アドレス: 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

オンプレミスと Azure の場所が ExpressRoute または VPN で接続されている場合は、このセクションのガイドラインに従ってください。

### <a name="forced-tunneling"></a>強制トンネリング

通常、送信インターネット トラフィックを強制して、オンプレミスの場所を通過する既定のルート (0.0.0.0/0) を定義します。 これはお勧めしません。 レプリケーション トラフィックと Site Recovery サービスの通信は、Azure の境界を離れないようにします。 解決するには、レプリケーション トラフィックがオンプレミスに移動しないように、[こちらの IP 範囲](#outbound-connectivity-for-azure-site-recovery-ip-ranges)のユーザー定義ルート (UDR) を追加します。

### <a name="connectivity"></a>接続 

ターゲットの場所とオンプレミスの場所の間の接続に関する、次のガイドラインに従ってください。
- アプリケーションがオンプレミスのマシンに接続する必要がある場合、または VPN/ExpressRoute を介してオンプレミスからアプリケーションに接続するクライアントがある場合は、ターゲットの Azure リージョンとオンプレミスのデータセンターの間に少なくとも 1 つの[サイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)があることを確認します。

- ターゲットの Azure リージョンとオンプレミスのデータセンターの間のトラフィック フローが多くなることが予想される場合は、ターゲットの Azure リージョンとオンプレミスのデータセンターの間に別の [ExpressRoute 接続](../expressroute/expressroute-introduction.md)を作成します。

- フェールオーバー後に仮想マシンの IP を保持する必要がある場合は、ターゲット リージョンのサイト間/ExpressRoute 接続を切断状態のままにしてください。 これにより、ソース リージョンの IP 範囲とターゲット リージョンの IP 範囲の間で競合がなくなります。

### <a name="expressroute-configuration"></a>ExpressRoute 構成
ExpressRoute 構成については、次のベスト プラクティスに従ってください。

- ソース リージョンとターゲット リージョンの両方で、ExpressRoute 回線を作成します。 その後、次の接続を作成します。
    - ソース仮想ネットワークとオンプレミス ネットワークの間の接続。ソース リージョンの ExpressRoute 回線を経由。
    - ターゲット仮想ネットワークとオンプレミス ネットワークの間の接続。ターゲット リージョンの ExpressRoute 回線を経由。


- ExpressRoute Standard の一部として、同じ地理的リージョンに回線を作成できます。 さまざまな地理的リージョンに ExpressRoute 回線を作成するには、Azure ExpressRoute Premium が必要ですが、これには増分コストが発生します  (既に ExpressRoute Premium を使用している場合、追加コストはありません)。詳細については、[ExpressRoute の場所に関するドキュメント](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)と「[ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/)」を参照してください。

- ソース リージョンとターゲット リージョンでは、異なる IP 範囲を使用することをお勧めします。 ExpressRoute 回線は、同じ IP アドレス範囲の 2 つの Azure 仮想ネットワークに同時に接続することはできません。

- 両方のリージョンに同じ IP 範囲で仮想ネットワークを作成してから、両方のリージョンに ExpressRoute 回線を作成できます。 フェールオーバー イベントの場合は、ソース仮想ネットワークから回線を切断し、ターゲット仮想ネットワークで回線を接続します。

 >[!IMPORTANT]
 > プライマリ リージョンが完全にダウンしている場合は、切断操作が失敗することがあります。 この場合、ターゲット仮想ネットワークは、ExpressRoute 接続を取得できません。

## <a name="next-steps"></a>次の手順
[Azure 仮想マシンをレプリケート](site-recovery-azure-to-azure.md)することで、ワークロードの保護を開始します。
