---
title: Azure Monitor でのクエリ パック
description: Azure Monitor のクエリ パックは、複数の Log Analytics ワークスペースでログ クエリのコレクションを共有する方法を提供します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 28bd435e8c6f6144b1dafd11df174d69f763d45d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987463"
---
# <a name="query-packs-in-azure-monitor-logs-preview"></a>Azure Monitor ログでのクエリ パック (プレビュー)
クエリ パックは、Azure Monitor のログ クエリのコンテナーとして機能する Resource Manager オブジェクトであり、ログ クエリを保存し、Log Analytics の複数のワークスペースや他のコンテキストでそれらを共有する方法を提供します。 

## <a name="view-query-packs"></a>クエリ パックの表示
**[Log Analytics クエリ パック]** メニューから、Azure portal クエリ パックを表示および管理できます。 クエリ パックを選択して、そのアクセス許可を表示および編集します。 API を使用してクエリ パックを作成する方法の詳細については、以下を参照してください。

[![クエリ パックの表示](media/query-packs/view-query-pack.png)](media/query-packs/view-query-pack.png#lightbox)

## <a name="permissions"></a>アクセス許可
Azure portal で表示しているときにクエリ パックのアクセス許可を設定できます。 ユーザーがクエリ パックを使用するには、次のアクセス許可が必要です。

- **閲覧者** - ユーザーは、クエリ パック内のすべてのクエリを表示して実行できます。
- **共同作成者** - ユーザーは既存のクエリを変更し、クエリ パックに新しいクエリを追加できます。

## <a name="default-query-pack"></a>既定のクエリ パック
**DefaultQueryPack** という名前のクエリ パックは、最初のクエリが保存されるときに、**LogAnalyticsDefaultResources** という名前のリソース グループ内の各サブスクリプションに自動的に作成されます。 このクエリ パック内にクエリを作成するか、要件に応じて追加のクエリ パックを作成できます。

## <a name="using-multiple-query-packs"></a>複数のクエリ パックの使用
ほとんどのユーザーにおいて、クエリを保存して再利用するには、1 つの既定のクエリパックで十分です。 しかし、組織内のユーザーのために複数のクエリ パックを作成する必要がある場合があります。たとえば、異なる Log Analytics セッションに異なるクエリ セットを読み込む場合や、クエリのコレクションごとに異なるアクセス許可を付与する場合などです。 

API を使用して新しいクエリ パックを作成するときに、ビジネス要件に応じてクエリを分類するタグを追加できます。 たとえば、クエリ パックにタグを付け、組織内の特定の部門に関連付けたり、含まれているクエリが対処する問題の重大度に関連付けたりすることができます。 これにより、さまざまなユーザーのセットや状況に合わせて、さまざまなクエリを作成できます。

## <a name="query-pack-definition"></a>クエリ パックの定義
各クエリ パックは、1 つ以上のクエリの定義を含む JSON ファイルで定義されています。 各クエリは、次のようにブロックによって表されます。

```json
{
    "properties":
       {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        },
   }
}
```


## <a name="query-properties"></a>クエリのプロパティ
クエリ パック内の各クエリには、次のプロパティがあります。


| プロパティ | 説明 |
|:---|:---|
| displayName | Log Analytics に表示される各クエリの表示名。 | 
| description | Log Analytics に表示される各クエリの説明。 |
| body        | KQL で記述されたクエリ。 |
| 関連     | クエリの関連カテゴリ、リソースの種類、およびソリューション。 クエリを見つけやすくするために、ユーザーが Log Analytics でグループ化とフィルター処理に使用します。 各クエリには、最大 10 個のタイプを使用することができます。 使用可能な値は、 https://api.loganalytics.io/v1/metadata?select=resourceTypes 、ソリューション、カテゴリから取得します。 |
| tags        | ユーザーが Log Analytics での並べ替えとフィルター処理に使用する追加のタグ。 各タグは、[クエリをグループ化およびフィルター処理](queries.md#finding-and-filtering-queries)するときに、カテゴリ、リソースの種類、およびソリューションに追加されます。 |

## <a name="create-a-query-pack"></a>クエリ パックを作成する
Azure portal でクエリ パックは、[Log Analytics query pack]\(Log Analytics クエリ パック\) ブレードで、または REST API を使用して作成します。 

### <a name="create-token"></a>トークンの作成
API 要求の認証にはトークンが必要です。 **armclient** の使用を含めて、トークンを取得するには、複数の方法があります。

最初に次のコマンドを使用して、Azure にログインします。

```
armclient login
```

次に、次のコマンドを使用してトークンを作成します。 トークンはクリップボードに自動的にコピーされるので、別のツールに貼り付けることができます。

```
armclient token
```

### <a name="create-payload"></a>ペイロードを作成する
要求のペイロードは、1 つ以上のクエリとクエリ パックの格納場所を定義する JSON です。 クエリ パックの名前は、次のセクションで説明する API 要求で指定します。

```json
{
    "location": "eastus",
    "properties":
    {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        }
    }
}
```

### <a name="create-request"></a>要求を作成
REST API を使用して新しいクエリ パックを作成するには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。 コンテンツ タイプは application/json である必要があります。

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack?api-version=2019-09-01-preview
```

Fiddler や Postman などの REST API 要求を送信できるツールを使用して、前のセクションで説明したペイロードを使用して要求を送信します。 クエリ ID が生成され、ペイロードに返されます。 

## <a name="update-a-query-pack"></a>クエリ パックを更新する
クエリ パックを更新するには、更新されたペイロードで次の要求を送信します。 このコマンドには、クエリ パック ID が必要です。

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack/queries/query-id/?api-version=2019-09-01-preview
```

## <a name="next-steps"></a>次のステップ

- Log Analytics でユーザーがクエリ パックを操作する方法については、「[Azure Monitor Log Analytics でのクエリの使用](queries.md)」を参照してください。
