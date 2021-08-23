---
title: Azure ロールを使用してアクセスを承認する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 管理の管理タスクを制御および委任するための、Azure portal での Azure ロールベースのアクセス制御 (Azure RBAC)。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/28/2021
ms.openlocfilehash: 4e79af03f4545e4af799c660314212d18dbc91ff
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110459966"
---
# <a name="authorize-access-through-azure-roles-in-azure-cognitive-search"></a>Azure Cognitive Search で Azure ロールを使用してアクセスを承認する

Azure では、ポータルまたはリソース マネージャー API で管理するすべてのサービスに対して、 [グローバルなロールベースの承認モデル](../role-based-access-control/role-assignments-portal.md) を用意しています。 承認モデルには所有者、共同作成者、および閲覧者のロールがあり、これらによって、各ロールに割り当てられる Active Directory のユーザー、グループ、およびセキュリティ プリンシパルに対する "*サービス管理*" のレベルが決まります。 Cognitive Search では、これらの 3 つのロールを使用して、検索サービス管理のアクセスを承認します。

Cognitive Search では、以下はサポートされていません。

+ [カスタム ロール](../role-based-access-control/custom-roles.md)。
+ インデックス、またはサービスのその他の任意のオブジェクトに対する作成やクエリなど、コンテンツに関連する操作に対するロールベースのアクセス制御 (Azure RBAC)。

  コンテンツ操作を実行するための承認には、[管理 API キーまたはクエリ API キー](search-security-api-keys.md)が必要です。

> [!Note]
> 検索結果に対する ID ベースのアクセス (行レベルのセキュリティと呼ばれることがあります) の場合は、セキュリティ フィルターを作成して ID によって結果をトリミングし、要求者がアクセスしてはいけないドキュメントを削除できます。 詳細については、[セキュリティ フィルター](search-security-trimming-for-azure-search.md)に関する記事を参照してください。

## <a name="roles-used-in-cognitive-search"></a>Cognitive Search で使用されるロール

Azure Cognitive Search の場合、ロールは次の管理タスクをサポートするアクセス許可レベルに関連付けられています。

| Role | タスク |
| --- | --- |
| 所有者 |サービスを作成または削除します。 サービスの任意のオブジェクト (API キー、インデックス、シノニム マップ、インデクサー、インデクサー データ ソース、およびスキルセット) を作成、更新、または削除します。 </br></br>ポータルで公開されているか、管理 REST API、Azure PowerShell、または Azure CLI によって公開される、すべてのサービス情報へのフル アクセス。 </br></br>ロールのメンバーシップを割り当てます。</br></br>サブスクリプション管理者とサービス所有者には、所有者ロールに自動メンバーシップがあります。 |
| Contributor | ロールの割り当てを除き、アクセス レベルは所有者と同じです。 [Search Service Contributor](../role-based-access-control/built-in-roles.md#search-service-contributor) は、汎用の共同作成者組み込みロールと同等のものです。 |
| Reader | 一部のサービス情報へのアクセスが制限されています。 ポータルでは、閲覧者ロールはサービスの [概要] ページの [要点] セクションと [監視] タブで情報にアクセスできます。他のすべてのタブとページにはアクセスできません。 </br></br>[要点] セクションには、リソース グループ、状態、場所、サブスクリプションの名前と ID、タグ、URL、価格レベル、レプリカ、パーティション、および検索ユニットが表示されます。 </br></br>[監視] タブには、サービス メトリック (検索の待ち時間、調整された要求の割合、1 秒あたりの平均クエリ数) が表示されます。 </br></br>[使用状況] タブ (ストレージ、サービスで作成されたインデックスまたはインデクサーの数) にはアクセスできず、[インデックス]、[インデクサー]、[データ ソース]、[スキルセット]、または [デバッグ セッション] の各タブの情報にもアクセスできません。 |

ロールは、サービス エンドポイントへのアクセス権を付与しません。 インデックスの管理、インデックスの作成、検索データのクエリなど、検索サービスの操作は、ロールではなく API キーによって制御されます。 詳細については、[API キーの管理](search-security-api-keys.md)に関するページを参照してください。

## <a name="tasks-and-permission-requirements"></a>タスクとアクセス許可の要件

次の表では、Azure Cognitive Search で許可される操作のほか、特定の操作へのアクセスを有効にするロールまたはキーについてまとめています。

+ Azure RBAC メンバーシップにより、ポータル ページとサービス管理タスク (サービスまたはその API キーの作成、削除、または変更) へのアクセスが許可されます。

+ API キーは、サービスが存在するようになった後に作成され、サービス上でのコンテンツ操作に適用されます。

さらに、オブジェクトの作成や削除など、ポータルでのコンテンツ関連の操作の場合、すべての操作と情報へのフル アクセスは、明示的なロール メンバーシップ (所有者または共同作成者) とポータルの管理者キーの内部使用によってサポートされます。 つまり、ポータルでインデックスを作成しているか読み込んでいる場合は、自分の RBAC メンバーシップによってページへのアクセスが許可されますが、ポータル自体は管理者キーを使用してサービスでの操作を認証します。

| 操作 | 何によって制御されるか |
|-----------|-------------------------|
| サービスの作成または削除 | Azure RBAC アクセス許可:所有者または共同作成者 |
| ネットワーク セキュリティの構成 (IP ファイアウォール) | Azure RBAC アクセス許可:所有者または共同作成者 |
| プライベート エンドポイントの作成または削除 | Azure RBAC アクセス許可:所有者または共同作成者 |
| カスタマー マネージド キーの実装 | Azure RBAC アクセス許可:所有者または共同作成者 |
| レプリカまたはパーティションの調整 | Azure RBAC アクセス許可:所有者または共同作成者|
| 管理キーまたはクエリ キーの管理 | Azure RBAC アクセス許可:所有者または共同作成者|
| ポータルまたは管理 API でのサービス情報の表示 | Azure RBAC アクセス許可:所有者、共同作成者、または閲覧者  |
| ポータルまたは管理 API でのオブジェクト情報およびメトリックの表示 | Azure RBAC アクセス許可:所有者または共同作成者 |
| サービス上のオブジェクトの作成、変更、削除: <br>インデックスとコンポーネント部分 (アナライザーの定義、スコアリング プロファイル、CORS オプションなど)、インデクサー、データ ソース、スキルセット、シノニム、サジェスター | API を使用する場合は管理者キー、ポータルを使用する場合は Azure RBAC 所有者または共同作成者 |
| インデックスのクエリ | API を使用する場合は管理者またはクエリ キー、ポータルを使用する場合は Azure RBAC 所有者または共同作成者 |
| オブジェクトに関するクエリ システム情報 (オブジェクトの統計、カウント、一覧を返す操作など) | API を使用する場合は管理者キー、ポータルを使用する場合は Azure RBAC 所有者または共同作成者 |

## <a name="next-steps"></a>次の手順

+ [PowerShell を使用した管理](search-manage-powershell.md) 
+ [Azure Cognitive Search のパフォーマンスと最適化](search-performance-optimization.md)
+ [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)。
