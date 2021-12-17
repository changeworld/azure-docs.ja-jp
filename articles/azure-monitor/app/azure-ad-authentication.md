---
title: Application Insights に対する Azure AD Authentication (プレビュー)
description: Azure Active Directory (Azure AD) 認証を有効にして、認証されたテレメトリのみが Application Insights リソースに取り込まれるようにする方法について説明します。
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 573a7807f6561dfb326bfa247b12ccafa0857152
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359865"
---
# <a name="azure-ad-authentication-for-application-insights-preview"></a>Application Insights に対する Azure AD Authentication (プレビュー)
Application Insights では、新たに Azure Active Directory (Azure AD) 認証がサポートされています。 認証されたテレメトリのみが Application Insights リソースに取り込まれるよう、Azure AD を使用して設定できるようになりました。 

一般的には、さまざまな認証システムを使用すると、資格情報の大規模な管理が困難なため、煩雑であり、リスクも高まります。 [マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) と [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) を使用して排他的に認証されたテレメトリのみが Application Insights リソースに取り込まれるよう、ローカル認証の無効化を選択することが可能になりました。 この機能は、(アラートやオートスケールなどの) 運用上の重要な決定と、ビジネスの意思決定の両方を行うために使用されるテレメトリのセキュリティと信頼性を強化するためのステップです。

> [!IMPORTANT]
> Azure AD 認証は現在プレビューの段階です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

以下の SDK とシナリオは、パブリック プレビューではサポートされていません。
- [Application Insights Java 2.x SDK](java-2x-agent.md) – Azure AD 認証は Application Insights Java Agent 3.2.0 以降でのみ使用できます。 
- [ApplicationInsights JavaScript Web SDK](javascript.md) 
- [Application Insights OpenCensus Python SDK](opencensus-python.md) と Python バージョン 3.4 および 3.5
- [証明書/シークレット ベースの Azure AD](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) は運用環境では推奨されていません。 代わりにマネージド ID を使用してください。 
- App Service、VM/仮想マシン スケール セット、Azure Functions などに対して既定でオンになっているコードレス監視 (言語)。
- [可用性テスト](availability-overview.md)
- [Profiler](profiler-overview.md)

## <a name="prerequisites-to-enable-azure-ad-authentication-ingestion"></a>Azure AD 認証のインジェストを有効にするための前提条件

- 次の知識が必要です。
    - [マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) 
    - [サービス プリンシパル](../../active-directory/develop/howto-create-service-principal-portal.md)。
    - [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md) 
- [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)を使用したアクセスを許可するために、リソース グループに対する "所有者" ロールを持っていること。

## <a name="configuring-and-enabling-azure-ad-based-authentication"></a>Azure AD ベースの認証の構成と有効化 

