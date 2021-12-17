---
title: Azure Application Insights の IP アドレスの収集 | Microsoft Docs
description: Application Insights が IP アドレスと位置情報を処理する方法について説明します。
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 28b3fb6242d496be7962961d36db3a85c6187d8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745890"
---
# <a name="geolocation-and-ip-address-handling"></a>位置情報と IP アドレスの処理

この記事では、Application Insights で位置情報検索と IP アドレスの処理がどのように機能するか、また、既定の動作を変更する方法について説明します。

## <a name="default-behavior"></a>既定の動作

既定では、IP アドレスは一時的に収集されますが、Application Insights には格納されません。 基本的なプロセスは次のとおりです。

テレメトリが Azure に送信されると、Application Insights は、[MaxMind の GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) を使って位置情報検索を行うために IP アドレスを使用します。 Application Insights は、この検索結果を使用して、`client_City`、`client_StateOrProvince`、`client_CountryOrRegion` の各フィールドを設定します。 アドレスは破棄され、`0.0.0.0` が `client_IP` フィールドに書き込まれます。 

テレメトリの種類は次のとおりです。

* ブラウザー テレメトリ: Application Insights は、送信者の IP アドレスを収集します。 インジェスト エンドポイントは IP アドレスを計算します。
* サーバー テレメトリ:Application Insights テレメトリ モジュールでクライアントの IP アドレスが一時的に収集されます。 `X-Forwarded-For` ヘッダーが設定されている場合、IP アドレスはローカルに収集されません。 受信した IP アドレスの一覧に複数の項目がある場合は、最後の IP アドレスを使用して位置情報フィールドが設定されます。

この動作の仕様は、個人データの不必要な収集を回避するのに役立ちます。 可能な限り、個人データの収集を回避することをお勧めします。 

> [!NOTE]
> 既定では IP アドレスを収集しませんが、この動作はオーバーライドすることができます。 ただし、コレクションがどのコンプライアンス要件にも地域の規制にも違反していないことを確認することをお勧めします。 
>
> Application Insights での個人データ処理の詳細については、[個人データのガイダンス](../logs/personal-data-mgmt.md)に関するページをご覧ください。


## <a name="storage-of-ip-address-data"></a>IP アドレス データのストレージ

IP の収集と格納を有効にするには、Application Insights コンポーネントの `DisableIpMasking` プロパティを `true` に設定する必要があります。 このプロパティは、Azure Resource Manager テンプレートを使用するか、REST API を呼び出すことによって設定できます。 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>ポータル

1 つの Application Insights リソースの動作のみを変更する必要がある場合は、Azure portal を使用します。 

1. Application Insights リソースに移動し、 **[Automation]**  >  **[テンプレートのエクスポート]** を選択します。 

2. **[デプロイ]** を選択します。

    ![赤で強調表示されている [デプロイ] ボタンを示すスクリーンショット。](media/ip-collection/deploy.png)

3. **[テンプレートの編集]** を選択します

    ![赤で強調表示されている [編集] ボタンと共にリソース グループに関する警告が表示されているスクリーンショット。](media/ip-collection/edit-template.png)

    > [!NOTE]
    > 次のエラー (スクリーンショット参照) が発生した場合は解決が可能です。"リソース グループは、テンプレート内の 1 つ以上のリソースがサポートしていない場所にあります。 別のリソース グループを選択してください。" 一時的にドロップダウン リストから別のリソース グループを選択してから、元のリソース グループを再選択します。

4. JSON テンプレートで、`resources` の中に `properties` を特定し、最後の JSON フィールドにコンマを追加して、`"DisableIpMasking": true` という新しい行を追加します。 次に、**[保存]** を選択します。

    ![要求ソースのプロパティの後にコンマと新しい行が追加されたことを示すスクリーンショット。](media/ip-collection/save.png)

5. **[確認と作成]**  >  **[作成]** の順に選択します。

    > [!NOTE]
    > "デプロイに失敗しました" と表示されている場合は、`microsoft.insights/components` という種類のデプロイの詳細を確認し、状態を確認します。 成功した場合は、`DisableIpMasking` に加えられた変更がデプロイされます。

