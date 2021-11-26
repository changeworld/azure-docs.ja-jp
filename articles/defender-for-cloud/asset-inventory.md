---
title: クラウドの資産インベントリ用 Microsoft Defender
description: クラウドの監視対象リソースのすべての Defender を完全に可視化する、クラウドの資産管理エクスペリエンスに関する Microsoft Defender について説明します。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 11/09/2021
ms.service: defender-for-cloud
ms.topic: how-to
ms.openlocfilehash: b8732094b7b575faf97753b8e0e8a5c234f733d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525600"
---
# <a name="use-asset-inventory-to-manage-your-resources-security-posture"></a>資産インベントリを使用してリソースのセキュリティ態勢を管理する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud の [資産インベントリ] ページでは、Microsoft Defender for Cloud に接続したリソースのセキュリティ対策を表示するための1ページが提供されています。

Defender for Cloud は、サブスクリプションに接続されているリソースのセキュリティの状態を定期的に分析して、潜在的なセキュリティの脆弱性を特定します。 その後、これらの脆弱性を修正する方法に関する推奨事項が提供されます。

いずれかのリソースに未処理のレコメンデーションがある場合は、インベントリに表示されます。

このビューとそのフィルターを使用して、次のような質問に対処できます。

- 強化されたセキュリティ機能が有効になっているサブスクリプションのうち、推奨されるものはありますか?
- 「Production」というタグが付いているマシンのうち、Log Analytics エージェントがないものはどれか
- 特定のタグが付いているマシンのうち、未処理の推奨事項があるマシンは何台か
- (CVE 番号を使用して) 既知の脆弱性を持つ特定のリソース グループ内のマシンは何ですか?

このツールによる資産管理には大きな可能性があり、その範囲は拡大し続けています。

