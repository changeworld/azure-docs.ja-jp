---
title: "Azure Stack と外部の監視ソリューションとの統合 | Microsoft Docs"
description: "Azure Stack とデータセンターの外部の監視ソリューションを統合する方法を説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: mabrigg
ms.openlocfilehash: 76499ac959b77e83494bc4f9593c20a99da5c147
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Azure Stack と外部の監視ソリューションとの統合

*適用対象: Azure Stack 統合システム*

Azure Stack インフラストラクチャの外部の監視のためには、Azure Stack ソフトウェア、物理コンピューター、物理ネットワーク スイッチを監視する必要があります。 これらにはそれぞれ、正常性とアラートの情報を取得する方法があります。

- Azure Stack ソフトウェアでは、REST ベースの API を使用して正常性とアラートの情報を取得します。 (記憶域スペース ダイレクトのようなソフトウェアによるテクノロジを使用すると、ストレージの正常性およびアラートはソフトウェアの監視に含まれます。)
- 物理コンピューターでは、ベースボード管理コントローラー (BMC) を通して、正常性とアラートの情報を取得できます。
- 物理ネットワーク デバイスでは、SNMP プロトコルを通して、正常性とアラートの情報を取得できます。

各 Azure Stack ソリューションは、ハードウェア ライフサイクル ホストに含まれています。 このホストは、物理サーバーとネットワーク デバイス用に OEM (Original Equipment Manufacturer) ハードウェア ベンダーの監視ソフトウェアを実行します。 必要に応じて、これらの監視ソリューションを使用せずに、データセンター内の既存の監視ソリューションと直接統合することもできます。

> [!IMPORTANT]
> 使用する外部の監視ソリューションは、エージェントレスである必要があります。 Azure Stack コンポーネント内にサード パーティ製エージェントをインストールすることはできません。

次の図は、Azure Stack 統合システム、ハードウェア ライフサイクル ホスト、外部の監視ソリューション、外部のチケット発行/データ収集システムの間のトラフィック フローを示しています。

