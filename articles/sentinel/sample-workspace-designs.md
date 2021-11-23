---
title: Microsoft Sentinel のサンプル ワークスペースの設計 | Microsoft Docs
description: 複数のテナント、クラウド、リージョンを使用した Microsoft Sentinel アーキテクチャの設計サンプルから学習します。
services: sentinel
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2c23760aa98f2930f10524a4920d27df1dc05d25
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712038"
---
# <a name="microsoft-sentinel-sample-workspace-designs"></a>Microsoft Sentinel のサンプル ワークスペースの設計

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、次のサンプル要件がある組織に推奨されるワークスペースの設計について説明します。

- 複数のテナントとリージョン。欧州データ主権の要件付き
- 複数のクラウドがあるシングル テナント
- 複数のリージョンがあり一元化されたセキュリティを備えた複数のテナント

この記事のサンプルでは、[Microsoft Sentinel ワークスペース設計のデシジョン ツリー](design-your-workspace-architecture.md)を使用して、各組織に最適なワークスペース設計を決定します。 詳細については、「[Microsoft Sentinel ワークスペース アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)」を参照してください。

## <a name="sample-1-multiple-tenants-and-regions"></a>サンプル 1: 複数のテナントとリージョン

Contoso Corporation は、ロンドンに本社を置く多国籍企業です。 Contoso は世界中にオフィスを持ち、ニューヨーク市 (NYC) と東京に重要なハブがあります。 最近、Contoso は生産性スイートを Office 365 に移行し、多くのワークロードが Azure に移されました。

### <a name="contoso-tenants"></a>Contoso のテナント

数年前の買収により、Contoso には 2 つの Azure AD テナント、`contoso.onmicrosoft.com` と `wingtip.onmicrosoft.com` があります。 次の図に示すように、それぞれのテナントに、独自の Office 365 インスタンスと複数の Azure サブスクリプションがあります。

:::image type="content" source="media/best-practices/contoso-tenants.png" alt-text="それぞれに個別のサブスクリプション セットがある Contoso のテナントの図。" border="false":::

### <a name="contoso-compliance-and-regional-deployment"></a>Contoso のコンプライアンスとリージョン デプロイ

Contoso は現在、Azure リソースを、米国東部、北ヨーロッパ、西日本の 3 つの異なるリージョンでホストしています。また、ヨーロッパで生成されたすべてのデータを、ヨーロッパ リージョン内で保持するという厳格な要件があります。

Contoso の Azure AD テナント両方に、米国東部、北ヨーロッパ、西日本の 3 つのリージョンすべてのリソースがあります

### <a name="contoso-resource-types-and-collection-requirements"></a>Contoso のリソースの種類とコレクションの要件

Contoso は、次のデータ ソースからイベントを収集する必要があります。

-   Office 365
-   Azure AD のサインイン ログと監査ログ
-   Azure アクティビティ
-   オンプレミス ソースと Azure VM ソースの両方からの Windows セキュリティ イベント
-   オンプレミス ソースと Azure VM ソースの両方からの Syslog
-   複数のオンプレミス ネットワーク デバイス (Palo Alto、Cisco ASA、Cisco Meraki など) からの CEF
-   複数の Azure PaaS リソース (Azure Firewall、AKS、Key Vault、Azure Storage、Azure SQL など)
-   Cisco Umbrella

Azure VM は主に北ヨーロッパ リージョンに配置され、米国東部と西日本にあるのはわずかです。 Contoso は、同社のすべての Azure VM 上のサーバーに対して、Microsoft Defender を使用しています。

Contoso は、同社のすべてのデータ ソースから 1 日あたり約 300 GB を取り込むことを想定しています。

### <a name="contoso-access-requirements"></a>Contoso のアクセス要件

Contoso の Azure 環境には Log Analytics ワークスペースが既に 1 つ存在しており、インフラストラクチャを監視するために運用チームが使用しています。 このワークスペースは、北ヨーロッパ リージョン内の Contoso AAD テナントに配置され、すべてのリージョン内の Azure VM からログを収集するために使用されています。 現在、1 日あたり約 50 GB が取り込まれています。

Contoso 運用チームは、現在ワークスペースにあるすべてのログにアクセスできる必要があり、これには、**Perf**、**InsightsMetrics**、**ContainerLog** など、SOC が必要としないデータ型が複数含まれています。 運用チームは、Microsoft Sentinel 内で収集される新しいログには、アクセス "*しないでください*"。

### <a name="contosos-solution"></a>Contoso のソリューション

次の手順では、[Microsoft Sentinel ワークスペース設計のデシジョン ツリー](design-your-workspace-architecture.md)を適用して、Contoso に最適なワークスペース設計を決定します。

