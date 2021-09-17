---
title: Azure Virtual Network とのアプリの統合
description: Azure App Service のアプリを Azure 仮想ネットワークと統合します。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/04/2021
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ac90dadc93ce09bc2ce0af6314e4bd2c48ab79f8
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768672"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>アプリを Azure 仮想ネットワークと統合する

この記事では、Azure App Service の VNet 統合機能と、それを [Azure App Service](./overview.md) のアプリで設定する方法について説明します。 [Azure Virtual Network][VNETOverview] (VNet) を使用すると、多くの Azure リソースをインターネットにルーティングできないネットワークに配置できます。 VNet 統合機能を使用すると、アプリは VNet 内のリソースにアクセスするか、VNet を通じてリソースにアクセスできます。 VNet 統合では、アプリにプライベートでアクセスすることはできません。

Azure App Service には、次の 2 つのバリエーションがあります。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNET 統合を有効にする

1. App Service ポータルで **[ネットワーク]** の UI に移動します。 **[VNet 統合]** の下の **[ここをクリックして構成]** を選択します。

1. **[Add VNet]\(VNet の追加)** を選択します。

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-app.png" alt-text="Vnet 統合を選択する":::

1. ドロップダウン リストには、お使いのサブスクリプションで同じリージョンに存在するすべての Azure Resource Manager 仮想ネットワークが含まれます。 その下に、他のすべてのリージョンの Resource Manager 仮想ネットワークの一覧が表示されます。 統合する VNet を選択します。

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-add-vnet.png" alt-text="VNet の選択":::

    * 同じリージョン内の VNet の場合は、新しいサブネットを作成するか、空の既存のサブネットを選択します。
    * 別のリージョンの VNet を選択するには、ポイント対サイトが有効になっている VNet ゲートウェイがプロビジョニングされている必要があります。
    * クラシック VNet と統合するには、 **[仮想ネットワーク]** ドロップダウン リストを選択するのではなく、 **[クラシック VNet に接続するには、ここをクリックします]** を選択します。 使用するクラシック仮想ネットワークを選択します。 ターゲットの VNet には、ポイント対サイトが有効になっている Virtual Network ゲートウェイがプロビジョニングされている必要があります。

    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-classic.png" alt-text="クラシック VNet の選択":::

統合中にアプリは再起動されます。 統合が完了すると、統合されている VNet の詳細が表示されます。

## <a name="regional-vnet-integration"></a>リージョン VNet 統合

リージョン VNet 統合では、同じリージョン内の VNet への接続がサポートされ、ゲートウェイは必要ではありません。 リージョン VNet 統合を使用すると、アプリは次のものにアクセスできるようになります。

* アプリと同じリージョンにある VNet 内のリソース。
* VNet 内のリソースは、アプリが統合されている VNet とピアリングされます。
* サービス エンドポイントでセキュリティ保護されたサービス。
* Azure ExpressRoute 接続にまたがるリソース。
* 統合されている VNet 内のリソース。
* Azure ExpressRoute 接続を含む、ピアリングされた接続にまたがるリソース。
* プライベート エンドポイントが有効なサービス。

同じリージョンの VNet との VNet 統合を使用する場合は、次の Azure ネットワーク機能を使用できます。

* **ネットワーク セキュリティ グループ (NSG)** :統合サブネットに配置された NSG を使って送信トラフィックをブロックできます。 VNet 統合を使ってアプリへの受信アクセスを提供できないため、受信規則は適用されません。
* **ルート テーブル (UDR)** :統合サブネット上にルート テーブルを配置して、必要な場所に送信トラフィックを送信できます。

この機能は、[カスタム コンテナー](./quickstart-custom-container.md)を含め、Windows と Linux の両方のアプリで完全にサポートされています。 すべての動作は、Windows アプリと Linux アプリ間で同じです。

### <a name="how-regional-vnet-integration-works"></a>リージョン VNET 統合のしくみ

App Service 内のアプリは、worker ロールでホストされます。 Basic 以上の価格プランは、同じ worker 上で他の顧客のワークロードが実行されない専用のホスティング プランです。 リージョン VNet 統合は、委任されたサブネット内のアドレスで仮想インターフェイスをマウントすることによって機能します。 送信元アドレスは VNet 内に存在するため、VNet 内の VM と同様に、VNet 内または VNet 経由でほとんどの場所にアクセスできます。 ネットワークの実装は、VNet で VM を実行する場合とは異なります。 そのため、一部のネットワーク機能はこの機能ではまだ使用できません。

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-regional-works.png" alt-text="リージョン VNET 統合のしくみ":::