1. まだ ID がない場合、マネージド ID またはサービス プリンシパルを使用して作成します。

    1. マネージド ID の使用 (推奨):

        (VM、App Service などの) [Azure サービス用のマネージド ID を設定します](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。

    1. サービス プリンシパルの使用 (非推奨):

        リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルの作成方法の詳細については、[サービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページを参照してください。

1. Azure サービスにロールを割り当てます。 

    [Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)に関するページの手順に従って、ターゲットの Application Insights リソースからの "Monitoring Metrics Publisher" (メトリック発行元の監視) ロールを、テレメトリの送信元の Azure リソースに追加します。 

    > [!NOTE]
    > "Monitoring Metrics Publisher" ロールは、"メトリック" が名前に含まれていますが、すべてのテレメトリを App Insights リソースに発行します。

1. 以下の言語別の構成ガイダンスに従います。

### <a name="aspnet-and-net"></a>[ASP.NET と .NET](#tab/net)

> [!NOTE]
> Application Insights .NET SDK での Azure AD のサポートは、[バージョン 2.18-Beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.18.0-beta3) 以降に含まれています。

Application Insights .NET SDK では、[Azure Identity](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity#credential-classes) によって提供される資格情報クラスがサポートされています。

- `DefaultAzureCredential` はローカル開発に対して推奨されます。
- `ManagedIdentityCredential` は、システム割り当ておよびユーザー割り当てのマネージド ID に対して推奨されます。
    - システム割り当ての場合は、パラメーターを指定せずに既定のコンストラクターを使用します。
    - ユーザー割り当ての場合は、clientId をコンストラクターに渡します。
- `ClientSecretCredential` はサービス プリンシパルに対して推奨されます。 
    - tenantId、clientId、clientSecret をコンストラクターに渡します。

.NET を使用して手動で `TelemetryConfiguration` を作成および構成する例を次に示します。

```csharp
var config = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
}
var credential = new DefaultAzureCredential();
config.SetAzureTokenCredential(credential);

```

ASP.NET Core を使用して `TelemetryConfiguration` を構成する例を次に示します。
```csharp
services.Configure<TelemetryConfiguration>(config =>
{
       var credential = new DefaultAzureCredential();
       config.SetAzureTokenCredential(credential);
});
services.AddApplicationInsightsTelemetry(new ApplicationInsightsServiceOptions
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
});
```
### <a name="nodejs"></a>[Node.js](#tab/nodejs)
 
> [!NOTE]
> Application Insights Node.JS での Azure AD のサポートは、[バージョン 2.1.0-beta.1](https://www.npmjs.com/package/applicationinsights/v/2.1.0-beta.1) 以降に含まれています。

Application Insights Node.JS では、[Azure Identity](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/identity/identity#credential-classes) によって提供される資格情報クラスがサポートされています。

#### <a name="defaultazurecredential"></a>DefaultAzureCredential

```javascript
let appInsights = require("applicationinsights");
import { DefaultAzureCredential } from "@azure/identity"; 
 
const credential = new DefaultAzureCredential();
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

#### <a name="clientsecretcredential"></a>ClientSecretCredential

```javascript
let appInsights = require("applicationinsights");
import { ClientSecretCredential } from "@azure/identity"; 
 
const credential = new ClientSecretCredential(
    "<YOUR_TENANT_ID>",
    "<YOUR_CLIENT_ID>",
    "<YOUR_CLIENT_SECRET>"
  );
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

### <a name="java"></a>[Java](#tab/java)

> [!NOTE]
> Application Insights Java エージェントでの Azure AD のサポートは、[Java 3.2.0-BETA](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0-BETA) 以降に含まれています。 

1. [Java エージェントを使用してアプリケーションを構成します。](java-in-process-agent.md#quickstart)

    > [!IMPORTANT]
    > Java エージェントを使用してアプリを構成するときは、"IngestionEndpoint" を含む完全な接続文字列を使用してください。 たとえば、「 `InstrumentationKey=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX;IngestionEndpoint=https://XXXX.applicationinsights.azure.com/` 」のように指定します。

    > [!NOTE]
    >  2\.X SDK から 3.X Java エージェントへの移行の詳細については、「[Application Insights Java 2.x SDK からのアップグレード](java-standalone-upgrade-from-2x.md)」を参照してください。

1. 使用している認証に応じて、json 構成を ApplicationInsights.json 構成ファイルに追加します。 ユーザーがマネージド ID を使用することを推奨します。

#### <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

次に示すのは、Azure AD を使用した認証にシステム割り当てマネージド ID を使用するように Java エージェントを構成する方法の例です。

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "SAMI" 
    } 
  } 
} 
```

#### <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

次に示すのは、Azure AD を使用した認証にユーザー割り当てマネージド ID を使用するように Java エージェントを構成する方法の例です。

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "UAMI", 
      "clientId":"<USER-ASSIGNED MANAGED IDENTITY CLIENT ID>" 
    } 
  }     
} 
```
:::image type="content" source="media/azure-ad-authentication/user-assigned-managed-identity.png" alt-text="ユーザー割り当てマネージド ID のスクリーンショット。" lightbox="media/azure-ad-authentication/user-assigned-managed-identity.png":::

#### <a name="client-secret"></a>クライアント シークレット

次に示すのは、Azure AD を使用した認証にサービス プリンシパルを使用するように Java エージェントを構成する方法の例です。 このタイプの認証は開発中にのみ使用することをお勧めします。 認証機能を追加することの最終的な目標は、シークレットを排除することです。

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint",
   "preview": { 
        "authentication": { 
          "enabled": true, 
          "type": "CLIENTSECRET", 
          "clientId":"<YOUR CLIENT ID>", 
          "clientSecret":"<YOUR CLIENT SECRET>", 
          "tenantId":"<YOUR TENANT ID>" 
    } 
  } 
} 
```
:::image type="content" source="media/azure-ad-authentication/client-secret-tenant-id.png" alt-text="tenantID と ClientID を持つ Client シークレットのスクリーンショット。" lightbox="media/azure-ad-authentication/client-secret-tenant-id.png":::

:::image type="content" source="media/azure-ad-authentication/client-secret-cs.png" alt-text="クライアント シークレットを持つ Client シークレットのスクリーンショット。" lightbox="media/azure-ad-authentication/client-secret-cs.png":::

### <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Azure AD 認証は、Python v2.7、v3.6、v3.7 でのみ使用できます。 Application Insights Opencensus Python SDK での Azure AD のサポートは、ベータ バージョン [opencensus-ext-azure 1.1b0](https://pypi.org/project/opencensus-ext-azure/1.1b0/) 以降に含まれています。

適切な[資格情報](/python/api/overview/azure/identity-readme#credentials)を作成し、Azure Monitor エクスポーターのコンストラクターに渡します。 リソースのインストルメンテーション キーとインジェスト エンドポイントを使用して接続文字列が設定されていることを確認してください。

Opencensus Azure Monitor エクスポーターでサポートされている認証のタイプを以下に示します。 運用環境ではマネージド ID を使用することをお勧めします。

#### <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential()
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential(client_id="<client-id>")
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="client-secret"></a>クライアント シークレット

```python
from azure.identity import ClientSecretCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tenant_id = "<tenant-id>"
client_id = "<client-id"
client_secret = "<client-secret>"

credential = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...
```
---

## <a name="disable-local-authentication"></a>ローカル認証の無効化

Azure AD 認証が有効になったら、ローカル認証を無効にすることを選択できます。 これによって、Azure AD によって排他的に認証されたテレメトリを取り込むことができ、これは (たとえば、API キーを通じて) データ アクセスに影響を及ぼします。 

ローカル認証は、Azure portal、Azure Policy、またはプログラムを使用して無効化できます。

### <a name="azure-portal"></a>Azure portal

1. Application Insights リソースで、左側のメニューの *[構成]* 見出しの下にある **[プロパティ]** を選択します。 次に、ローカル認証が有効になっている場合は、 **[Enabled (click to change)]\(有効 (クリックして変更)\)** を選択します。 

   :::image type="content" source="./media/azure-ad-authentication/enabled.png" alt-text="[構成] で [プロパティ] が選択され、ローカル認証の [Enabled (click to change)]\(有効 (クリックして変更)\) ボタンが表示されているスクリーンショット。":::

1. **[無効]** を選択し、変更を適用します。

   :::image type="content" source="./media/azure-ad-authentication/disable.png" alt-text="ローカル認証の有効/無効の切り替えボタンが強調表示されたスクリーンショット。":::

1. リソースでローカル認証が無効になると、対応する情報が **[概要]** ペインに表示されます。

   :::image type="content" source="./media/azure-ad-authentication/overview.png" alt-text="[概要] タブで [Disabled (click to change)]\(無効 (クリックして変更)\) が強調表示されたスクリーンショット。":::

### <a name="azure-policy"></a>Azure Policy 

"DisableLocalAuth" の Azure Policy は、このプロパティが "true" に設定されていない場合に、ユーザーが新しい Application Insights リソースを作成することを拒否します。 ポリシー名は "Application Insights components should block non-AAD auth ingestion" (Application Insights コンポーネントは AAD 認証以外のインジェストをブロックする必要がある) です。

このポリシー定義をサブスクリプションに適用するには、[新しいポリシー割り当てを作成してポリシーを割り当てます](../../governance/policy/assign-policy-portal.md)。

ポリシー テンプレートの定義を次に示します。
```JSON
{
    "properties": {
        "displayName": "Application Insights components should block non-AAD auth ingestion",
        "policyType": "BuiltIn",
        "mode": "Indexed",
        "description": "Improve Application Insights security by disabling log ingestion that are not AAD-based.",
        "metadata": {
            "version": "1.0.0",
            "category": "Monitoring"
        },
        "parameters": {
            "effect": {
                "type": "String",
                "metadata": {
                    "displayName": "Effect",
                    "description": "The effect determines what happens when the policy rule is evaluated to match"
                },
                "allowedValues": [
                    "audit",
                    "deny",
                    "disabled"
                ],
                "defaultValue": "audit"
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Insights/components"
                    },
                    {
                        "field": "Microsoft.Insights/components/DisableLocalAuth",
                        "notEquals": "true"                        
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effect')]"
            }
        }
    }
}
```

### <a name="programmatic-enablement"></a>プログラムによる有効化 

プロパティ `DisableLocalAuth` は、Application Insights リソースでローカル認証を無効にするために使用されます。 このプロパティを `true` に設定すると、すべてのアクセスで Azure AD 認証を使用することが必須になります。

次に示すサンプルの Azure Resource Manager テンプレートは、ワークスペースベースの Application Insights リソースを作成してローカル認証を無効にするために使用できます。

```JSON 
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        },
        "disableLocalAuth": {
            "type": "bool"
        }
     
    },
    "resources": [
        {
        "name": "[parameters('name')]",
        "type": "microsoft.insights/components",
        "location": "[parameters('regionId')]",
        "tags": "[parameters('tagsArray')]",
        "apiVersion": "2020-02-02-preview",
        "dependsOn": [],
        "properties": {
            "Application_Type": "[parameters('type')]",
            "Flow_Type": "Redfield",
            "Request_Source": "[parameters('requestSource')]",
            "WorkspaceResourceId": "[parameters('workspaceResourceId')]",
            "DisableLocalAuth": "[parameters('disableLocalAuth')]"
            }
    }
 ]
}

