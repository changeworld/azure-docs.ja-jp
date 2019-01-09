---
title: Azure Functions の外部テーブル バインディング (試験段階)
description: Azure Functions での外部テーブル バインディングの使用
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: 62924488b776a1a89e1abf492db1881a44585b1a
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997824"
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Azure Functions の外部テーブル バインディング (試験段階)

この記事では、Azure Functions で Sharepoint や Dynamics など SaaS プロバイダーの表形式データを操作する方法について説明します。 Azure Functions は、外部テーブルの入力バインディングと出力バインディングをサポートしています。

> [!IMPORTANT]
> 外部テーブル バインディングは試験段階であり、一般公開 (GA) されない可能性があります。 外部テーブル バインディングは Azure Functions 1.x にのみ含まれており、Azure Functions 2.x に追加される予定はありません。 SaaS プロバイダーのデータにアクセスする必要があるシナリオでは、[関数を呼び出すロジック アプリケーション](functions-twitter-email.md)の使用を検討してください。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API 接続

テーブル バインディングでは、サード パーティの SaaS プロバイダーに対する認証に外部 API 接続を利用します。 

バインディングを割り当てるときに、新しい API 接続を作成するか、または同じリソース グループ内の既存の API 接続を使うことができます。

### <a name="available-api-connections-tables"></a>使用できる API 接続 (テーブル)

|コネクタ|トリガー|入力|出力|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||○|○
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||○|○
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||○|○
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||○|○
|[Google スプレッドシート](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||○|○
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||○|○
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||○|○
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||○|○
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||○|○
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||○|○
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||○|○
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||○|○
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||○|○
|[Teradata](https://www.teradata.com/products-and-services/azure/products/)||○|○
|UserVoice||○|○
|Zendesk||○|○

> [!NOTE]
> 外部テーブル接続を [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) で使用することもできます。

## <a name="creating-an-api-connection-step-by-step"></a>API 接続の作成手順

1. 関数アプリケーションの Azure Portal ページで、プラス記号 (**+**) を選択して関数を作成します。

1. **[シナリオ]** ボックスで、**[試験段階]** を選択します。

1. **[外部テーブル]** を選択します。

1. 言語を選択します。

2. **[外部テーブル接続]** で既存の接続を選択するか、**[新規]** を選択します。

1. 新しい接続の場合は、設定を構成し、**[承認]** を選択します。

1. **[作成]** を選択すると、関数が作成されます。

1. **[統合]、[外部テーブル]** の順に選択します。

1. ターゲット テーブルを使用するための接続を構成します。 これらの設定は SaaS プロバイダーごとに異なります。 例については、次のセクションを参照してください。

## <a name="example"></a>例

この例では、Id、LastName、FirstName の各列で構成された "Contact" という名前のテーブルに接続します。 以下のコードは、Contact テーブル内のエンティティを列挙し、ファースト ネームとラスト ネームをログに記録するものです。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>SQL Server データ ソース

SQL Server でこの例で使用するテーブルを作成するには、このスクリプトを使用します。 `dataSetName` は "既定値" です。

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Google スプレッドシート データ ソース

Google ドキュメントでこの例で使用する表を作成するには、`Contact` というワークシートを含むスプレッドシートを作成します。 コネクタでスプレッドシートの表示名を使用することはできません。 内部的な名前 (太字部分) を dataSetName として使う必要があります (例:`docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**)。先頭行に列名として `Id`、`LastName`、`FirstName` を追加し、それ以降の行にデータを投入します。

### <a name="salesforce"></a>Salesforce

この例を Salesforce で使用する場合は、`dataSetName` が "既定値" です。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 説明|
|---------|----------------------|
|**type** | `apiHubTable` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 関数コード内のイベント項目を表す変数の名前。 | 
|**connection**| API 接続文字列が格納されるアプリ設定を指定します。 このアプリ設定は、[統合] の UI で API 接続を追加すると自動的に作成されます。|
|**dataSetName**|読み込むテーブルを含むデータ セットの名前。|
|**tableName**|テーブルの名前|
|**entityId**|テーブル バインディングの場合は空にする必要があります。

テーブル コネクタによってデータセットが得られ、それぞれのデータセットにテーブルが格納されます。 既定のデータセットの名前は "default" です。 以下の表は、各種 SaaS プロバイダーにおけるデータセットとテーブルのタイトルを一覧にしたものです。

|コネクタ|Dataset|テーブル|
|:-----|:---|:---| 
|**SharePoint**|サイト|SharePoint リスト
|**SQL**|Database|テーブル 
|**Google シート**|スプレッドシート|ワークシート 
|**Excel**|Excel ファイル|シート 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