リージョン VNet 統合が有効になっているとき、アプリによる送信は VNet 経由で行われます。 アプリのプロパティ ポータルに一覧表示される送信アドレスは、引き続きそのアプリによって使用されるアドレスです。 すべてのトラフィック ルーティングが有効になっている場合、すべての送信トラフィックは VNet に送信されます。 すべてのトラフィック ルーティングが有効になっていない場合、統合サブネット上で構成されたプライベート トラフィック (RFC1918) とサービス エンドポイントだけが VNet に送信され、インターネットへの送信トラフィックは通常と同じチャネルを経由します。

この機能では、worker ごとに 1 つの仮想インターフェイスのみがサポートされます。 worker あたり 1 つの仮想インターフェイスは、App Service プランごとに 1 リージョンの VNet 統合があることを意味します。 同じ App Service プラン内のすべてのアプリで、同じ VNet 統合を使用できます。 アプリで追加の VNet に接続する必要がある場合は、別の App Service プランを作成する必要があります。 使用される仮想インターフェイスは、顧客が直接アクセスできるリソースではありません。

このテクノロジが動作する方法の性質のために、VNet 統合で使用されるトラフィックは Azure Network Watcher や NSG フローのログには表示されません。

### <a name="subnet-requirements"></a>サブネットの要件

VNet 統合は、専用サブネットに依存します。 サブネットをプロビジョニングすると、Azure サブネットは先頭から 5 つの IP を失います。 プラン インスタンスごとに、統合サブネットから 1 つのアドレスが使用されます。 アプリを 4 つのインスタンスにスケールする場合は、4 つのアドレスが使用されます。 

サイズをスケールアップまたはスケールダウンすると、必要なアドレス空間が短期間に 2 倍になります。 これは、特定のサブネット サイズでサポートされる実際の使用可能なインスタンスに影響します。 次の表に、CIDR ブロックあたりの使用可能アドレスの最大数と、これが水平スケールに与える影響を示します。

| CIDR ブロック サイズ | 使用可能なアドレスの最大数 | 水平スケールの最大値 (インスタンス)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>ある時点でサイズまたは SKU のいずれかでスケールアップまたはスケールダウンする必要があることを前提としています。 

割り当てた後はサブネット サイズを変更できないため、アプリが到達する可能性のあるスケールに対応できるだけの十分な大きさを持つサブネットを使用してください。 サブネット容量に関する問題を回避するには、64 個のアドレスを持つ /26 を使用する必要があります。

プラン内のご自身のアプリを、別のプラン内のアプリから既に接続されている VNet にアクセスできるようにしたい場合は、既存の VNet 統合によって使用されているものとは異なるサブネットを選択します。

### <a name="routes"></a>ルート

リージョン VNet 統合を構成する場合は、2 種類のルーティングを考慮する必要があります。 アプリケーション ルーティングでは、アプリケーションから VNet にルーティングされるトラフィックを定義します。 ネットワーク ルーティングは、トラフィックが VNet から送信される方法を制御する機能です。

#### <a name="application-routing"></a>アプリケーション ルーティング

アプリケーション ルーティングを構成する場合は、VNet にすべてのトラフィックか、またはプライベート トラフィック ([RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) トラフィックとも呼ばれます) のみをルーティングできます。 これを構成するには、[Route All]\(すべてのルート\) 設定を使用します。 [Route All]\(すべてのルート\) が無効になっている場合、アプリはプライベート トラフィックのみを VNet にルーティングします。 すべての送信トラフィックを VNet にルーティングする場合は、[Route All]\(すべてのルート\) が有効になっていることを確認してください。

> [!NOTE]
> * [Route All]\(すべてのルート\) が有効になっている場合、すべてのトラフィックは、統合サブネットに適用された NSG と UDR の対象になります。 すべてのトラフィックのルーティングが有効になっていても、トラフィックを他の場所に送信するルートを指定しない限り、送信トラフィックはアプリのプロパティに一覧表示されているアドレスから送信されます。
> 
> * [Route All]\(すべてのルート\) は、Windows コンテナーでは現在サポートされていません。
>
> * リージョン VNet 統合では、ポート 25 を使用できません。

次の手順を使用して、ポータルからアプリでの [Route All]\(すべてのルート\) を無効にできます。 

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-enabled.png" alt-text="[Route All]\(すべてのルート\) が有効":::

