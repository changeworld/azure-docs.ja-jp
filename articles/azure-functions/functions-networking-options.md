---
title: Azure Functions のネットワーク オプション
description: Azure Functions で利用可能なすべてのネットワーク オプションの概要。
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: c954f411d14aaa0f33bf4a3423a67ae64204f932
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892860"
---
# <a name="azure-functions-networking-options"></a>Azure Functions のネットワーク オプション

この記事では、Azure Functions のホスティング オプションで利用できるネットワーク機能について説明します。 以下に示すネットワーク オプションではすべて、インターネットのルーティング可能アドレスを使用せずにリソースにアクセスする機能、または関数アプリへのインターネット アクセスを制限する機能が提供されます。

ホスティング モデルには、さまざまなレベルのネットワーク分離機能があります。 正しいものを選択することで、ネットワーク分離の要件を満たすことができます。

関数アプリは、いくつかの方法でホストできます。

* さまざまなレベルの仮想ネットワーク接続性とスケーリングのオプションを備えた、マルチテナント インフラストラクチャ上で実行されるプラン オプションから選択できます。
    * [従量課金プラン](consumption-plan.md)。負荷に応じて動的なスケーリングが行われ、最小限のネットワークの分離オプションが提供されます。
    * [Premium プラン](functions-premium-plan.md)。やはり動的なスケーリングが行われますが、より包括的なネットワークの分離が提供されます。
    * [App Service プラン](dedicated-plan.md)。固定されたスケールで動作し、Premium プランと同様のネットワークの分離が提供されます。
* [App Service Environment](../app-service/environment/intro.md) で関数を実行できます。 この方法では、関数を仮想ネットワークにデプロイし、完全なネットワーク制御と分離を提供します。

## <a name="matrix-of-networking-features"></a>ネットワーク機能のマトリックス

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>受信アクセス制限

