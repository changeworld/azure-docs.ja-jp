---
title: Azure Data Factory を使用した Web テーブルからのデータ コピー | Microsoft Docs
description: Azure Data Factory の Web Table コネクタについて説明します。このコネクタを使用すると、Web テーブルから、Data Factory でシンクとしてサポートされているデータ ストアにデータをコピーすることができます。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: 995bf4586b88671c65077d965b0588de8de74e5c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048936"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Azure Data Factory を使用して Web テーブルからデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-web-table-connector.md)
> * [現在のバージョン](connector-web-table.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Web テーブル データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Web テーブル データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Web テーブル コネクタは、**HTML ページからのテーブル コンテンツの抽出**をサポートしています。 HTTP エンドポイントからデータを取得するには、代わりに [HTTP コネクタ](connector-http.md)を使用します。

## <a name="prerequisites"></a>前提条件

この Web テーブル コネクタを使用するには、セルフホステッド Integration Runtime を設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Web テーブル コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Web テーブルのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Web** |[はい] |
| url | Web ソースへの URL |[はい] |
| authenticationType | 許可されている値は **Anonymous** です。 |[はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |[はい] |

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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Web テーブル データセットでサポートされるプロパティの一覧を示します。

Web テーブルからデータをコピーするには、データセットの type プロパティを **WebTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティを **WebTable** に設定する必要があります。 | [はい] |
| パス |テーブルを含むリソースの相対 URL。 |いいえ。 パスが指定されていないとき、リンクされたサービス定義に指定されている URL のみだけが使用されます。 |
| Index |リソースのテーブルのインデックス。 HTML ページのテーブルのインデックスを取得する方法については、「 [HTML ページのテーブルのインデックスを取得する](#get-index-of-a-table-in-an-html-page) 」を参照してください。 |[はい] |

**例:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
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

1. **Excel 2016** を起動し、**[データ]** タブに切り替えます。
2. ツール バーの **[新しいクエリ]** をクリックし、**[その他のソースから]** をポイントし、**[Web から]** をクリックします。

    ![Power Query メニュー](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. **[Web から]** ダイアログ ボックスで、リンクされたサービスの JSON で使用する **URL** を入力し (例: https://en.wikipedia.org/wiki/)、データセットに指定するパスを入力し (例: AFI%27s_100_Years...100_Movies)、**[OK]** をクリックします。

    ![Web ダイアログから](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    この例で使用される URL は https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies です。
4. **[Web コンテンツへのアクセス]** ダイアログ ボックスが表示された場合、適切な **URL** と**認証**を選択し、**[接続]** をクリックします。

   ![[Access Web コンテンツ] ダイアログ ボックス](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. ツリー ビューの**テーブル** アイテムをクリックしてテーブルのコンテンツを表示し、一番下にある **[編集]** をクリックします。  

   ![[ナビゲーター] ダイアログ](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. **[クエリ エディター]** ウィンドウで、ツール バーの **[詳細エディター]** をクリックします。

    ![[詳細エディター] ボタン](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. [詳細エディター] ダイアログ ボックスで、"Source" の横にある数字はインデックスです。

    ![詳細エディター - インデックス](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Excel 2013 を使用している場合、 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) を使用してインデックスを取得します。 詳細については、「 [Web ページに接続する](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) 」を参照してください。 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)の使用時と手順は同じです。


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
