---
title: Web テーブルからデータをコピーする
description: Web テーブルから、Azure Data Factory および Synapse Analytics でシンクとしてサポートされているデータ ストアにデータをコピーすることができる Web テーブル コネクタについて説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: af6423b58cb2eba4fca0d902270afee03a733637
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782686"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して Web テーブルからデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-web-table-connector.md)
> * [現在のバージョン](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory または Synapse Analytics パイプラインで Copy アクティビティを使用して、Web テーブル データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

この Web テーブル コネクタ、[REST コネクタ](connector-rest.md)および [HTTP コネクタ](connector-http.md)の違いは次のとおりです。

- **Web テーブル コネクタ** では、HTML Web ページからテーブルの内容を抽出します。
- **REST コネクタ** では、具体的には RESTful API からのデータのコピーがサポートされます。
- **HTTP コネクタ** では一般的に、HTTP エンドポイントからデータを取得します (たとえば、ファイルをダウンロードします)。 

## <a name="supported-capabilities"></a>サポートされる機能

この Web テーブル コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Web テーブル データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Web テーブル コネクタは、**HTML ページからのテーブル コンテンツの抽出** をサポートしています。

## <a name="prerequisites"></a>前提条件

この Web テーブル コネクタを使用するには、セルフホステッド Integration Runtime を設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-web-table-using-ui"></a>UI を使用して Web Table のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Web Table のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成する。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用して新しいリンク サービスを作成します。":::

2. Web を検索し、Web Table コネクタを選択します。

   :::image type="content" source="media/connector-web-table/web-table-connector.png" alt-text="Web Table コネクタを選択します。":::    


1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

   :::image type="content" source="media/connector-web-table/configure-web-table-linked-service.png" alt-text="Web Table のリンク サービスを構成します。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、Web テーブル コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Web テーブルのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Web** |はい |
| url | Web ソースへの URL |はい |
| authenticationType | 使用可能な値:**Anonymous**。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Web テーブル データセットでサポートされるプロパティの一覧を示します。

Web テーブルからデータをコピーするには、データセットの type プロパティを **WebTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**WebTable** に設定する必要があります | はい |
| path |テーブルを含むリソースの相対 URL。 |いいえ。 パスが指定されていないとき、リンクされたサービス定義に指定されている URL のみだけが使用されます。 |
| インデックス (index) |リソースのテーブルのインデックス。 HTML ページのテーブルのインデックスを取得する方法については、「 [HTML ページのテーブルのインデックスを取得する](#get-index-of-a-table-in-an-html-page) 」を参照してください。 |はい |

**例:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Web テーブル ソースでサポートされるプロパティの一覧を示します。

### <a name="web-table-as-source"></a>ソースとしての Web テーブル

Web テーブルからデータをコピーするには、コピー アクティビティでソースの種類を **WebSource** に設定します。追加のプロパティはサポートされていません。

**例:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>HTML ページのテーブルのインデックスを取得する

[データセットのプロパティ](#dataset-properties)で構成する必要のあるテーブルのインデックスを取得するには、次のように、たとえば Excel 2016 をツールとして使用します。

1. **Excel 2016** を起動し、 **[データ]** タブに切り替えます。
2. ツール バーの **[新しいクエリ]** をクリックし、 **[その他のソースから]** をポイントし、 **[Web から]** をクリックします。

    :::image type="content" source="./media/copy-data-from-web-table/PowerQuery-Menu.png" alt-text="Power Query メニュー":::
3. **[Web から]** ダイアログ ボックスで、リンクされたサービスの JSON で使用する **URL** を入力し (例: https://en.wikipedia.org/wiki/) 、データセットに指定するパスを入力し (例: AFI%27s_100_Years...100_Movies)、 **[OK]** をクリックします。

    :::image type="content" source="./media/copy-data-from-web-table/FromWeb-DialogBox.png" alt-text="Web ダイアログから":::

    この例で使用される URL は https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies です。
4. **[Web コンテンツへのアクセス]** ダイアログ ボックスが表示された場合、適切な **URL** と **認証** を選択し、 **[接続]** をクリックします。

   :::image type="content" source="./media/copy-data-from-web-table/AccessWebContentDialog.png" alt-text="[Access Web コンテンツ] ダイアログ ボックス":::
5. ツリー ビューの **テーブル** アイテムをクリックしてテーブルのコンテンツを表示し、一番下にある **[編集]** をクリックします。  

   :::image type="content" source="./media/copy-data-from-web-table/Navigator-DialogBox.png" alt-text="[ナビゲーター] ダイアログ":::
6. **[クエリ エディター]** ウィンドウで、ツール バーの **[詳細エディター]** をクリックします。

    :::image type="content" source="./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png" alt-text="[詳細エディター] ボタン":::
7. [詳細エディター] ダイアログ ボックスで、"Source" の横にある数字はインデックスです。

    :::image type="content" source="./media/copy-data-from-web-table/AdvancedEditor-Index.png" alt-text="詳細エディター - インデックス":::

Excel 2013 を使用している場合、 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) を使用してインデックスを取得します。 詳細については、「 [Web ページに接続する](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) 」を参照してください。 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)の使用時と手順は同じです。


## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