1. Contoso には既に既存のワークスペースが存在するため、そのワークスペースで Microsoft Sentinel を有効にする方法を探すことができます。

    SOC 以外のデータ インジェストは 1 日あたり 100 GB 未満なので、[手順 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) に進み、[手順 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) で関連するオプションを確実に選択します。

1.  Contoso には規制要件があるため、ヨーロッパ内には Microsoft Sentinel ワークスペースが少なくとも 1 つ必要です。

1.  Contoso には 2 つの異なる Azure AD テナントがあり、Office 365 や Azure AD のサインイン ログや監査ログなど、テナントレベルのデータ ソースから収集を行います。このため、テナントごとに少なくとも 1 つのワークスペースが必要です。

1.  Contoso は[チャージバック](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back)する必要がないため、[手順 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) を続行できます。

1.  Contoso は SOC 以外のデータを収集する必要がありますが、SOC データと SOC 以外のデータは重複しません。 また、SOC データは 1 日あたり約 250 GB を使用するため、コスト効率を考えて個別のワークスペースを使用する必要があります。

1.  Contoso の VM のほとんどが北ヨーロッパ リージョンであり、既にワークスペースがあります。 したがって、このケースでは、帯域幅のコストを気にする必要はありません。

1.  Contoso で Microsoft Sentinel を使用する SOC チームは 1 つなので、追加の分離は不要です。

1.  Contoso の SOC チームのメンバーすべてが、すべてのデータにアクセスするため、追加の分離は不要です。

結果として、Contoso の Microsoft Sentinel ワークスペースの設計は、次の図のようになります。

:::image type="content" source="media/best-practices/contoso-solution.png" alt-text="運用チーム用に個別のワークスペースがある Contoso のソリューションの図。" border="false":::

推奨されるソリューションには以下が含まれます。

- Contoso 運用チーム用の個別の Log Analytics ワークスペース。 このワークスペースには、**Perf** テーブル、**InsightsMetrics** テーブル、**ContainerLog** テーブルなど、Contoso の SOC チームには必要ないデータのみが格納されます。

- 2 つの Microsoft Sentinel ワークスペース (Azure AD テナントごとに 1 つ)。Office 365、Azure アクティビティ、Azure AD など、すべての Azure PaaS サービスからデータを取り込みます。

- オンプレミスのデータ ソースから取得されるその他のすべてのデータ。2 つの Microsoft Sentinel ワークスペースのいずれかにルーティングできます。


## <a name="sample-2-single-tenant-with-multiple-clouds"></a>サンプル 2: 複数のクラウドがあるシングル テナント

Fabrikam は、ニューヨーク市に本社を置く組織で、米国全土にオフィスがあります。 Fabrikam は、クラウド導入の取り組みを開始したばかりで、最初の Azure ランディング ゾーンをデプロイし、最初のワークロードを移行する必要があります。 Fabrikam には既に AWS 上にワークロードがいくつかあります。これは、Microsoft Sentinel を使って監視する予定です。

### <a name="fabrikam-tenancy-requirements"></a>Fabrikam のテナントの要件

Fabrikam には、1 つの Azure AD があります。

### <a name="fabrikam-compliance-and-regional-deployment"></a>Fabrikam のコンプライアンスとリージョン デプロイ

Fabrikam にはコンプライアンス要件はありません。 Fabrikam のリソースは、米国の複数の Azure リージョンにありますが、帯域幅のコストは大きな懸念事項ではありません。

### <a name="fabrikam-resource-types-and-collection-requirements"></a>Fabrikam のリソースの種類とコレクションの要件

Fabrikam は、次のデータ ソースからイベントを収集する必要があります。

-   Azure AD のサインイン ログと監査ログ
-   Azure アクティビティ
-   オンプレミス ソースと Azure VM ソースの両方からのセキュリティ イベント
-   オンプレミス ソースと Azure VM ソースの両方からの Windows イベント
-   オンプレミス ソースと Azure VM ソースの両方からのパフォーマンス データ
-   AWS CloudTrail
-   AKS の監査ログとパフォーマンス ログ

### <a name="fabrikam-access-requirements"></a>Fabrikam のアクセス要件

Fabrikam 運用チームは、以下にアクセスする必要があります。

-   オンプレミス ソースと Azure VM ソースの両方からのセキュリティ イベントと Windows イベント
-   オンプレミス ソースと Azure VM ソースの両方からのパフォーマンス データ
-   AKS パフォーマンス (コンテナー分析情報) と監査ログ
-   すべての Azure アクティビティ データ

Fabrikam SOC チームは、以下にアクセスする必要があります。
-   Azure AD のサインイン ログと監査ログ
-   すべての Azure アクティビティ データ
-   オンプレミス ソースと Azure VM ソースの両方からのセキュリティ イベント
-   AWS CloudTrail ログ
-   AKS 監査ログ
-   完全な Microsoft Sentinel ポータル