```

## <a name="troubleshooting"></a>トラブルシューティング

ここでは、サポート チケットを発行する前にユーザーが問題解決のために実行できる各種トラブルシューティングのシナリオと手順について説明します。 

### <a name="ingestion-http-errors"></a>インジェスト HTTP エラー

インジェスト サービスでは、SDK 言語に関係なく、特定のエラーを返します。 ネットワーク トラフィックは、Fiddler などのツールを使用して収集できます。 接続文字列に設定されたインジェスト エンドポイントへのトラフィックをフィルター処理する必要があります。

#### <a name="http11-400-authentication-not-support"></a>HTTP/1.1 400 Authentication not support (認証がサポートされていません) 

これは、Application Insights リソースは Azure AD 専用に構成されているが、SDK が正しく構成されておらず、正しくない API に送信していることを示しています。

> [!NOTE]
>  "v2/track" では Azure AD はサポートされていません。 SDK が正しく構成されると、テレメトリは "v2.1/track" に送信されます。

次のステップで、SDK 構成を確認する必要があります。

#### <a name="http11-401-authorization-required"></a>HTTP/1.1 401 Authorization required (認証が必要です)

これは、SDK は正しく構成されているが、有効なトークンを取得できなかったことを示しています。 これは、Azure Active Directory の問題を示している可能性があります。

次の手順として、SDK ログの例外、または Azure ID からのネットワーク エラーを特定する必要があります。

#### <a name="http11-403-unauthorized"></a>HTTP/1.1 403 Unauthorized (権限がありません) 

これは、Application Insights のリソースまたはサブスクリプションに対するアクセス許可を付与されていない資格情報を使用して SDK が構成されていることを示します。

次の手順として、Application Insights リソースのアクセスの制御を確認する必要があります。 SDK は、"監視メトリック発行者" ロールが付与された資格情報を使用して構成されている必要があります。

### <a name="language-specific-troubleshooting"></a>言語固有のトラブルシューティング

### <a name="aspnet-and-net"></a>[ASP.NET と .NET](#tab/net)

#### <a name="event-source"></a>イベント ソース

Application Insights .NET SDK は、イベント ソースを使用してエラー ログを出力します。 イベント ソース ログの収集の詳細については、[「データが存在しない場合のトラブルシューティング」の「PerfView でログを収集する」](asp-net-troubleshoot-no-data.md#PerfView)を参照してください。

SDK でトークンを取得できなかった場合、次の例外メッセージがログに記録されます: "AAD トークンを取得できませんでした。 エラー メッセージ:"

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

内部ログは、次の設定を使用して有効にすることができます。 これを有効にすると、Azure AD 統合に関連したエラーを含むエラー ログがコンソールに表示されます。 たとえば、指定した資格情報が誤っていた場合のトークン生成失敗や、指定した資格情報を使用してインジェスト エンドポイントを認証できなかった場合のエラーです。

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").setInternalLogging(true, true);
```

