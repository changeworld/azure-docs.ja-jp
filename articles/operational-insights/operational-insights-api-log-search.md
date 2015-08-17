<properties
   pageTitle="Operational Insights のログ検索 API"
   description="Operational Insights のログ検索 API の概要と、使用方法の例が記載されています。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/21/2015"
   ms.author="banders" />



# Operational Insights の検索 API の概要とリファレンス

このガイドには、Operational Insights のログ検索 API の使用方法に関する基本的な説明と、コマンドの使用方法の例が記載されています。

## ログ検索 API の概要

Operational Insights のログ検索 API は RESTful であり、Azure リソース マネージャー API を使用してアクセスできます。このドキュメントでは、Azure リソース マネージャー API の呼び出しを簡略化するオープン ソースのコマンド ライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を通じて API にアクセスする例を示します。Operational Insights のログ検索 API には、ARMClient や PowerShell を使用する以外にもさまざまな方法でアクセスできます。これらのツールを使用すると、RESTful な Azure リソース マネージャー API を使用して Operational Insights のワークスペースにアクセスし、その中で検索コマンドを実行できます。API の検索結果は JSON 形式で出力されるため、検索結果をプログラムによりさまざまな方法で使用できます。

Azure リソース マネージャーは [.NET のライブラリ](https://msdn.microsoft.com/library/azure/dn910477.aspx)や [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx) 経由で使用できます。詳細はリンク先の Web ページで確認してください。

## 検索 API の基本チュートリアル

### ARMClient を使用するには

1. [Chocolatey](https://chocolatey.org/) をインストールします。これは、Windows のオープン ソースのマシン パッケージ マネージャーです。
2. 管理者として PowerShell ウィンドウを開き、次のコマンドを実行します。

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

3. 新しいコマンド プロンプトを開いて ARMClient をインストールし、次のコマンドを実行します。

    ```
    choco install armclient
    ```

### ARMClient を使用して単純な検索を実行するには

1. Microsoft アカウントまたは OrgID アカウントにログインします。

    ```
    armclient login
```
  ログインに成功すると、指定のアカウントに関連付けられているすべてのサブスクリプションが一覧表示されます。次に例を示します。

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Operations Management Suite のワークスペースを取得します。次に例を示します。

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2014-10-10
    ```

    取得の呼び出しが成功すると、サブスクリプションに関連付けられているすべてのワークスペースが出力されます。次に例を示します。

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. 検索変数を作成します。次に例を示します。

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}”;
    ```
4. 新しい検索変数を使用して検索します。次に例を示します。

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2014-10-10 $mySearch
    ```

## 検索 API リファレンスの例
次の例は、検索 API の使用方法を示します。

### 検索 - アクション/読み取り

**サンプル URL:**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2014-10-10
```

**要求:**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
```
次のテーブルは、使用可能なプロパティについて説明しています。

|**プロパティ**|**説明**|
|---|---|
|top|返される結果の最大数。|
|highlight|pre パラメーターと post パラメーターが含まれ、一般に一致するフィールドを強調表示するために使用します。|
|pre|一致するフィールドに指定した文字列をプレフィックスします。|
|post|一致するフィールドに指定した文字列を追加します。|
|query|結果を収集して返すのに使用する検索クエリ。|
|start|結果を検索する時間枠の最初。|
|end|結果を検索する時間枠の最後。|


**応答:**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### 検索/{ID} - アクション/読み取り

**保存された検索の内容を要求:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2014-10-10
```

>[AZURE.NOTE]検索によって「Pending」状態が返される場合、この API を使用して更新後の結果をポーリングできます。検索の結果は 6 分後にキャッシュから削除され、Http Gone が返されます。最初の検索要求によって「Successful」状態がすぐに返された場合、結果はキャッシュに追加されず、クエリを実行してもこの API で Http Gone は返されません。Http 200 の結果の内容は最初の検索要求と同じ形式で、値のみが更新されます。

### 保存された検索 - REST のみ

**保存された検索の一覧を要求:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2014-10-10
```

サポートされるメソッド: GET PUT DELETE

サポートされるコレクション メソッド: GET

次のテーブルは、使用可能なプロパティについて説明しています。

|プロパティ|説明|
|---|---|
|ID|一意の識別子。|
|ETag|**PATCH の場合は必須**。書き込みごとにサーバーによって更新されます。更新するには、値を、現在格納されている値と等しくするか、"*"' にする必要があります。古い値や無効な値の場合は、409 が返されます。|
|properties.query|**必須**。検索クエリ。|
|properties.displayName|**必須**。ユーザー定義のクエリの表示名。Azure のリソースとしてモデル化されている場合はタグになります。|
|properties.category|**必須**。ユーザー定義のクエリのカテゴリ。Azure のリソースとしてモデル化されている場合はタグになります。|

>[AZURE.NOTE]Operational Insights の検索 API では現在、保存された検索をワークスペースでポーリングすると、ユーザー作成の保存された検索が返されます。このとき、ソリューションで提供されている保存された検索は返されません。この機能は、今後追加される予定です。

### 保存された検索の削除

**要求:**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10
```

### 保存された検索の更新

 **要求:**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime'2015-04-16T23%3A35%3A35.3182423Z'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10 $savedSearchParametersJson
```

### メタデータ - JSON のみ

ここでは、ワークスペースで収集したデータのすべてのログの種類に対応したフィールドを表示する方法を示します。たとえば、イベントの種類に "Computer" という名前のフィールドがあるかどうかを知りたい場合は、この方法を使用して、検索して確認できます。

**フィールドの要求:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2014-10-10
```

**応答:**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

次のテーブルは、使用可能なプロパティについて説明しています。

|**プロパティ**|**説明**|
|---|---|
|name|フィールド名。|
|displayName|フィールドの表示名。|
|type|フィールド値の型。|
|facetable|現在の "Indexed"、"stored"、"facet" の各プロパティの組み合わせ。|
|display|現在の "display" プロパティ。フィールドが検索で表示される場合は true。|
|ownerType|オンボードされた IP アドレスに属している型のみに限定されます。|


## 省略可能なパラメーター
以下では、使用可能なオプションのパラメーターについて説明します。

### 強調表示

“highlight” パラメーターを使用して、マーカーのセットを応答に含めるよう検索サブシステムに要求できます。

これらのマーカーは、検索クエリで指定した語句に一致して強調表示されるテキストの開始と終了を示します。強調表示される語句を囲むために検索で使用される開始マーカーと終了マーカーを指定できます。

**検索クエリの例**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
```

**サンプル結果:**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

上記の結果にはプレフィックスおよび追加されたエラー メッセージが含まれています。

<!---HONumber=August15_HO6-->