> [!TIP]
> 資産インベントリ ページのセキュリティに関する推奨事項は、**推奨事項** に関するページのセキュリティに関する推奨事項と同じですが、ここでは影響を受けるリソースに応じて示しています。 推奨事項を解決する方法の詳細については、「 [Microsoft Defender For Cloud でのセキュリティに関する推奨事項の実装](review-security-recommendations.md)」を参照してください。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|Free *<br>* インベントリ ページの一部の機能 ([ソフトウェア インベントリ](#access-a-software-inventory)など) には、有料のソリューションを用意する必要があります|
|必要なロールとアクセス許可:|すべてのユーザー|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="what-are-the-key-features-of-asset-inventory"></a>資産インベントリの主な機能

インベントリ ページには次のツールが含まれています。

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Microsoft Defender for Cloud の資産インベントリページの主な機能。" lightbox="media/asset-inventory/highlights-of-inventory.png":::

### <a name="1---summaries"></a>1 - 概要

フィルターを定義する前に、インベントリ ビューの上部に以下の値のストリップが目立つように表示されます。

- **リソースの合計**: クラウドの Defender に接続されているリソースの合計数。
- **異常なリソース**:アクティブなセキュリティのレコメンデーションがあるリソース。 セキュリティのレコメンデーションの詳細については、[こちら](review-security-recommendations.md)をご覧ください。
- **監視されていないリソース**:エージェントの監視に関して問題があるリソースです。Log Analytics エージェントがデプロイされていますが、エージェントがデータを送信していないか、正常性に関する他の問題が発生しています。
- **登録** されていないサブスクリプション: 選択したスコープ内のサブスクリプションのうち、Microsoft Defender for Cloud にまだ接続されていないものがあります。

### <a name="2---filters"></a>2 - フィルター

ページの上部にある複数のフィルターを使用すると、質問に回答しようとすることによって、リソースの一覧をすばやく絞り込むことができます。 たとえば、" *'Production' というタグが付いているマシンのうち、Log Analytics エージェントがないものはどれですか?* " という質問に回答する場合は、 **[エージェント監視]** フィルターと **[タグ]** フィルターを組み合わせることができます。

フィルターを適用すると、すぐに概要の値がクエリ結果に関連付けて更新されます。

### <a name="3---export-and-asset-management-tools"></a>3 - エクスポートと資産管理ツール

**エクスポート オプション** - インベントリには、選択したフィルター オプションの結果を CSV ファイルにエクスポートするオプションが含まれます。 クエリ自体を Azure Resource Graph エクスプローラーにエクスポートして、Kusto クエリ言語 (KQL) クエリを絞り込んだり、保存や変更を行ったりすることもできます。

> [!TIP]
> KQL のドキュメントでは、いくつかのサンプル データと単純なクエリを組み合わせたデータベースを使用して、この言語の「感触」をつかむことができます。 詳しくは、[KQL のチュートリアル](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)をご覧ください。

**資産管理オプション** - クエリに一致するリソースが見つかると、インベントリに次のような操作のショートカットが表示されます。

- フィルター処理されたリソースにタグを割り当てる - タグを付けるリソースの横にあるチェック ボックスをオンにします。
- クラウドの Defender に新しいサーバーを追加する- **[Azure 以外のサーバーの追加]** ツールバーボタンを使用します。
- Azure Logic Apps を使用してワークロードを自動化する - **[ロジック アプリのトリガー]** ボタンを使用して、1 つ以上のリソースでロジック アプリを実行します。 ロジック アプリを事前に準備し、関連するトリガーの種類 (HTTP 要求) を受け入れる必要があります。 [Logic Apps の詳細をご覧ください](../logic-apps/logic-apps-overview.md)。

## <a name="how-does-asset-inventory-work"></a>資産インベントリのしくみ

資産インベントリでは、 [azure Resource Graph (ARG)](../governance/resource-graph/index.yml)を利用しています。これは、複数のサブスクリプションでクラウドのセキュリティ体制データを Defender に照会する機能を提供する azure サービスです。

ARG は、大規模なクエリの実行機能によってリソースを効率的に探索できるように設計されています。

[Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/)を使用すると、資産インベントリは、他のリソースプロパティを使用してクラウドデータに対して相互参照 Defender を使用することによって、詳細な洞察を迅速に生み出すことができます。

## <a name="how-to-use-asset-inventory"></a>資産インベントリの使用方法

1. Defender for Cloud のサイドバーから、 **[インベントリ]** を選択します。

1. **[名前でフィルター]** ボックスを使用して特定のリソースを表示するか、下記のようにフィルターを使用します。

1. フィルターの関連するオプションを選択して、実行する特定のクエリを作成します。

    既定では、リソースはアクティブなセキュリティのレコメンデーションの数によって並べ替えられます。

    > [!IMPORTANT]
    > 各フィルターのオプションは、現在選択されているサブスクリプション内のリソース **および** 他のフィルターで選択した項目に固有のものです。
    >
    > たとえば、サブスクリプションを 1 つだけ選択していて、そのサブスクリプションに、修復すべき未処理のセキュリティの推奨事項があるリソースがない場合 (異常なリソースが 0 の場合)、 **[推奨事項]** フィルターにオプションは表示されません。

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="クラウドの資産インベントリに Microsoft Defender のフィルターオプションを使用して、監視されていない運用リソースに対してリソースをフィルター処理する":::

1. **[セキュリティの調査結果に含まれる内容]** フィルターを使用するには、脆弱性の検出結果の ID、セキュリティ チェック、または CVE 名からフリー テキストを入力して、影響を受けるリソースをフィルター処理します。

    ![[セキュリティの調査結果に含まれる内容] フィルター](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **[セキュリティの調査結果に含まれる内容]** と **[タグ]** フィルターには、1 つの値のみ指定できます。 複数のフィルターを使用してフィルター処理を行うには **[フィルターの追加]** を使用します。

1. **クラウドフィルターに Defender** を使用するには、1つまたは複数のオプション (Off、On、または Partial) を選択します。

    - **オフ** -Microsoft Defender プランによって保護されていないリソース。 これらのいずれかを右クリックしてアップグレードできます。

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="右クリックにより、関連する Microsoft Defender プランによって保護されるようにリソースをアップグレードします。" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - Microsoft Defender プランによって保護さ **れている** リソース
    - **Partial** -一部の Microsoft Defender プランが無効になっている **サブスクリプション** に適用されます。 たとえば、次のサブスクリプションでは、7つの Microsoft Defender プランが無効になっています。

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="サブスクリプションは Microsoft Defender プランによって部分的に保護されています。":::

1. クエリの結果をさらに調べるには、該当するリソースを選択します。

1. 現在選択されているフィルター オプションを、Resource Graph エクスプローラーでクエリとして表示するには、 **[クエリを開く]** を選択します。

    ![ARG でのインベントリ クエリ。](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 一部のフィルターを定義し、ページを開いたままにした場合、Defender for Cloud は結果を自動的に更新しません。 ページを手動で再読み込みするか、 **[更新]** を選択しない限り、リソースを変更しても表示される結果には影響しません。

## <a name="access-a-software-inventory"></a>ソフトウェア インベントリにアクセスする

Microsoft Defender for Endpoint との統合を有効にし、サーバーに対して Microsoft Defender を有効にした場合は、ソフトウェアインベントリにアクセスできます。

:::image type="content" source="media/asset-inventory/software-inventory-filters.gif" alt-text="脅威と脆弱性の解決策を有効にした場合、クラウドの資産インベントリの Defender は、インストールされているソフトウェアによってリソースを選択するためのフィルターを提供します。":::

> [!NOTE]
> "Blank" オプションを選択すると、エンドポイントに Microsoft Defender がないコンピューターが表示されます (または、Microsoft Defender for server は使用できません)。

資産インベントリ ページのフィルターと同様に、Azure Resource Graph エクスプローラーからソフトウェア インベントリ データを探索することができます。

Azure Resource Graph エクスプローラーを使用してソフトウェア インベントリ データにアクセスし、探索する例:

1. **Azure Resource Graph エクスプローラー** を開きます。

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

1. 次のサブスクリプション スコープを選択します: securityresources/softwareinventories

1. 次のいずれかのクエリを入力し (または、それらをカスタマイズしたり、独自に作成して)、 **[クエリの実行]** を選択します。

    - インストールされているソフトウェアの基本的な一覧を生成するには:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=properties.version
        ```

    - バージョン番号でフィルター処理するには:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=tostring(properties.    version)
        | where Software=="windows_server_2019" and parse_version(Version)<=parse_version("10.0.17763.1999")
        ```

    - ソフトウェア製品の組み合わせでコンピューターを検索するには:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = properties.azureVmId
        | where properties.softwareName == "apache_http_server" or properties.softwareName == "mysql"
        | summarize count() by tostring(vmId)
        | where count_ > 1
        ```

    - ソフトウェア製品と別のセキュリティ推奨事項の組み合わせ:

        (この例では、MySQL がインストールされ、管理ポートが公開されているコンピューター)

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = tolower(properties.azureVmId)
        | where properties.softwareName == "mysql"
        | join (
        securityresources
        | where type == "microsoft.security/assessments"
        | where properties.displayName == "Management ports should be closed on your virtual machines" and properties.status.code == "Unhealthy"
        | extend vmId = tolower(properties.resourceDetails.Id)
        ) on vmId
        ```

## <a name="faq---inventory"></a>FAQ - インベントリ

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>一部のサブスクリプション、マシン、ストレージ アカウントなどが表示されないのはなぜですか。

[インベントリ] ビューには、クラウドに接続されているリソースの Defender がクラウドセキュリティ対策管理 (CSPM) の観点から一覧表示されます。 フィルター処理によって返されるのは、環境内のすべてのリソースではなく、未処理の (または「アクティブな」) レコメンデーションを含むリソースだけです。

たとえば、次のスクリーンショットは、8 つのサブスクリプションにアクセスできるユーザーを示していますが、現在推奨事項を持っているのは 7 人のみです。 そのため、リソースの種類 **= サブスクリプション** でフィルター処理すると、アクティブな推奨事項を持つ 7 つのサブスクリプションだけがインベントリに表示されます。

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="アクティブなレコメンデーションがない場合、一部のサブスクリプションは返されません。" lightbox="./media/asset-inventory/filtered-subscriptions-some.png":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-defender-for-cloud-or-monitoring-agent-columns"></a>一部のリソースで Defender for Cloud または監視エージェントの列に空白の値が表示される理由

すべての Defender for Cloud で監視されるリソースにエージェントがあるというのではありません。 たとえば、Azure Storage、ディスク、Logic Apps、Data Lake Analysis、Event Hub などの PaaS リソースを使用する場合、Defender for Cloud でエージェントを監視する必要はありません。

価格やエージェントの監視がリソースに関係しない場合、インベントリのこれらの列には何も表示されません。

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="一部のリソースでは、監視エージェントまたは Defender for Cloud 列に空白の情報が表示されます。" lightbox="./media/asset-inventory/agent-pricing-blanks.png":::

## <a name="next-steps"></a>次の手順

この記事では、Microsoft Defender for Cloud の資産インベントリ ページについて説明しました。

関連するツールの詳細については、以下のページをご覧ください。

- [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)
- [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/)