### <a name="java"></a>[Java](#tab/java)

#### <a name="http-traffic"></a>HTTP トラフィック

Fiddler などのツールを使用してネットワーク トラフィックを検査できます。 Fiddler 経由でトラフィックをトンネリングできるようにするには、構成ファイルに次のプロキシ設定を追加します。

```JSON
"proxy": {
"host": "localhost",
"port": 8888
}
```

または、アプリケーションの実行中に次の jvm 引数を追加します: `-Djava.net.useSystemProxies=true -Dhttps.proxyHost=localhost -Dhttps.proxyPort=8888`

エージェントで Azure AD が有効な場合、送信トラフィックには HTTP ヘッダー "Authorization" が含まれます。


#### <a name="401-unauthorized"></a>401 権限がありません 

次の WARN メッセージがログ ファイル `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 401, please check your credentials` に記録されている場合、エージェントがテレメトリを送信できなかったことを示しています。 おそらくは、エージェントで Azure AD 認証を有効にしていない一方で、Application Insights リソースでは `DisableLocalAuth: true` が構成されています。 有効な資格情報を渡しており、Application Insights リソースにアクセスするためのアクセス許可をその資格情報が持っていることを確認してください。


Fiddler を使用している場合、応答ヘッダーが次のようになることがあります: `HTTP/1.1 401 Unauthorized - please provide the valid authorization token`。


