---
title: ポータル内でのサービス管理
titleSuffix: Azure Cognitive Search
description: Azure portal を使用して、Microsoft Azure のホスト型クラウド検索サービスである Azure Cognitive Search サービスを管理します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579770"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Azure portal での Azure Cognitive Search のサービス管理

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [ポータル](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search は、高度な検索エクスペリエンスをカスタム アプリに組み込むために使用される、フル マネージドのクラウドベースの検索サービスです。 この記事では、既に作成済みの検索サービスに対して [Azure portal](https://portal.azure.com) で実行できる管理タスクについて説明します。 ポータルを使用すると、コンテンツの管理と探索だけでなく、サービスに関連するすべての[管理タスク](#management-tasks)を実行できます。 そのため、ポータルでは、検索サービスの操作のあらゆる側面に幅広くアクセスできます。

各検索サービスは、スタンドアロン リソースとして管理されます。 次の図は、"demo-search-svc" という 1 つの無料検索サービスのポータル ページを示しています。 サービス管理に Azure PowerShell または Azure CLI を使用することに慣れている場合でも、ポータル ページで提供されるツールと機能について理解しておくことは意義があります。 タスクによっては、コードを使用する場合よりポータルで実行する方が簡単で迅速な場合があります。 

## <a name="overview-home-page"></a>概要 (ホーム) ページ

概要ページは、各サービスの "ホーム" ページです。 下の、赤色のボックスで囲まれた画面上の領域は、特にサービスを初めて使用する場合に頻繁に使用すると思われるタスク、ツール、タイルを示しています。

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="検索サービスのポータル ページ" border="true":::

| 領域 | 説明 |
|------|-------------|
| 1  | **[主な機能]** セクションには、接続の設定時に使用されるエンドポイントを含む、サービス プロパティが表示されます。 また、サービス レベル、レプリカ、パーティションの数も一目でわかります。 |
| 2 | ページの上部には、対話型ツールを起動したりサービスを管理したりするための一連のコマンドが表示されています。 [[データをインポート]](search-get-started-portal.md) と [[検索エクスプローラー]](search-explorer.md) はどちらも、プロトタイプ作成と探索に一般的に使用されます。 |
| 3 | **[主な機能]** セクションの下には一連のタブ付きサブページがあり、使用状況の統計情報、サービスの正常性メトリックにすばやくアクセスしたり、サービス上にある既存のすべてのインデックス、インデクサー、データ ソース、スキルセットにアクセスしたりすることができます。 インデックスまたは別のオブジェクトを選択すると、オブジェクトの構成、設定、状態 (該当する場合) が表示される追加のページが表示されるようになります。 |
| 4 | 左側では、接続で使用される API キーへのアクセス、サービスの構成、セキュリティの構成、操作の監視、タスクの自動化、サポートの取得を行うための追加ページを開くリンクにアクセスできます。 |

### <a name="read-only-service-properties"></a>読み取り専用のサービス プロパティ

検索サービスのいくつかの側面は、サービスがプロビジョニングされるときに決定され、変更できません。

* サービス名 (検索サービスの名前は変更できません)
* サービスの場所 (元のままの検索サービスを別のリージョンに簡単に移動することはできません。 テンプレートはありますが、コンテンツの移動は手動での処理です)
* サービス レベル (新しいサービスを作成せずに、たとえば、S1 から S2 に切り替えることはできません)

## <a name="management-tasks"></a>管理タスク

サービス管理は、設計上は軽量であり、多くの場合、サービス自体に関連した、実行できるタスクによって定義されます。

* レプリカとパーティションを追加または削除して[容量を調整する](search-capacity-planning.md)
* 管理およびクエリの操作に使用される [API キーを管理する](search-security-api-keys.md)
* ロールベースのセキュリティを通じて[管理操作へのアクセスを制御する](search-security-rbac.md)
* IP アドレスによってアクセスを制限するように [IP ファイアウォール規則を構成する](service-configure-firewall.md)
* Azure Private Link とプライベート仮想ネットワークを使用して[プライベート エンドポイントを構成する](service-create-private-endpoint.md)
* [サービスの正常性を監視する](search-monitor-usage.md): ストレージ、クエリ ボリューム、待機時間

また、サービスで作成されたすべてのオブジェクト (インデックス、インデクサー、データ ソース、スキルセットなど) を列挙することもできます。 ポータルの概要ページには、サービスに存在するすべてのコンテンツが表示されます。 検索サービスの大部分の操作は、コンテンツに関連しています。

ポータルで実行されるものと同じ管理タスクは、[管理 REST API](/rest/api/searchmanagement/)、[Az.Search PowerShell モジュール](search-manage-powershell.md)、[az search Azure CLI モジュール](search-manage-azure-cli.md)、および .NET、Python、Java、JavaScript 用の Azure SDK を使用してプログラムによって処理することもできます。 管理タスクは、ポータルとすべてのプログラマティック インターフェイスにわたって完全に表現されます。 1 つのモダリティでのみ使用できる特定の管理タスクはありません。

Cognitive Search では、より詳細な監視と管理を行うために、他の Azure サービスを活用します。 検索サービス自体によってその中に格納されるデータは、オブジェクト コンテンツ (インデックス、インデクサーとデータ ソースの定義、その他のオブジェクト) だけです。 ポータルのページに報告が出力されるメトリックは、30 日のローリング サイクルで内部ログから取得されます。 ユーザーが制御するログのデータ保持とその他のイベントについては、[Azure Monitor](../azure-monitor/index.yml) が必要になります。 検索サービスの診断ログの設定の詳細については、[ログ データの収集と分析](search-monitor-logs.md)に関する記事を参照してください。

## <a name="administrator-permissions"></a>管理者の権限

検索サービスの概要ページを開くと、ご自身のアカウントに割り当てられているアクセス許可によって、使用できるページが決まります。 この記事の冒頭にある概要ページには、管理者または共同作成者に表示されるポータル ページが示されています。

Azure リソースでは、管理者権限はロールの割り当てによって付与されます。 Azure Cognitive Search のコンテキストでは、ポータル、[PowerShell](search-manage-powershell.md)、[Azure CLI](search-manage-azure-cli.md)、[管理 REST API](/rest/api/searchmanagement/search-howto-management-rest-api) のうちどれを使用しているかとは無関係に、[ロールの割り当て](search-security-rbac.md)によって、レプリカとパーティションの割り当てまたは API キーの管理を実行できるユーザーが決定されます。

> [!TIP]
> サービス自体のプロビジョニングまたは使用停止は、Azure サブスクリプションの管理者または共同管理者が実行できます。 Azure 全体のメカニズムを使用して、サブスクリプションまたはリソースをロックし、管理者権限を持つユーザーが検索サービスを誤って、または許可なく削除しないようにすることができます。 詳細については、[リソースのロックによる予期せぬ削除の防止](../azure-resource-manager/management/lock-resources.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* ポータルで使用できる[監視機能](search-monitor-usage.md)を確認する
* [PowerShell](search-manage-powershell.md) または [Azure CLI](search-manage-azure-cli.md) によって自動化する
* コンテンツと操作を保護するための[セキュリティ機能](search-security-overview.md)を確認する
* [診断ログ](search-monitor-logs.md)を有効にしてクエリとインデックス作成のワークロードを監視する