![Azure Stack 監視とチケット発行ソリューションとの間のトラフィックを示す図。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

この記事では、Azure Stack を System Center Operations Manager や Nagios などの外部の監視ソリューションと統合する方法を説明します。 PowerShell を使用して、または REST API 呼び出しを介して、アラートをプログラムで操作する方法も説明します。

## <a name="integrate-with-operations-manager"></a>Operations Manager との統合

Azure Stack の外部の監視に Operations Manager を使用できます。 Microsoft Azure Stack 用 System Center 管理パックを使用すれば、複数の Azure Stack デプロイを 1 つの Operations Manager インスタンスで監視できます。 この管理パックは、正常性リソースプロバイダーと更新リソースプロバイダーの REST API を使用して Azure Stack と通信します。 ハードウェア ライフサイクル ホストで実行されている OEM 監視ソフトウェアを使用しない場合は、ベンダー管理パックをインストールして物理サーバーを監視できます。 Operations Manager のネットワーク デバイス検出機能を使用して、ネットワーク スイッチを監視することもできます。

Azure Stack 用管理パックには、次の機能があります。

- 複数の Azure Stack デプロイを監視できます。
- Azure Active Directory (Azure AD) と Active Directory フェデレーション サービス (AD FS) をサポートしています。
- アラートを取得したり終了したりできます。
- 正常性と容量についてのダッシュボードがあります。
- パッチおよび更新プログラム (P&U) 進行時の自動メンテナンス モード検出機能を備えています。
- デプロイ用とリージョン用の強制更新タスクが含まれます。
- リージョンにカスタム情報を追加できます。
- 通知とレポートがサポートされています。

Microsoft Azure Stack 用 System Center 管理パックと、関連するユーザー ガイドを[こちら](https://www.microsoft.com/en-us/download/details.aspx?id=55184)から、または Operations Manager から直接ダウンロードできます。

チケット発行ソリューションについては、System Center Service Manager と Operations Manager を統合できます。 統合された製品コネクタによって双方向通信が可能になり、それによって、Service Manager のサービス要求を解決したあとに Azure Stack と Operations Manager のアラートを終了できます。

次の図は、既存の System Center デプロイと Azure Stack の統合を示しています。 System Center Orchestrator または Service Management Automation (SMA) を使用してさらに Service Manager を自動化し、Azure Stack 内で操作を実行できます。

![OM、Service Manager、SMA との統合を示す図。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Nagios との統合

Nagios 監視プラグインは、制約のない無料ソフトウェア ライセンスである MIT (Massachusetts Institute of Technology) ライセンスの下で使用できるパートナー クラウドベース ソリューションと合わせて開発されました。

このプラグインは Python で書かれており、正常性リソースプロバイダーの REST API を使用します。 また、Azure Stack でアラートを取得したり終了したりする基本的な機能を提供します。 System Center 管理パックと同じように、このプラグインで複数の Azure Stack デプロイを追加したり、通知を送信したりすることが可能になります。

このプラグインは、Nagios Enterprise と Nagios Core で使用可能です。 [こちら](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)からダウンロードできます。 このダウンロード サイトでインストールと詳細な構成も行えます。

### <a name="plugin-parameters"></a>プラグイン パラメーター

プラグイン ファイルの "Azurestack_plugin.py" を次のパラメーターで構成します。

| パラメーター | Description | 例 |
|---------|---------|---------|
| *arm_endpoint* | Azure Resource Manager (管理者) エンドポイント |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure Resource Manager (管理者) エンドポイント  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 管理者のサブスクリプション ID | 管理者ポータルまたは PowerShell で取得します |
| *User_name* | オペレーターのサブスクリプション ユーザー名 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | オペレーターのサブスクリプション パスワード | mypassword |
| *Client_id* | クライアント | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *リージョン* |  Azure Stack のリージョン名 | local |
|  |  |

* 指定の PowerShell GUID はユニバーサルです。 各デプロイ用に使用できます。

## <a name="use-powershell-to-monitor-health-and-alerts"></a>PowerShell を使用した正常性とアラートの監視

Operations Manager、Nagios、または Nagios ベースのソリューションを使用していない場合は、PowerShell でさまざまな監視ソリューションを使用して Azure Stack と統合できます。
 
1. PowerShell を使用するには、Azure Stack オペレーター環境用に [PowerShell がインストールされ構成されている](azure-stack-powershell-configure-quickstart.md)必要があります。 Resource Manager (管理者) エンドポイント (https://adminmanagement.[region].[External_FQDN]) にアクセスできるローカル コンピューターに PowerShell をインストールします。

2. 次のコマンドを実行して、Azure Stack オペレーターとして Azure Stack 環境に接続します。

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. PowerShell のインストールの一環として [Azure Stack ツール](https://github.com/Azure/AzureStack-Tools)をインストールしたディレクトリ (例: c:\azurestack-tools-master) に移動します。 次に、インフラストラクチャのディレクトリに移動し、次のコマンドを実行してインフラストラクチャ モジュールをインポートします。

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. 次の例のようなコマンドを使用して、アラートを操作します。
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>REST API を使用した正常性とアラートの監視

REST API 呼び出しを使用して、アラートを取得したり、アラートを終了したり、リソースプロバイダーの正常性を取得したりできます。

### <a name="get-alert"></a>アラートの取得

**要求**

要求によって、既定のプロバイダー サブスクリプションのすべてのアクティブなアラートと終了したアラートを取得します。 要求の本文はありません。


|メソッド  |要求 URI  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**引数**

|引数  |説明  |
|---------|---------|
|armendpoint     |  ご利用の Azure Stack 環境の Azure Resource Manager エンドポイント。形式は https://adminmanagement.{RegionName}.{External FQDN}。 たとえば、外部の FQDN が *azurestack.external* でリージョン名が *local* の場合、Resource Manager エンドポイントは https://adminmanagement.local.azurestack.external になります。       |
|subid     |   呼び出しを行っているユーザーのサブスクリプション ID。 この API を使用して、既定のプロバイダー サブスクリプションへのアクセス許可を持つユーザーにだけクエリを実行できます。      |
|RegionName     |    Azure Stack デプロイのリージョン名。     |
|api-version     |  この要求を行うために使用するプロトコルのバージョン。 2016-05-01 を使用する必要があります。      |
|     |         |

**応答**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**応答の詳細**


|  引数  |説明  |
|---------|---------|
|*id*     |      アラートの一意の ID。   |
|*name*     |     アラートの内部名。   |
|*type*     |     リソース定義    |
|*location*     |       リージョン名。     |
|*タグ*     |   リソース タグ。     |
|*closedtimestamp*    |  アラートが終了した UTC 時間。    |
|*createdtimestamp*     |     アラートが作成された UTC 時間。   |
|*description*     |    アラートの説明。     |
|*faultid*     | 影響を受けるコンポーネント。        |
|*alertid*     |  アラートの一意の ID。       |
|*faulttypeid*     |  問題のあるコンポーネントの一意の型。       |
|*lastupdatedtimestamp*     |   アラート情報が最後に更新された UTC 時間。    |
|*healthstate*     | 全体的な正常性の状態。        |
|*name*     |   特定のアラートの名前。      |
|*fabricname*     |    問題のあるコンポーネントの登録済みファブリック名。   |
|*description*     |  登録済みファブリック コンポーネントの説明。   |
|*servicetype*     |   登録済みファブリック サービスの種類。   |
|*remediation*     |   推奨の修復手順。    |
|*type*     |   アラートの種類。    |
|*resourceRegistrationid*    |     影響を受ける登録済みリソースの ID。    |
|*resourceProviderRegistrationID*   |    影響を受けるコンポーネントの登録済みリソースプロバイダーの ID。  |
|*serviceregistrationid*     |    登録済みサービスの ID。   |
|*severity*     |     アラートの重要度。  |
|*state*     |    アラートの状態。   |
|*title*     |    アラートのタイトル。   |
|*impactedresourceid*     |     影響を受けるリソースの ID。    |
|*ImpactedresourceDisplayName*     |     影響を受けるリソースの名前。  |
|*closedByUserAlias*     |   アラートを終了したユーザー。      |

### <a name="close-alert"></a>アラートの終了

**要求**

要求が一意の ID によってアラートを終了します。

|メソッド    |要求 URI  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**引数**


|引数  |説明  |
|---------|---------|
|*armendpoint*     |   ご利用の Azure Stack 環境の Resource Manager エンドポイント。形式は https://adminmanagement.{RegionName}.{External FQDN}。 たとえば、外部の FQDN が *azurestack.external* でリージョン名が *local* の場合、Resource Manager エンドポイントは https://adminmanagement.local.azurestack.external になります。      |
|*subid*     |    呼び出しを行っているユーザーのサブスクリプション ID。 この API を使用して、既定のプロバイダー サブスクリプションへのアクセス許可を持つユーザーにだけクエリを実行できます。     |
|*RegionName*     |   Azure Stack デプロイのリージョン名。      |
|*api-version*     |    この要求を行うために使用するプロトコルのバージョン。 2016-05-01 を使用する必要があります。     |
|*alertid*     |    アラートの一意の ID。     |

**本文**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**応答**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**応答の詳細**


|  引数  |説明  |
|---------|---------|
|*id*     |      アラートの一意の ID。   |
|*name*     |     アラートの内部名。   |
|*type*     |     リソース定義    |
|*location*     |       リージョン名。     |
|*タグ*     |   リソース タグ。     |
|*closedtimestamp*    |  アラートが終了した UTC 時間。    |
|*createdtimestamp*     |     アラートが作成された UTC 時間。   |
|*説明*     |    アラートの説明。     |
|*faultid*     | 影響を受けるコンポーネント。        |
|*alertid*     |  アラートの一意の ID。       |
|*faulttypeid*     |  問題のあるコンポーネントの一意の型。       |
|*lastupdatedtimestamp*     |   アラート情報が最後に更新された UTC 時間。    |
|*healthstate*     | 全体的な正常性の状態。        |
|*name*     |   特定のアラートの名前。      |
|*fabricname*     |    問題のあるコンポーネントの登録済みファブリック名。   |
|*description*     |  登録済みファブリック コンポーネントの説明。   |
|*servicetype*     |   登録済みファブリック サービスの種類。   |
|*remediation*     |   推奨の修復手順。    |
|*type*     |   アラートの種類。    |
|*resourceRegistrationid*    |     影響を受ける登録済みリソースの ID。    |
|*resourceProviderRegistrationID*   |    影響を受けるコンポーネントの登録済みリソースプロバイダーの ID。  |
|*serviceregistrationid*     |    登録済みサービスの ID。   |
|*severity*     |     アラートの重要度。  |
|*state*     |    アラートの状態。   |
|*title*     |    アラートのタイトル。   |
|*impactedresourceid*     |     影響を受けるリソースの ID。    |
|*ImpactedresourceDisplayName*     |     影響を受けるリソースの名前。  |
|*closedByUserAlias*     |   アラートを終了したユーザー。      |

### <a name="get-resource-provider-health"></a>リソースプロバイダーの正常性の取得

**要求**

要求によって、すべての登録済みリソースプロバイダーの正常性の状態を取得します。


|メソッド  |要求 URI  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**引数**


|引数  |説明  |
|---------|---------|
|*armendpoint*     |    ご利用の Azure Stack 環境の Resource Manager エンドポイント。形式は https://adminmanagement.{RegionName}.{External FQDN}。 たとえば、外部の FQDN が azurestack.external でリージョン名が local の場合、Resource Manager エンドポイントは https://adminmanagement.local.azurestack.external になります。     |
|*subid*     |     呼び出しを行っているユーザーのサブスクリプション ID。 この API を使用して、既定のプロバイダー サブスクリプションへのアクセス許可を持つユーザーにだけクエリを実行できます。    |
|*RegionName*     |     Azure Stack デプロイのリージョン名。    |
|*api-version*     |   この要求を行うために使用するプロトコルのバージョン。 2016-05-01 を使用する必要があります。      |


**応答**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**応答の詳細**


|引数  |説明  |
|---------|---------|
|*Id*     |   アラートの一意の ID。      |
|*name*     |  アラートの内部名。       |
|*type*     |  リソース定義       |
|*location*     |  リージョン名。       |
|*タグ*     |     リソース タグ。    |
|*registrationId*     |   リソースプロバイダーの一意の登録。      |
|*displayName*     |リソースプロバイダーの表示名。        |
|*namespace*     |   リソースプロバイダーが実装している API 名前空間。       |
|*routePrefix*     |    リソースプロバイダーと通信する URI。     |
|*serviceLocation*     |   このリソースプロバイダーが登録されているリージョン。      |
|*infraURI*     |   インフラストラクチャ ロールとして一覧に表示されているリソースプロバイダーの URI。      |
|*alertSummary*     |   リソースプロバイダーに関連付けられた重大なアラートと警告のアラートの概要。      |
|*healthState*     |    リソースプロバイダーの正常性の状態。     |


### <a name="get-resource-health"></a>リソースの正常性の取得

要求によって、特定の登録済みリソースプロバイダーの正常性の状態を取得します。

**要求**

|メソッド  |要求 URI  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**引数**

|引数  |説明  |
|---------|---------|
|*armendpoint*     |    ご利用の Azure Stack 環境の Resource Manager エンドポイント。形式は https://adminmanagement.{RegionName}.{External FQDN}。 たとえば、外部の FQDN が azurestack.external でリージョン名が local の場合、Resource Manager エンドポイントは https://adminmanagement.local.azurestack.external になります。     |
|*subid*     |呼び出しを行っているユーザーのサブスクリプション ID。 この API を使用して、既定のプロバイダー サブスクリプションへのアクセス許可を持つユーザーにだけクエリを実行できます。         |
|*RegionName*     |  Azure Stack デプロイのリージョン名。       |
|*api-version*     |  この要求を行うために使用するプロトコルのバージョン。 2016-05-01 を使用する必要があります。       |
|*RegistrationID* |特定のリソースプロバイダーの登録 ID。 |

**応答**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**応答の詳細**

|引数  |説明  |
|---------|---------|
|*Id*     |   アラートの一意の ID。      |
|*name*     |  アラートの内部名。       |
|*type*     |  リソース定義       |
|*location*     |  リージョン名。       |
|*タグ*     |     リソース タグ。    |
|*registrationId*     |   リソースプロバイダーの一意の登録。      |
|*resourceType*     |リソースの種類。        |
|*resourceName*     |   リソース名。   |
|*usageMetrics*     |    リソースの使用状況メトリック。     |
|*resourceLocation*     |   デプロイしたリージョンの名前。      |
|*resourceURI*     |   リソースの URI。   |
|*alertSummary*     |   重大なアラート、警告のアラート、正常性の状態の概要。     |

## <a name="next-steps"></a>次のステップ

- 組み込みの正常性監視の詳細については、「[Azure Stack での正常性およびアラートの監視](azure-stack-monitor-health.md)」をご覧ください。