#### <a name="credentialunavailableexception"></a>CredentialUnavailableException

次の例外がログ ファイル `com.azure.identity.CredentialUnavailableException: ManagedIdentityCredential authentication unavailable. Connection to IMDS endpoint cannot be established` に記録されている場合、エージェントがアクセス トークンを取得できなかったことを示しています。ユーザー割り当てマネージド ID の構成で、無効な `clientId` を指定したことが原因である可能性があります。


#### <a name="failed-to-send-telemetry"></a>Failed to send telemetry (テレメトリを送信できませんでした)

次の WARN メッセージがログ ファイル `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 403, please check your credentials` に記録されている場合、エージェントがテレメトリを送信できなかったことを示しています。 原因として考えられるのは、指定された資格情報では、テレメトリをコンポーネントに取り込むためのアクセスが許可されていないことです。

Fiddler を使用している場合、応答ヘッダーが次のようになることがあります: `HTTP/1.1 403 Forbidden - provided credentials do not grant the access to ingest the telemetry into the component`。

根本原因として考えられるのは、次のいずれかです。
- システム割り当てマネージド ID を有効にしてリソースを作成した。または、ユーザー割り当て ID をリソースに関連付けたが、`Monitoring Metrics Publisher` ロールをリソース (SAMI を使用している場合) またはユーザー割り当て ID (UAMI を使用している場合) に追加するのを忘れていた可能性がある。
- アクセス トークンを取得するための適切な資格情報を指定したが、その資格情報が適切な Application Insights リソースに属していない。 Application Insights リソースで、リソース (VM、App Service など) またはユーザー割り当て ID に `Monitoring Metrics Publisher` ロールが付与されていることを確認してください。