6. デプロイが完了すると、新しいテレメトリ データが記録されます。

    テンプレートをもう一度選択して編集すると、新しく追加されたプロパティを使用せずに既定のテンプレートのみが表示されます。 IP アドレス データが表示されず、`"DisableIpMasking": true` が設定されていることを確認する必要がある場合、次の PowerShell コマンドを実行します。
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using Azure Cloud Shell, you need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    結果として、プロパティの一覧が返されます。 プロパティのいずれかが `DisableIpMasking: true` になっているはずです。 Azure Resource Manager で新しいプロパティをデプロイする前に PowerShell コマンドを実行する場合、プロパティは存在しません。

### <a name="rest-api"></a>Rest API

同じ変更を行うための [Rest API](/rest/api/azure/) ペイロードは次のとおりです。

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>テレメトリ初期化子

`DisableIpMasking` よりも柔軟な代替方法が必要な場合は、[テレメトリ初期化子](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)を使用して、IP アドレスのすべてまたは一部をカスタム フィールドにコピーすることができます。 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet-or-aspnet-core"></a>ASP.NET または ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> `ISupportProperties` にアクセスできない場合は、Application Insights SDK の最新の安定したリリースを実行していることを確認してください。 `ISupportProperties` は、カーディナリティの値が高いことを意図しています。 `GlobalProperties` は、リージョン名や環境名などの小さなカーディナリティの値に適しています。

### <a name="enable-the-telemetry-initializer-for-aspnet"></a>ASP.NET のテレメトリ初期化子を有効にする

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-the-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core のテレメトリ初期化子を有効にする

ASP.NET と同じ方法で、ASP.NET Core テレメトリ初期化子を作成できます。 初期化子を有効にするには、次の例を参考にしてください。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[クライアント側の JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>クライアント側の JavaScript

サーバー側の SDK とは異なり、クライアント側の JavaScript SDK では IP アドレスは計算されません。 既定では、クライアント側のテレメトリの IP アドレス計算は、Azure のインジェスト エンドポイントで実行されます。 

クライアント側で IP アドレスを直接計算する場合は、独自のカスタム ロジックを追加し、結果を使用して `ai.location.ip` タグを設定する必要があります。 `ai.location.ip` が設定されている場合、IP アドレスの計算はインジェスト エンドポイントでは行われず、指定された IP アドレスが、位置情報検索に使用されます。 このシナリオでは、引き続き、既定で IP アドレスがゼロに設定されます。 

カスタム ロジックから計算された IP アドレス全体を保持する場合は、テレメトリ初期化子を使用できます。これにより、`ai.location.ip` で指定した IP アドレス データが別のカスタム フィールドにコピーされます。 ただし、ここでもサーバー側の SDK とは異なり、サードパーティのライブラリや独自のカスタム ロジックに依存しない場合、クライアント側の SDK ではアドレスが計算されません。    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

インジェスト エンドポイントに転送する前に、クライアント側のデータがプロキシを通過した場合、IP アドレスの計算では、クライアントではなく、プロキシの IP アドレスが表示されることがあります。 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>テレメトリ初期化子の結果を表示する

サイトに新しいトラフィックを送信して数分待ってから、クエリを実行してコレクションが動作していることを確認できます。

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新しく収集された IP アドレスは `customDimensions_client-ip` 列に表示されます。 既定の `client-ip` 列では、4 つすべてのオクテットがゼロのままになります。 

localhost からテストし、`customDimensions_client-ip` の値が `::1` の場合、この値は想定される動作です。 `::1` の値は、IPv6 のループバック アドレスを表します。 これは、IPv4 での `127.0.0.1` と同じです。

## <a name="next-steps"></a>次のステップ

* Application Insights での[個人データ収集](../logs/personal-data-mgmt.md)について、さらに学習します。

* Application Insights の [IP アドレス収集](https://apmtips.com/posts/2016-07-05-client-ip-address/)のしくみについて、さらに学習します  この記事は、Microsoft のエンジニアの 1 人が書き込んだ外部の古いブログ投稿です。 IP アドレスが `0.0.0.0` として記録される現在の既定の動作より前のものですが、組み込みのテレメトリ初期化子のメカニズムについてより詳しく説明されています。