アクセス制限を使用すると、アプリへのアクセスを許可または拒否される IP アドレスの優先順位付きリストを定義できます。 この一覧には、IPv4 と IPv6 のアドレス、または[サービス エンドポイント](#use-service-endpoints)を使用する特定の仮想ネットワーク サブネットを含めることができます。 1 つ以上のエントリがある場合、リストの最後にあるものは暗黙的に "すべて拒否" になります。 IP 制限は、すべての関数ホスティング オプションで有効です。

アクセス制限は、[Premium](functions-premium-plan.md)、[従量課金](consumption-plan.md)、[App Service](dedicated-plan.md) で利用できます。

> [!NOTE]
> ネットワーク制限が適用されると、仮想ネットワーク内からか、または Azure portal へのアクセスに使用しているコンピューターの IP アドレスを [信頼された宛先のリスト] に入れている場合にのみ、デプロイを行うことができます。 ただし、ポータルを使用して関数を管理することもできます。

詳細については、「[Azure App Service の静的なアクセス制限](../app-service/app-service-ip-restrictions.md)」を参照してください。

## <a name="private-endpoint-connections"></a>プライベート エンドポイント接続

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

ストレージやサービス バスなどのプライベート エンドポイント接続を持つその他のサービスを呼び出すには、必ず[プライベート エンドポイントへの送信呼び出し](#private-endpoints)を行うようにアプリを構成してください。

### <a name="use-service-endpoints"></a>サービス エンドポイントの使用

サービス エンドポイントを使用することで、選択した Azure 仮想ネットワーク サブネットへのアクセスを制限できます。 特定のサブネットへのアクセスを制限するには、種類が **仮想ネットワーク** である制限規則を作成します。 その後、アクセスを許可または拒否するサブスクリプション、仮想ネットワーク、およびサブネットを選択できます。 

選択したサブネットのサービス エンドポイントが Microsoft.Web でまだ有効になっていない場合は、自動的に有効になります。ただし、 **[見つからない Microsoft.Web サービス エンドポイントを無視する]** チェック ボックスをオンにしていない場合に限ります。 サービス エンドポイントをアプリで有効にしてサブネットでは有効にしないというシナリオは、主としてサブネット上でそれらを有効にするためのアクセス許可があるかどうかに依存します。 

サブネット上でサービス エンドポイントを有効にするために他のユーザーが必要な場合は、 **[見つからない Microsoft.Web サービス エンドポイントを無視する]** チェック ボックスをオンにします。 アプリは、サービス エンドポイントが後からサブネット上で有効にされることを想定して構成されます。 

![種類として仮想ネットワークが選択された [IP 制限の追加] ウィンドウのスクリーンショット。](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

App Service Environment で実行されているアプリへのアクセスを制限するために、サービス エンドポイントを使うことはできません。 アプリが App Service Environment 内にあるときは、IP アクセス規則を適用することでアプリへのアクセスを制御できます。 

サービス エンドポイントを設定する方法については、[Azure Functions のプライベート サイト アクセスの設定](functions-create-private-site-access.md)に関するページ参照してください。

## <a name="virtual-network-integration"></a>仮想ネットワークの統合

仮想ネットワーク統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできます。
Azure Functions では、2 種類の仮想ネットワーク統合がサポートされています。

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions の仮想ネットワーク統合では、App Service Web アプリと共有インフラストラクチャを使用します。 2 種類の仮想ネットワーク統合の詳細については、次を参照してください。

* [リージョンでの仮想ネットワーク統合](../app-service/overview-vnet-integration.md#regional-virtual-network-integration)
* [ゲートウェイが必要な仮想ネットワーク統合](../app-service/overview-vnet-integration.md#gateway-required-virtual-network-integration)

仮想ネットワーク統合を設定する方法については、[Vnet 統合の有効化](#enable-vnet-integration)に関する記事を参照してください。

## <a name="enable-vnet-integration"></a>VNET 統合を有効にする

1. 関数アプリ ポータルの **[ネットワーク]** ブレードに移動します。 **[VNet 統合]** の下の **[ここをクリックして構成]** を選択します。

1. **[Add VNet]\(VNet の追加)** を選択します。

    :::image type="content" source="./media/functions-networking-options/vnet-int-function-app.png" alt-text="Vnet 統合を選択する":::

1. ドロップダウン リストには、お使いのサブスクリプションで同じリージョンに存在するすべての Azure Resource Manager 仮想ネットワークが含まれます。 統合する VNet を選択します。

    :::image type="content" source="./media/functions-networking-options/vnet-int-add-vnet-function-app.png" alt-text="VNet の選択":::

    * Functions の Premium プランでは、リージョン VNet 統合のみがサポートされています。 同じリージョン内の VNet の場合は、新しいサブネットを作成するか、空の既存のサブネットを選択します。
    * 別のリージョンの VNet を選択するには、ポイント対サイトが有効になっている VNet ゲートウェイがプロビジョニングされている必要があります。 リージョン間の VNet 統合は、Dedicated プランでのみサポートされています。

統合中にアプリは再起動されます。 統合が完了すると、統合されている VNet の詳細が表示されます。 既定では、[すべてをルーティング] が有効になり、すべてのトラフィックが VNet にルーティングされます。

プライベート トラフィック ([RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3) トラフィック) のみをルーティングする場合は、[App Service のドキュメント](../app-service/overview-vnet-integration.md#application-routing)に記載されている手順に従ってください。

## <a name="regional-virtual-network-integration"></a>リージョンでの仮想ネットワーク統合

リージョン VNet 統合を使用すると、アプリは次のものにアクセスできるようになります。

* アプリと同じリージョンにある VNet 内のリソース。
* VNet 内のリソースは、アプリが統合されている VNet とピアリングされます。
* サービス エンドポイントでセキュリティ保護されたサービス。
* Azure ExpressRoute 接続にまたがるリソース。
* 統合されている VNet 内のリソース。
* Azure ExpressRoute 接続を含む、ピアリングされた接続にまたがるリソース。
* プライベート エンドポイント 

同じリージョンの VNet との VNet 統合を使用する場合は、次の Azure ネットワーク機能を使用できます。

* **ネットワーク セキュリティ グループ (NSG)** :統合サブネットに配置された NSG を使って送信トラフィックをブロックできます。 VNet 統合を使ってアプリへの受信アクセスを提供できないため、受信規則は適用されません。
* **ルート テーブル (UDR)** :統合サブネット上にルート テーブルを配置して、必要な場所に送信トラフィックを送信できます。

> [!NOTE]
> すべての送信トラフィックを VNet にルーティングする場合は、統合サブネットに適用されている NSG と UDR に従います。 VNet に統合されている場合、トラフィックを他の場所に送信するルートを指定しない限り、パブリック IP アドレスへの関数アプリの送信トラフィックは、引き続きアプリのプロパティにリストされているアドレスから送信されます。
> 
> リージョン VNet 統合では、ポート 25 を使用できません。

同じリージョンの VNet との VNet 統合を使用する場合、いくつかの制限があります。

* グローバル ピアリング接続にまたがるリソースには到達できません。
* この機能は、Premium V2 と Premium V3 のすべての App Service スケール ユニットから使用できます。 また、Standard でも使用できますが、新しい App Service のスケール ユニットからのみ利用できます。 以前のスケール ユニットを使用している場合は、Premium V2 App Service プランの機能のみを使用できます。 Standard App Service プランでこの機能を使用できるようにするには、Premium V3 App Service プランでアプリを作成します。 それらのプランは、最新のスケール ユニットでのみサポートされます。 その後、必要に応じてスケールダウンできます。
* 統合サブネットは、1 つの App Service プランでしか使用できません。
* この機能は、App Service Environment にある Isolated プランのアプリでは使用できません。
* この機能には、Azure Resource Manager VNet 内に /28 以上の未使用のサブネットが必要です。
* アプリと VNet は同じリージョンに存在する必要があります。
* 統合アプリで VNet を削除することはできません。 VNet を削除する前に、統合を削除してください。
* App Service プランごとに 1 リージョンの VNet 統合のみを持つことができます。 同じ App Service プラン内の複数のアプリが同じ VNet を使用できます。
* リージョン VNet 統合を使用しているアプリがあるときに、アプリまたはプランのサブスクリプションを変更することはできません。
* アプリでは、構成を変更せずに Azure DNS Private Zones のアドレスを解決することはできません。

## <a name="subnets"></a>サブネット

VNet 統合は、専用サブネットに依存します。 サブネットをプロビジョニングすると、Azure サブネットは先頭から 5 つの IP を失います。 プラン インスタンスごとに、統合サブネットから 1 つのアドレスが使用されます。 アプリを 4 つのインスタンスにスケールする場合は、4 つのアドレスが使用されます。 

サイズをスケールアップまたはスケールダウンすると、必要なアドレス空間が短期間に 2 倍になります。 これは、特定のサブネット サイズでサポートされる実際の使用可能なインスタンスに影響します。 次の表に、CIDR ブロックあたりの使用可能アドレスの最大数と、これが水平スケールに与える影響を示します。

| CIDR ブロック サイズ | 使用可能なアドレスの最大数 | 水平スケールの最大値 (インスタンス)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>ある時点でサイズまたは SKU のいずれかでスケールアップまたはスケールダウンする必要があることを前提としています。 

割り当てた後はサブネット サイズを変更できないため、アプリが到達する可能性のあるスケールに対応できるだけの十分な大きさを持つサブネットを使用してください。 Functions の Premium プランのサブネット容量に関する問題を回避するには、Windows では /24 で 256 アドレス、Linux では /26 で 64 アドレスを使用する必要があります。 

別のプラン内のご自身のアプリが、別のプラン内のアプリから既に接続されている VNet にアクセスできるようにしたい場合は、既存の VNet 統合によって使用されているものとは異なるサブネットを選択します。

この機能は、[カスタム コンテナー](../app-service/configure-custom-container.md)を含め、Windows と Linux の両方のアプリで完全にサポートされています。 すべての動作は、Windows アプリと Linux アプリ間で同じです。

### <a name="service-endpoints"></a>サービス エンドポイント

より高度なセキュリティを実現するために、サービス エンドポイントを使用して、仮想ネットワークに対する Azure サービス数を制限することができます。 リージョン VNet 統合を使用すると、サービス エンドポイントで保護されている Azure サービスに関数アプリから接続できます。 この構成は、仮想ネットワークの統合をサポートするすべての[計画](functions-scale.md#networking-features)でサポートされています。 サービス エンドポイントで保護されたサービスにアクセスするには、次の操作を行う必要があります。

1. 特定のサブネットに接続するために、関数アプリとのリージョン VNet 統合を構成します。
1. 対象のサービスに移動し、統合サブネットに対してサービス エンドポイントを構成します。

詳細については、「[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)」を参照してください。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループを使用して、VNet 内のリソースへの受信および送信トラフィックをブロックできます。 リージョン VNet 統合を使用するアプリでは、[ネットワーク セキュリティ グループ][VNETnsg]を使用して、VNet 内またはインターネット上のリソースへの送信トラフィックをブロックできます。 パブリック アドレスへのトラフィックをブロックするには、[すべてをルーティング] が有効になっている VNet 統合を使用する必要があります。 VNet 統合はアプリからの送信トラフィックのみに影響を与えるため、NSG での受信規則はアプリに適用されません。

アプリへの受信トラフィックを制御するには、アクセス制限機能を使用します。 統合サブネットに適用される NSG は、統合サブネットに適用されているルートに関係なく有効になります。 [すべてをルーティング] が有効になっている VNet に関数アプリが統合されていて、統合サブネット上でパブリック アドレス トラフィックに影響を与えるルートがない場合、すべての送信トラフィックは引き続き、統合サブネットに割り当てられた NSG に従います。 [すべてをルーティング] が有効になっていない場合、NSG は RFC1918 トラフィックにのみ適用されます。

### <a name="routes"></a>ルート

ルート テーブルを使用して、アプリからの送信トラフィックを任意の場所にルーティングすることができます。 既定では、ルート テーブルは、RFC1918 の送信先トラフィックのみに影響を与えます。 [すべてをルーティング] が有効になっている場合、すべての送信呼び出しが影響を受けます。 [[すべてをルーティング]](../app-service/overview-vnet-integration.md#application-routing) が無効になっている場合、ルート テーブルの影響を受けるのはプライベート トラフィック (RFC1918) のみです。 統合サブネット上に設定されているルートは、受信アプリ要求への応答には影響を与えません。 一般的な宛先には、ファイアウォール デバイスまたはゲートウェイを含めることができます。

オンプレミスのすべての送信トラフィックをルーティングする場合は、ルート テーブルを使用して、すべての送信トラフィックを ExpressRoute ゲートウェイに送信できます。 ゲートウェイにトラフィックをルーティングする場合は、外部ネットワークのルートを設定して、応答を返信するようにしてください。

また、Border Gateway Protocol (BGP) ルートも、アプリのトラフィックに影響を与えます。 ExpressRoute ゲートウェイのようなものから BGP ルートを使用している場合は、アプリの送信トラフィックが影響を受けます。 既定では、BGP ルートは、RFC1918 の送信先トラフィックにのみ影響を与えます。 [すべてをルーティング] が有効になっている VNet に関数アプリが統合されている場合、すべての送信トラフィックは BGP ルートの影響を受ける可能性があります。

### <a name="azure-dns-private-zones"></a>Azure DNS プライベート ゾーン 

アプリでは、VNet に統合された後、VNet が構成されているのと同じ DNS サーバーが使用されます。 既定では、アプリは Azure DNS プライベート ゾーンで動作しません。 Azure DNS プライベート ゾーンで動作させるには、次のアプリ設定を追加する必要があります。

- `WEBSITE_VNET_ROUTE_ALL` (値 `1`)

これらの設定により、アプリからのすべての送信呼び出しが VNet に送信され、アプリから Azure DNS プライベート ゾーンにアクセスできるようになります。 これらの設定を使用すると、アプリは、ワーカー レベルで DNS プライベート ゾーンを照会することによって、Azure DNS を使用できます。  

### <a name="private-endpoints"></a>プライベート エンドポイント

[プライベート エンドポイント][privateendpoints]を呼び出す場合は、DNS 参照がプライベート エンドポイントに解決されるようにする必要があります。 この動作は、次のいずれかの方法で実現できます。 

* Azure DNS プライベート ゾーンと統合する。 VNet にカスタム DNS サーバーがない場合、これは自動的に行われます。
* アプリで使用される DNS サーバーでプライベート エンドポイントを管理する。 これを行うには、プライベート エンドポイント アドレスを把握し、到達しようとしているエンドポイントが A レコードを使用してそのアドレスにポイントされるようにします。
* [Azure DNS プライベート ゾーン](#azure-dns-private-zones)に転送する独自の DNS サーバーを構成する。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>お使いのストレージ アカウントを仮想ネットワークに制限する 

関数アプリを作成するときは、BLOB、Queue、および Table Storage をサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 このストレージ アカウントは、サービス エンドポイントまたはプライベート エンドポイントで保護されているものに置き換えることができます。 

この機能は、専用 (App Service) プランおよび Premium プランのすべての Windows 仮想ネットワーク対応 SKU でサポートされています。 それは、Linux 仮想ネットワークでサポートされている SKU に対してもプライベート DNS によってサポートされています。 Linux プランでの従量課金プランとカスタム DNS はサポートされていません。 プライベート ネットワークに制限されたストレージ アカウントを使用して関数を設定する方法については、「[お使いのストレージ アカウントを仮想ネットワークに制限する](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network)」を参照してください。

## <a name="use-key-vault-references"></a>Key Vault 参照を使用する

Azure Key Vault 参照を使用すると、コードの変更を必要とせずに、Azure Functions アプリケーションで Azure Key Vault のシークレットを使用することができます。 Azure Key Vault は、アクセス ポリシーと監査履歴を完全制御する、一元化されたシークレット管理を提供するサービスです。

アプリに対して仮想ネットワーク統合が構成されている場合、[Key Vault 参照](../app-service/app-service-key-vault-references.md)を使用して、ネットワークで制限されたコンテナーのシークレットを取得することができます。

## <a name="virtual-network-triggers-non-http"></a>仮想ネットワーク トリガー (非 HTTP)

現時点では、次の 2 つの方法のいずれかで、仮想ネットワーク内から非 HTTP トリガー関数を使用できます。

+ Premium プランで関数アプリを実行し、仮想ネットワーク トリガーのサポートを有効にする。
+ App Service プランまたは App Service 環境で関数アプリを実行する。

### <a name="premium-plan-with-virtual-network-triggers"></a>仮想ネットワーク トリガーを使用した Premium プラン

Premium プランを実行する場合は、仮想ネットワーク内で実行されているサービスに非 HTTP トリガー関数を接続できます。 これを行うには、関数アプリの仮想ネットワーク トリガーのサポートを有効にする必要があります。 **[Runtime Scale Monitoring]\(ランタイム スケールの監視\)** の設定は、[Azure portal](https://portal.azure.com) の **[構成]**  >  **[関数のランタイム設定]** にあります。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

次の Azure CLI コマンドを使用して、仮想ネットワーク トリガーを有効にすることもできます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> 仮想ネットワーク トリガーを有効にすると、アプリケーションのパフォーマンスに影響することがあります。これは、App Service プランのインスタンスがトリガーを監視して、スケーリングのタイミングを判断する必要があるためです。 この影響は非常に小さい場合がほとんどです。

仮想ネットワーク トリガーは、バージョン 2.x 以降の Functions ランタイムでサポートされています。 次の非 HTTP トリガーの種類がサポートされています。

| 拡張機能 | 最小バージョン |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 以降 |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 以降|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 以降|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 以降|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 以降|

> [!IMPORTANT]
> 仮想ネットワーク トリガーのサポートを有効にすると、上記の表に示されたトリガーの種類のみが、アプリケーションで動的にスケーリングされます。 表に示されていないトリガーも使用できますが、事前ウォーミングされたインスタンス数を超えてスケーリングされることはありません。 トリガーの全一覧については、[トリガーとバインド](./functions-triggers-bindings.md#supported-bindings)に関する記事を参照してください。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>仮想ネットワーク トリガーを使用した App Service プランと App Service Environment

関数アプリを App Service プランまたは App Service Environment のいずれかで実行する場合は、非 HTTP トリガー関数を使用できます。 関数が正しくトリガーされるようにするには、トリガー接続で定義されているリソースにアクセスできる仮想ネットワークに接続する必要があります。

たとえば、仮想ネットワークからのみトラフィックを受け入れるように Azure Cosmos DB を構成するとします。 この場合、その仮想ネットワークとの仮想ネットワーク統合を提供する App Service プランで関数アプリをデプロイする必要があります。 統合により、該当の Azure Cosmos DB リソースによって関数がトリガーされるようになります。

## <a name="hybrid-connections"></a>ハイブリッド接続

[ハイブリッド接続](../azure-relay/relay-hybrid-connections-protocol.md)は、他のネットワークのアプリケーション リソースにアクセスするために使用できる Azure Relay の機能です。 アプリからアプリケーション エンドポイントにアクセスできます。 アプリケーションにアクセスするために使用することはできません。 ハイブリッド接続は、Windows で実行されている、従量課金プラン以外のすべての関数に使用できます。

Azure Functions で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 つまり、TCP リッスン ポートにアクセスしている限り、任意のオペレーティング システムの任意のアプリケーションがハイブリッド接続のエンドポイントになることができます。 ハイブリッド接続機能では、アプリケーション プロトコルやアクセス先は認識されません。 ネットワーク アクセスを提供するだけです。

詳細については、[ハイブリッド接続に関する App Service ドキュメント](../app-service/app-service-hybrid-connections.md)を参照してください。 これらの同じ構成手順を Azure Functions に使用できます。

>[!IMPORTANT]
> ハイブリッド接続は、Windows プランでのみサポートされています。 Linux はサポートされていません。

## <a name="outbound-ip-restrictions"></a>送信 IP の制限

送信 IP の制限は、Premium プラン、App Service プラン、App Service Environment で利用できます。 App Service Environment が展開されている仮想ネットワークの送信制限を構成することができます。

Premium プランまたは App Service プランの関数アプリを仮想ネットワークと統合した場合でも、アプリは既定でインターネットへの送信呼び出しを行うことができます。 [すべてをルーティング] が有効になっている VNet と関数アプリを統合することにより、すべての送信トラフィックが仮想ネットワークに強制的に送信されます。この場合は、ネットワーク セキュリティ グループの規則を使用してトラフィックを制限できます。

仮想ネットワークを使用して送信 IP を制御する方法については、[Azure 仮想ネットワークの NAT ゲートウェイを使用した Azure Functions の送信 IP 制御に関するチュートリアル](functions-how-to-use-nat-gateway.md)を参照してください。 

## <a name="automation"></a>オートメーション
次の API では、リージョンでの仮想ネットワーク統合をプログラミングで管理できます。

+ **Azure CLI**:[`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) コマンドを使用し、リージョンでの仮想ネットワーク統合を追加、一覧表示、または削除します。  
+ **ARM テンプレート**:リージョンでの仮想ネットワーク統合は、Azure Resource Manager テンプレートを使用することで有効にできます。 完全な例については、[こちらの関数クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/) ページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ

ネットワークと Azure Functions の詳細については、以下を参照してください。

* [仮想ネットワーク統合の概要に関するチュートリアル](./functions-create-vnet.md)
* [関数のネットワークに関する FAQ](./functions-networking-faq.yml)
* [仮想ネットワーク統合と App Service/Functions に関する詳細情報](../app-service/overview-vnet-integration.md)
* [Azure の仮想ネットワークに関する詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
* [ハイブリッド接続を使用して、ファイアウォールを変更せずに個々のオンプレミス リソースに接続する](../app-service/app-service-hybrid-connections.md)


<!--Links-->
[VNETnsg]: ../virtual-network/network-security-groups-overview.md
[privateendpoints]: ../app-service/networking/private-endpoint.md