#### <a name="invalid-tenantid"></a>無効な TenantId

次の例外がログ ファイル `com.microsoft.aad.msal4j.MsalServiceException: Specified tenant identifier <TENANT-ID> is neither a valid DNS name, nor a valid external domain.` に記録されている場合、エージェントがアクセス トークンを取得できなかったことを示しています。クライアント シークレット構成で、無効または誤った `tenantId` を指定したことが原因である可能性があります。

#### <a name="invalid-client-secret"></a>無効なクライアント シークレット

次の例外がログ ファイル `com.microsoft.aad.msal4j.MsalServiceException: Invalid client secret is provided` に記録されている場合、エージェントがアクセス トークンを取得できなかったことを示しています。クライアント シークレット構成で、無効な `clientSecret` を指定したことが原因である可能性があります。


#### <a name="invalid-clientid"></a>無効な ClientId

次の例外がログ ファイル `com.microsoft.aad.msal4j.MsalServiceException: Application with identifier <CLIENT_ID> was not found in the directory` に記録されている場合、エージェントがアクセス トークンを取得できなかったことを示しています。クライアント シークレット構成で、無効または誤った "clientId" を指定したことが原因である可能性があります。

 このエラーは、アプリケーションがテナントの管理者によってインストールされていない場合や、アプリケーションがテナント内のいずれのユーザーによっても同意されていない場合に発生することがあります。 間違ったテナントに認証要求を送信した可能性があります。

### <a name="python"></a>[Python](#tab/python)

#### <a name="error-starts-with-credential-error-with-no-status-code"></a>"credential error" で始まるエラー (ステータス コードなし)

使用している資格情報に何らかの問題があるため、クライアントは認可用のトークンを取得できません。 これは通常、状態に関する必要なデータの欠落が原因です。 たとえば、システムの ManagedIdentityCredential を渡したが、リソースはシステム管理 ID を使用するように構成されていない場合などです。

#### <a name="error-starts-with-authentication-error-with-no-status-code"></a>"authentication error" で始まるエラー (ステータス コードなし)

クライアントは、指定された資格情報で認証できませんでした。 通常は、使用する資格情報のロール割り当てが正しくない場合に発生します。

#### <a name="im-getting-a-status-code-400-in-my-error-logs"></a>エラー ログにステータス コード 400 が記録されている

可能性が高い原因は、資格情報の欠落です。または、資格情報が `None` に設定されているが、Application Insights リソースは `DisableLocalAuth: true` を使用して構成されていることです。 有効な資格情報を渡しており、Application Insights リソースにアクセスするためのアクセス許可をその資格情報が持っていることを確認してください。

#### <a name="im-getting-a-status-code-403-in-my-error-logs"></a>エラー ログにステータス コード 403 が記録されている

通常は、指定した資格情報で、Application Insights リソースのテレメトリを取り込むためのアクセスが許可されていない場合に発生します。 AI リソースのロール割り当てが正しいことを確認してください。

---
## <a name="next-steps"></a>次のステップ
* [ポータルでテレメトリを監視する](overview-dashboard.md)
* [Live Metrics Stream を使用して診断する](live-stream.md)