### <a name="fabrikams-solution"></a>Fabrikam のソリューション

次の手順では、[Microsoft Sentinel ワークスペース設計のデシジョン ツリー](design-your-workspace-architecture.md)を適用して、Fabrikam に最適なワークスペース設計を決定します。

1.  Fabrikam には既存のワークスペースがないので、[手順 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) に進みます。

1.  Fabrikam には規制要件がないので、[手順 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants) に進みます。

1.  Fabrikam はシングルテナント環境なので、 [手順 4](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back) に進みます。

1.  Fabrikam では料金を分割する必要がないので、[手順 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) に進みます。

1.  Fabrikam では、SOC チームと運用チームに個別のワークスペースが必要です。

    Fabrikam 運用チームは、VM と AKS の両方からパフォーマンス データを収集する必要があります。 AKS は診断設定に基づくため、特定のログを選択して、特定のワークスペースに送信することができます。 Fabrikam は、AKS 監査ログを Microsoft Sentinel ワークスペースに送信し、すべての AKS ログを、Microsoft Sentinel が有効になっていない別のワークスペースに送信することを選択できます。 Microsoft Sentinel が有効になっていないワークスペースでは、Fabrikam はコンテナー分析情報のソリューションを有効にします。

    Windows VM の場合、Fabrikam は、[Azure Monitoring Agent (AMA)](connect-windows-security-events.md#connector-options) を使用してログを分割し、Microsoft Sentinel ワークスペースにはセキュリティ イベントを、Microsoft Sentinel がないワークスペースにはパフォーマンス イベントと Windows イベントを送信できます。

    Fabrikam は、セキュリティ イベントや Azure アクティビティ イベントなどの重複するデータを SOC データのみと見なし、このデータを Microsoft Sentinel を使用してワークスペースに送信します。

1.  帯域幅のコストは Fabrikam には大きな懸念事項ではないので、[手順 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership) に進みます。

1.  Fabrikam は既に、SOC チームと運用チームに個別のワークスペースを使用することを決めています。 さらなる分離の必要はありません。

1.  Fabrikam は、セキュリティ イベントや Azure アクティビティ イベントなどの重複するデータへのアクセスを制御する必要がありますが、行レベルの要件はありません。

    セキュリティ イベントも Azure アクティビティ イベントもカスタム ログではありません。したがって、Fabrikam はテーブルレベルの RBAC を使用して、運用チームに対してその 2 つのテーブルへのアクセスを許可することができます。

結果として、Fabrikam の Microsoft Sentinel ワークスペースの設計は、次の図のようになります。設計をシンプルにするため、この図には重要なログ ソースのみが含まれています。

:::image type="content" source="media/best-practices/fabrikam-solution.png" alt-text="運用チーム用に個別のワークスペースがある Fabrikam のソリューションの図。" border="false" :::

推奨されるソリューションには以下が含まれます。

- 米国リージョン内の 2 つの個別のワークスペース: Microsoft Sentinel が有効になっている SOC チーム用と、Microsoft Sentinel がない運用チーム用。

- [Azure Monitoring Agent (AMA)](connect-windows-security-events.md#connector-options)。Azure VM とオンプレミス VM から各ワークスペースに、どのログが送信されるようにするかを決めるときに使用されます。

- 診断設定。AKS などの Azure リソースから各ワークスペースに、どのログが送信されるようにするかを決めるときに使用されます。

- Microsoft Sentinel ワークスペースに送信される重複データ。必要に応じて、テーブルレベルの RBAC を使用して運用チームにアクセスを許可します。

## <a name="sample-3-multiple-tenants-and-regions-and-centralized-security"></a>サンプル 3: 複数のテナントとリージョン、および一元化されたセキュリティ

Adventure Works は、東京に本社を置く多国籍企業です。 Adventure Works には 10 の異なるサブエンティティがあり、これらは世界中のさまざまな国に基づいています。

Adventure Works は Microsoft 365 E5 を使っており、既に Azure にワークロードがあります。

### <a name="adventure-works-tenancy-requirements"></a>Adventure Works のテナントの要件

Adventure Works には、3 つの異なる Azure AD テナントが大陸ごとに 1 つあり、それぞれにアジア、ヨーロッパ、アフリカというサブエンティティがあります。 それぞれのサブエンティティの国の ID は、その国が所属する大陸のテナント内にあります。 たとえば、日本のユーザーは "*アジア*" テナントに、ドイツのユーザーは "*ヨーロッパ*" テナントに、エジプトのユーザーは "*アフリカ*" テナントに含まれます。

### <a name="adventure-works-compliance-and-regional-requirements"></a>Adventure Works のコンプライアンスとリージョンの要件

Adventure Works は現在、サブエンティティが存在する大陸に応じて、3 つの Azure リージョンを使用しています。 Adventure Works には、厳格なコンプライアンス要件はありません。

### <a name="adventure-works-resource-types-and-collection-requirements"></a>Adventure Works のリソースの種類とコレクションの要件

Adventure Works は、サブエンティティごとに次のデータ ソースを収集する必要があります。

-   Azure AD のサインイン ログと監査ログ
-   Office 365 のログ
-   Microsoft 365 Defender for Endpoint の生のログ
-   Azure アクティビティ
-   Microsoft Defender for Cloud
-   Azure PaaS リソース (Azure Firewall、Azure Storage、Azure SQL、Azure WAF など)
-   Azure VM からのセキュリティ イベントと Windows イベント
-   オンプレミス ネットワーク デバイスからの CEF ログ

Azure VM は 3 つの大陸に分散していますが、帯域幅のコストを気にする必要はありません。

### <a name="adventure-works-access-requirements"></a>Adventure Works のアクセス要件

Adventure Works では、一元化された 1 つの SOC チームが、さまざまなサブエンティティすべてのセキュリティ運用を監視しています。

また、Adventure Works には、3 つの独立した SOC チームが大陸ごとに 1 つあります。 各大陸の SOC チームが、そのリージョン内で生成されたデータにのみアクセスできるようにしてください。また、他の大陸からのデータが表示されないようにします。 たとえば、アジアの SOC チームは、アジアにデプロイされた Azure リソースからのデータ、アジアのテナントからの AAD サインイン、アジアのテナントからの Defender for Endpoint にのみアクセスする必要があります。

各大陸の SOC チームが、Microsoft Sentinel ポータル エクスペリエンス全体にアクセス必要があります。

Adventure Works の運用チームは独立して運営されており、Microsoft Sentinel を含まない独自のワークスペースがあります。

### <a name="adventure-works-solution"></a>Adventure Works のソリューション

次の手順では、[Microsoft Sentinel ワークスペース設計のデシジョン ツリー](design-your-workspace-architecture.md)を適用して、Adventure Works に最適なワークスペース設計を決定します。

1.  Adventure Works の運用チームには独自のワークスペースがあるので、[手順 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) に進みます。

1.  Adventure Works には規制要件がないので、[手順 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants) に進みます。

1.  Adventure Works には 3 つのAzure AD テナントがあり、Office 365 ログなどのテナントレベルのデータ ソースを収集する必要があります。 したがって、Adventure Works は、テナントごとに少なくとも 1 つの Microsoft Sentinel ワークスペースを作成する必要があります。

1.  Adventure Works では料金を分割する必要がないので、[手順 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) に進みます。

1.  Adventure Works の運用チームには独自のワークスペースがあるので、この決定で考慮されるすべてのデータが、Adventure Works SOC チームによって使用されます。

1.  帯域幅のコストは Adventure Works には大きな懸念事項ではないので、[手順 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership) に進みます。

1.  各コンテンツの SOC チームは、そのコンテンツに関連するデータにのみアクセスする必要があるため、Adventure Works は、データを所有権ごとに分離する必要があります。 ただし、各大陸の SOC チームも、Microsoft Sentinel ポータル全体にアクセスする必要があります。

1.  Adventure Works は、テーブルによるデータ アクセスを制御する必要はありません。

結果として、Adventure Works の Fabrikam の Microsoft Sentinel ワークスペースの設計は、次の図のようになります。設計をシンプルにするため、この図には重要なログ ソースのみが含まれています。

:::image type="content" source="media/best-practices/adventure-works-solution.png" alt-text="Azure AD テナントごとに個別のワークスペースがある Adventure Works のソリューションの図。" border="false":::

推奨されるソリューションには以下が含まれます。

- Azure AD テナントごとに個別の Microsoft Sentinel ワークスペース。 各ワークスペースによって、すべてのデータ ソースのそのテナントに関連するデータが収集されます。

- 各大陸の SOC チームは自身のテナント内のワークスペースにのみアクセスできます。これにより、各 SOC チームからは、テナント境界内で生成されたログにのみアクセスすることができます。

- 中央の SOC チームは、引き続き個別の Azure AD テナントから操作でき、Azure Lighthouse を使用して、さまざまな Microsoft Sentinel 環境にアクセスすることができます。 追加テナントがない場合、中央の SOC チームは引き続き Azure Lighthouse を使用して、リモート ワークスペースにアクセスすることができます。

- また、中央 SOC チームは、大陸 SOC チームには引き続き非表示の成果物を格納する必要がある場合、または大陸 SOC チームに関連しない他のデータを取り込む必要がある場合に、追加のワークスペースを作成することもできます。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Microsoft Sentinel のオンボード](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](get-visibility.md)