1. アプリ ポータルの **[VNet 統合]** UI にアクセスします。
1. **[Route All]\(すべてのルート\)** を [無効] にします。
    
    :::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-route-all-disabling.png" alt-text="[Route All]\(すべてのルート\) を無効にする":::

1. **[はい]** を選択します。

CLI を使用して [Route All]\(すべてのルート\) を構成することもできます (*注*: `az version` は 2.27.0 以降が必要です)。

```azurecli-interactive
az webapp config set --resource-group myRG --name myWebApp --vnet-route-all-enabled [true|false]
```

[Route All]\(すべてのルート\) 構成設定は、すべてのトラフィックをルーティングを有効にするのに推奨される方法です。 この構成設定を使用すると、[組み込みポリシー](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33228571-70a4-4fa1-8ca1-26d0aba8d6ef)での動作を監査することができます。 既存の `WEBSITE_VNET_ROUTE_ALL` アプリ設定を使用することもでき、どちらの設定でも、すべてのトラフィックのルーティングを有効にすることができます。

#### <a name="network-routing"></a>ネットワーク ルーティング

ルート テーブルを使用して、アプリからの送信トラフィックを任意の場所にルーティングすることができます。 ルート テーブルは、宛先トラフィックに影響します。 [アプリケーションのルーティング](#application-routing)で [Route All]\(すべてのルート\) が無効になっている場合、ルート テーブルの影響を受けるのはプライベート トラフィック (RFC1918) のみです。 一般的な宛先には、ファイアウォール デバイスまたはゲートウェイを含めることができます。 統合サブネット上に設定されているルートは、受信アプリ要求への応答には影響を与えません。 

オンプレミスのすべての送信トラフィックをルーティングする場合は、ルート テーブルを使用して、すべての送信トラフィックを ExpressRoute ゲートウェイに送信できます。 ゲートウェイにトラフィックをルーティングする場合は、外部ネットワークのルートを設定して、応答を返信するようにしてください。

また、Border Gateway Protocol (BGP) ルートも、アプリのトラフィックに影響を与えます。 ExpressRoute ゲートウェイのようなものから BGP ルートを使用している場合は、アプリの送信トラフィックが影響を受けます。 BGP ルートもユーザー定義のルートと同様に、ルーティング スコープの設定に従ってトラフィックに影響を与えます。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

リージョン VNet 統合を使用するアプリでは、[ネットワーク セキュリティ グループ][VNETnsg]を使用して、VNet 内またはインターネット上のリソースへの送信トラフィックをブロックできます。 パブリック アドレスへのトラフィックをブロックするには、VNet への [[Route All]\(すべてのルート\)](#application-routing) が有効になっていることを確認する必要があります。 [Route All]\(すべてのルート\) が有効になっていない場合、NSG は RFC1918 トラフィックにのみ適用されます。

統合サブネットに適用された NSG は、統合サブネットに適用されているルート テーブルに関係なく有効になります。 

VNet 統合はアプリからの送信トラフィックのみに影響を与えるため、NSG の受信規則はアプリに適用されません。 アプリへの受信トラフィックを制御するには、アクセス制限機能を使用します。

### <a name="service-endpoints"></a>サービス エンドポイント

リージョン VNet 統合を使用すると、サービス エンドポイントで保護されている Azure サービスに接続できます。 サービス エンドポイントで保護されたサービスにアクセスするには、次の操作を行う必要があります。

* 統合のための特定のサブネットに接続するために、Web アプリとのリージョン VNet 統合を構成します。
* 対象のサービスに移動し、統合サブネットに対してサービス エンドポイントを構成します。

### <a name="private-endpoints"></a>プライベート エンドポイント

[プライベート エンドポイント][privateendpoints]を呼び出す場合は、DNS 参照がプライベート エンドポイントに解決されるようにする必要があります。 この動作は、次のいずれかの方法で実現できます。 

* Azure DNS プライベート ゾーンと統合する。 VNet にカスタム DNS サーバーがない場合、この処理はゾーンが VNet にリンクされると自動的に行われます。
* アプリで使用される DNS サーバーでプライベート エンドポイントを管理する。 これを行うには、プライベート エンドポイント アドレスを把握し、到達しようとしているエンドポイントが A レコードを使用してそのアドレスにポイントされるようにします。
* Azure DNS プライベート ゾーンに転送する独自の DNS サーバーを構成する。

### <a name="azure-dns-private-zones"></a>Azure DNS プライベート ゾーン 

アプリを VNet と統合すると、VNet に構成されているのと同じ DNS サーバーが使用されます。カスタム DNS が指定されていない場合は、Azure の既定の DNS と、VNet にリンクされているすべてのプライベート ゾーンが使用されます。

> [!NOTE]
> Linux アプリの場合、Azure DNS プライベート ゾーンは、[Route All]\(すべてのルート\) が有効になっている場合にのみ機能します。

### <a name="limitations"></a>制限事項

同じリージョンの VNet との VNet 統合を使用する場合、いくつかの制限があります。

* グローバル ピアリング接続にまたがるリソースには到達できません。
* クラシック仮想ネットワークでは、ピアリング接続をまたいだリソースには到達できません。
* この機能は、Premium V2 と Premium V3 のすべての App Service スケール ユニットから使用できます。 また、Standard でも使用できますが、新しい App Service のスケール ユニットからのみ利用できます。 以前のスケール ユニットを使用している場合は、Premium V2 App Service プランの機能のみを使用できます。 Standard App Service プランでこの機能を使用できるようにするには、Premium V3 App Service プランでアプリを作成します。 それらのプランは、最新のスケール ユニットでのみサポートされます。 その後、必要に応じてスケールダウンできます。  
* 統合サブネットは、1 つの App Service プランでしか使用できません。
* この機能は、App Service Environment にある Isolated プランのアプリでは使用できません。
* この機能には、Azure Resource Manager VNet 内に /28 以上の未使用のサブネットが必要です。
* アプリと VNet は同じリージョンに存在する必要があります。
* 統合アプリで VNet を削除することはできません。 VNet を削除する前に、統合を削除してください。
* App Service プランごとに 1 リージョンの VNet 統合のみを持つことができます。 同じ App Service プラン内の複数のアプリが同じ VNet を使用できます。
* リージョン VNet 統合を使用しているアプリがあるときに、アプリまたはプランのサブスクリプションを変更することはできません。
* アプリでは、構成を変更せずに Linux プラン上で Azure DNS Private Zones のアドレスを解決することはできません。

## <a name="gateway-required-vnet-integration"></a>ゲートウェイが必要な VNet 統合

ゲートウェイが必要な VNet 統合では、別のリージョンの VNet またはクラシック仮想ネットワークへの接続がサポートされています。 ゲートウェイが必要な VNet 統合の特徴は次のとおりです。

* アプリでは一度に 1 つの VNet にのみ接続できます。
* App Service プランで、最大 5 つの VNet と統合できます。
* App Service プランで使用できる総数に影響を与えることなく、App Service プラン内の複数のアプリで同じ VNet を使用できます。 同じ App Service プラン内の同じ VNet を使用しているアプリが 6 つある場合、使用されている VNet は 1 つとしてカウントされます。
* ゲートウェイでの SLA により、99.9% の SLA がサポートされます。
* アプリでは VNet が構成されている DNS を使用できます。
* アプリに接続できるためには、Virtual Network のルート ベースのゲートウェイに、SSTP のポイント対サイト VPN が構成されている必要があります。

ゲートウェイが必要な VNet 統合は次の場合は使用できません。

* Azure ExpressRoute で接続された VNet に対して。
* Linux アプリから。
* [Windows コンテナー](quickstart-custom-container.md)から。
* サービス エンドポイントによって保護されているリソースにアクセスする場合。
* ExpressRoute とポイント対サイトまたはサイト間 VPN の両方をサポートする共存ゲートウェイに対して。

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Azure 仮想ネットワークでゲートウェイを設定する

ゲートウェイを作成するには:

1. VNet 内に[ゲートウェイ サブネットを作成][creategatewaysubnet]します。

1. [VPN ゲートウェイを作成][creategateway]します。 VPN の種類はルート ベースを選択します。

1. [ポイント対サイト アドレスを設定][setp2saddresses]します。 ゲートウェイが Basic SKU 内にない場合は、ポイント対サイトの構成で IKEV2 を無効にする必要があり、SSTP を選択する必要があります。 ポイント対サイトのアドレス空間は、RFC 1918 のアドレス ブロック 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16 内に存在する必要があります。

App Service の VNet 統合で使用するゲートウェイを作成する場合は、証明書をアップロードする必要はありません。 ゲートウェイの作成には 30 分かかることがあります。 ゲートウェイがプロビジョニングされるまで、アプリを VNet に統合することはできません。

### <a name="how-gateway-required-vnet-integration-works"></a>ゲートウェイが必要な VNet 統合のしくみ

ゲートウェイが必要な VNet 統合は、ポイント対サイト VPN テクノロジに基づいて構築されています。 ポイント対サイト VPN により、ネットワーク アクセスはアプリがホストされている仮想マシンに制限されます。 アプリは、ハイブリッド接続または VNet 統合を通してのみ、インターネットにトラフィックを送信するように制限されます。 ゲートウェイが必要な VNet 統合を使用するようにアプリをポータルで構成すると、ゲートウェイとアプリケーション側で証明書を作成して割り当てるための複雑なネゴシエーションが、ユーザーの代わりに管理されます。 結果として、アプリをホストするために使用される worker は、選択した VNet 内の仮想ネットワーク ゲートウェイに直接接続できるようになります。

:::image type="content" source="./media/web-sites-integrate-with-vnet/vnetint-how-gateway-works.png" alt-text="ゲートウェイが必要な VNet 統合のしくみ":::

### <a name="access-on-premises-resources"></a>オンプレミスのリソースにアクセスする

アプリは、サイト間接続がある Vnet と統合することで、オンプレミスのリソースにアクセスできます。 ゲートウェイが必要な VNet 統合を使用する場合は、ポイント対サイト アドレス ブロックでオンプレミスの VPN ゲートウェイのルートを更新します。 サイト間 VPN が初めてセットアップされるときには、その構成に使用するスクリプトによって、ルートが正しくセットアップされるはずです。 サイト間 VPN を作成した後にポイント対サイト アドレスを追加する場合は、ルートを手動で更新する必要があります。 その詳しい方法はゲートウェイごとに異なるため、ここでは説明しません。 BGP をサイト間 VPN 接続で構成することはできません。

VNet 経由でオンプレミスのリソースにアクセスするために、リージョン VNet 統合機能に対する追加の構成は必要ありません。 必要なのは、単に ExpressRoute またはサイト間 VPN を使用して VNet をオンプレミスのリソースに接続することだけです。

> [!NOTE]
> ゲートウェイが必要な VNet 統合機能では、アプリは ExpressRoute ゲートウェイを含む VNet とは統合されません。 ExpressRoute ゲートウェイが[共存モード][VPNERCoex]で構成されている場合であっても、VNet 統合は機能しません。 ExpressRoute 接続経由でリソースにアクセスする必要がある場合は、VNet で実行されるリージョン VNet 統合機能または [App Service Environment][ASE] を使用します。
>
>

### <a name="peering"></a>ピアリング

リージョン VNet 統合でピアリングを使用する場合、追加の構成を行う必要はありません。

ゲートウェイが必要な VNet 統合でピアリングを使用する場合は、いくつかの追加の項目を構成する必要があります。 アプリで動作するようにピアリングを構成するには:

1. アプリの接続先の VNet でピアリング接続を追加します。 ピアリング接続を追加するときに、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** チェック ボックスと **[ゲートウェイ転送を許可する]** チェック ボックスをオンにします。
1. 接続先の VNet とピアリングする VNet にピアリング接続を追加します。 対象の VNet でピアリング接続を追加するときは、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** と **[Allow remote gateways]\(リモート ゲートウェイを許可する)** を選択します。
1. ポータルで **[App Service プラン]**  >  **[ネットワーク]**  >  **[VNet 統合]** に移動します。 アプリの接続先 VNet を選択します。 ルーティングのセクションで、アプリの接続先の VNet とピアリングされている VNet のアドレス範囲を追加します。

## <a name="manage-vnet-integration"></a>VNet 統合を管理する

VNet との接続および切断は、アプリ レベルで行われます。 複数のアプリで VNet 統合に影響する可能性がある操作は、App Service プラン レベルで行われます。 アプリの **[ネットワーク]**  >  **[VNet 統合]** ポータルから、VNet に関する詳細を取得できます。 **[App Service プラン]**  >  **[ネットワーク]**  >  **[VNet 統合]** ポータルの App Service プラン レベルでも、同様の情報が表示されます。

VNet 統合インスタンスのアプリ ビューで実行できる操作は、現在接続されている VNet からアプリを切断する操作のみです。 VNet からアプリを切断するには、 **[切断]** を選択します。 VNet から切断すると、アプリが再起動されます。 切断によって VNet に変更は生じません。 サブネットやゲートウェイは削除されません。 その後で VNet を削除する必要がある場合は、まず VNet からアプリを切断し、ゲートウェイなどのそこに含まれているリソースを削除します。

App Service プランの VNet 統合の UI には、App Service プラン内のアプリによって使用されているすべての VNet 統合が表示されます。 各 VNet の詳細を表示するには、目的の VNet を選択します。 ここでは、ゲートウェイが必要な VNet 統合に対して実行できる操作が 2 つあります。

* **ネットワークを同期する**: ネットワーク同期操作は、ゲートウェイに依存する VNet 統合機能に対してのみ使用されます。 ネットワーク同期操作を実行すると、証明書とネットワークの情報が確実に同期されるようになります。VNet の DNS を追加または変更する場合は、ネットワーク同期操作を実行する必要があります。 この操作では、この VNet を使用するすべてのアプリが再起動されます。 この操作は、使用しているアプリと VNet が異なるサブスクリプションに属している場合は機能しません。
* **ルートを追加する**: ルートを追加すると、送信トラフィックが VNet に転送されます。

インスタンスに割り当てられたプライベート IP は、環境変数 **WEBSITE_PRIVATE_IP** によって公開されます。 Kudu コンソールの UI には、Web アプリで使用できる環境変数の一覧も表示されます。 この IP は、統合されたサブネットのアドレス範囲から割り当てられます。 リージョン VNet 統合の場合、WEBSITE_PRIVATE_IP の値は委任されたサブネットのアドレス範囲の IP になります。また、ゲートウェイが必要な VNet 統合では、この値は Virtual Network ゲートウェイで構成されているポイント対サイトのアドレス プールのアドレス範囲の IP になります。 これは、Web アプリが Virtual Network を通じてリソースに接続するために使用する IP です。 

> [!NOTE]
> WEBSITE_PRIVATE_IP の値は、必ず変更されます。 ただし、これは統合サブネットまたはポイント対サイトのアドレス範囲内の IP であるため、アドレス範囲全体からのアクセスを許可する必要があります。
>

### <a name="gateway-required-vnet-integration-routing"></a>ゲートウェイが必要な VNET 統合のルーティング
VNet で定義されているルートが、アプリから VNet にトラフィックを転送するために使用されます。 VNet に追加の送信トラフィックを送信するには、ここでそれらのアドレス ブロックを追加します。 この機能は、ゲートウェイが必要な VNet 統合でのみ動作します。 ゲートウェイが必要な VNet 統合を使用すると、リージョン VNet 統合の場合とは異なり、ルート テーブルによるアプリのトラフィックへの影響はありません。

### <a name="gateway-required-vnet-integration-certificates"></a>ゲートウェイが必要な VNet 統合の証明書
ゲートウェイが必要な VNet 統合が有効になっている場合は、接続のセキュリティを確保するために証明書の交換が必要です。 証明書と共に、DNS の構成、ルート、ネットワークについて記述するその他の同様の情報があります。

証明書またはネットワーク情報が変更された場合は、 **[ネットワークの同期]** を選択します。 **[ネットワークの同期]** を選択すると、アプリと VNet の間の接続が短時間途切れます。 アプリが再起動されていない間は、接続の途切れによって、サイトが正常に機能しなくなる可能性があります。

## <a name="pricing-details"></a>価格の詳細
リージョン VNet 統合機能には、App Service プランの価格レベルの料金を超える追加の使用料金はありません。

ゲートウェイが必要な VNet 統合機能の使用には、3 つの料金が関連します。

* **App Service プランの価格レベルの料金**: アプリは、Standard、Premium、PremiumV2、または PremiumV3 の App Service プランに属している必要があります。 コストについて詳しくは、「[App Service の価格][ASPricing]」をご覧ください。
* **データ転送コスト**: VNet が同じデータ センター内にある場合でも、データのエグレスには料金がかかります。 これらの料金については、[データ転送価格の詳細][DataPricing]に関するページをご覧ください。
* **VPN Gateway のコスト**: ポイント対サイト VPN に必要な仮想ネットワーク ゲートウェイに対して費用がかかります。 詳細については、「[VPN Gateway の価格][VNETPricing]」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

> [!NOTE]
> VNET 統合は、App Service の Docker Compose シナリオではサポートされていません。
> プライベート エンドポイントが存在する場合、Azure Functions のアクセス制限は無視されます。
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>オートメーション

リージョン VNet 統合では CLI がサポートされています。 次のコマンドにアクセスするには、[Azure CLI をインストール][installCLI]します。

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.
```

リージョン VNet 統合では PowerShell のサポートも提供されていますが、サブネットの resourceID のプロパティ配列を使用して汎用のリソースを作成する必要があります

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

# Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

# Creation of the VNet Integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```

<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
[VNETnsg]: /azure/virtual-network/security-overview/
