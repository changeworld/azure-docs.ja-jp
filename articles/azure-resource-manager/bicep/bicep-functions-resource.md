---
title: Bicep 関数 - リソース
description: Bicep ファイルでリソースの値取得に使用する関数について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: a14019e3cdf595efe0a32a7021333aa5b4f6a6b1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087421"
---
# <a name="resource-functions-for-bicep"></a>Bicep のリソース関数

この記事では、リソースの値を取得するための Bicep 関数について説明します。

現在のデプロイから値を取得するには、「[Deployment value functions](./bicep-functions-deployment.md) (デプロイ値関数)」を参照してください。

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

[拡張リソース](../management/extension-resource-types.md)のリソース ID を返します。これは、その機能に追加する別のリソースに適用されるリソースの種類です。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

extensionResourceId 関数は Bicep ファイルで使用できますが、通常は必要はありません。 代わりに、リソースのシンボリック名を使用して、`id` プロパティにアクセスします。

この関数から返されるリソース ID の基本形式は次のとおりです。

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

スコープ セグメントは、拡張されるリソースによって変わります。

拡張リソースが **リソース** に適用される場合、リソース ID は次の形式で返されます。

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

拡張リソースが **リソース グループ** に適用される場合、形式は次のようになります。

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

拡張リソースが **サブスクリプション** に適用される場合、形式は次のようになります。

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

拡張リソースが **管理グループ** に適用される場合、形式は次のようになります。

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

管理グループにデプロイされたカスタム ポリシー定義は、拡張リソースとして実装されます。 ポリシーを作成して割り当てるには、以下の Bicep ファイルを管理グループにデプロイします。

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

---

組み込みのポリシー定義は、テナント レベルのリソースです。 組み込みのポリシー定義をデプロイする例については、「[tenantResourceId](#tenantresourceid)」を参照してください。

## <a name="getsecret"></a>getSecret

`keyVaultName.getSecret(secretName)`

Azure Key Vault からシークレットを返します。 `getSecret` 関数は `Microsoft.KeyVault/vaults` リソースについてのみ呼び出すことができます。 この関数を使用して、Bicep モジュールのセキュリティで保護された文字列パラメーターにシークレットを渡します。 この関数は、`@secure()` デコレーターを持つパラメーターでのみ使用できます。

キー コンテナーでは、`enabledForTemplateDeployment` を `true` に設定する必要があります。 Bicep ファイルをデプロイするユーザーは、シークレットにアクセスできる必要があります。 詳細については、「[Bicep デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](key-vault-parameter.md)」を参照してください。

この関数はリソースの種類と共に使用されるため、[namespace 修飾子](bicep-functions.md#namespaces-for-functions)は必要ありません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| secretName | はい | string | Key Vault に格納されているシークレットの名前。 |

### <a name="return-value"></a>戻り値

シークレット名のシークレット値。

### <a name="example"></a>例

次の Bicep ファイルがモジュールとして使用されます。  これには、*adminPassword* パラメーターが `@secure()` デコレーターで定義されています。

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  ...
}
```

次の Bicep ファイルは、前の Bicep ファイルをモジュールとして使用します。 Bicep ファイルは既存のキー コンテナーを参照し、`getSecret` 関数を呼び出してキー コンテナーのシークレットを取得し、その値をパラメーターとしてモジュールに渡します。

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`resourceName.list([apiVersion], [functionValues])`

`list` で始まる操作を使用して、任意のリソースの種類に対して list 関数を呼び出すことができます。 一般的に使用されるものとして、`list`、`listKeys`、`listKeyValue`、`listSecrets` があります。

この関数の構文は、リスト操作の名前によって異なります。 戻り値も操作によって異なります。 Bicep は現在、`list*` 関数の完了と検証をサポートしていません。

**Bicep バージョン 0.4.412 以降** では、[アクセサー演算子](operators-access.md#function-accessor)を使用して list 関数を呼び出すことができます。 たとえば、「 `stg.listKeys()` 」のように入力します。

この関数はリソースの種類と共に使用されるため、[namespace 修飾子](bicep-functions.md#namespaces-for-functions)は必要ありません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| apiVersion |いいえ |string |このパラメーターを指定しない場合は、リソースの API バージョンが使用されます。 特定のバージョンで関数を実行する必要がある場合にのみ、カスタム API バージョンを指定します。 **yyyy-mm-dd** の形式を使用します。 |
| functionValues |いいえ |object | 関数の値を持つオブジェクト。 このオブジェクトは、ストレージ アカウントの **listAccountSas** など、パラメーター値を持つオブジェクトの受信をサポートする関数に対してのみ指定します。 関数値を渡す例をこの記事で紹介します。 |

### <a name="valid-uses"></a>有効な使用方法

リスト関数は、リソース定義のプロパティで使用できます。 Bicep ファイルの outputs セクションでは、機密情報を公開するリスト関数を使用しないでください。 出力値はデプロイ履歴に格納されるため、悪意のあるユーザーによって取得される可能性があります。

[反復ループ](loops.md)と一緒に使用する場合は、 のリスト関数を使用できます。これは、式がリソース プロパティ`input` に割り当てられるためです。 これらを `count` と一緒に使用することはできません。カウントは、list 関数が解決される前に決定される必要があるためです。

**list** 関数を条件付きでデプロイされるリソースで使用した場合、この関数はリソースがデプロイされていなくても評価されます。 **list** 関数で存在しないリソースを参照した場合、エラーが返されます。 [条件式 **?:** 演算子](./operators-logical.md#conditional-expression--)を使用して、リソースがデプロイされているときにのみ関数が評価されるようにします。

### <a name="return-value"></a>戻り値

返されるオブジェクトは、使用するリスト関数によって異なります。 たとえば、ストレージ アカウントに対して listKeys は次の形式を返します。

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

他のリスト関数の戻り値の形式はさまざまです。 関数の形式を確認するには、Bicep ファイルの例に示すように、outputs セクションにその関数を含めてください。

### <a name="list-example"></a>リストの例

次の例では、ストレージ アカウントをデプロイし、そのストレージ アカウントで listKeys を呼び出します。 このキーは、[デプロイ スクリプト](../templates/deployment-script-template.md)の値を設定するときに使用されます。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'dscript${uniqueString(resourceGroup().id)}'
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource dScript 'Microsoft.Resources/deploymentScripts@2019-10-01-preview' = {
  name: 'scriptWithStorage'
  location: location
  ...
  properties: {
    azCliVersion: '2.0.80'
    storageAccountSettings: {
      storageAccountName: stg.name
      storageAccountKey: stg.listKeys().keys[0].value
    }
    ...
  }
}
```

次の例は、パラメーターを受け取るリスト関数を示しています。 この場合の関数は **listAccountSas** です。 有効期限のオブジェクトを渡します。 有効期限は将来の日付にする必要があります。

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: stg.listAccountSas('2021-04-01', accountSasProperties).accountSasToken
```

### <a name="implementations"></a>実装

list* の使用例を次の表にまとめています。

| リソースの種類 | 関数名 |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/authorization-server/list-secrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2021-04-01-preview/gateway/list-keys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/identity-provider/list-secrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2021-04-01-preview/named-value/list-value) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/openid-connect-provider/list-secrets) |
| Microsoft.ApiManagement/service/subscriptions | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/subscription/list-secrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | listoutputfiles |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/get-build-source-upload-url) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/2020-09-01/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/2020-09-01/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-connection-strings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-keys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-04-15/notebook-workspaces/list-connection-info) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/compute/list-keys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/compute/list-nodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspaces/list-keys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft.RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/2021-04-01-preview/admin-keys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/preview/queues-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20%E2%80%93%20authorization%20rules/list-keys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Synapse/workspaces/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

どのリソースの種類にリスト操作が含まれているのかを判断するには、次のオプションを使用できます。

* リソース プロバイダーの [REST API の操作](/rest/api/)に関するページを参照して、リスト操作を検索します。 たとえば、ストレージ アカウントには [listKeys 操作](/rest/api/storagerp/storageaccounts)があります。
* [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell コマンドレットを使用します。 次の例では、ストレージ アカウントのすべてのリスト操作が取得されます。

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* 次の Azure CLI コマンドを使って、リスト操作のみをフィルター処理します。

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

リソースの種類がリージョンのゾーンをサポートしているかどうかを判断します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| providerNamespace | はい | string | ゾーンのサポートについて確認するためのリソースの種類のリソース プロバイダーの名前空間。 |
| resourceType | はい | string | ゾーンのサポートについて確認するためのリソースの種類。 |
| location | はい | string | ゾーンのサポートについて確認するためのリージョン。 |
| numberOfZones | いいえ | 整数 (integer) | 返される論理ゾーンの数。 既定値は 1 です。 この数は、1 から 3 までの正の整数である必要があります。  単一ゾーンのリソースには 1 を使用します。 複数ゾーンのリソースの場合、サポートされているゾーンの数以下の値である必要があります。 |
| offset | いいえ | 整数 (integer) | 開始論理ゾーンからのオフセット。 オフセットと numberOfZones の合計が、サポートされているゾーンの数を超えた場合、関数はエラーを返します。 |

### <a name="return-value"></a>戻り値

サポートされているゾーンを含む配列。 Offset および numberOfZones の既定値を使用する場合、ゾーンをサポートするリソースの種類とリージョンは次の配列を返します。

```json
[
    "1"
]
```

`numberOfZones` パラメーターが 3 に設定されている場合は、次を返します。

```json
[
    "1",
    "2",
    "3"
]
```

リソースの種類またはリージョンがゾーンをサポートしていない場合は、空の配列が返されます。

```json
[
]
```

### <a name="pickzones-example"></a>pickZones の例

次のBicep ファイルは、pickZones 関数を使用した場合の 3 つの結果を示しています。

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

前の例からの出力は、3 つの配列を返します。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| サポート対象 | array | [ "1" ] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

ゾーンに対して null を指定するか、別のゾーンに仮想マシンを割り当てるかを決定するために、pickZones からの応答を使用することができます。

## <a name="providers"></a>providers

**providers 関数は非推奨となりました。** これの使用は推奨されていません。 この関数を使用してリソース プロバイダーの API バージョンを取得した場合は、テンプレートで特定の API バージョンを指定することをお勧めします。 動的に返された API バージョンを使用すると、プロパティがバージョン間で変更された場合にテンプレートが破損する可能性があります。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

リソースのランタイム状態を表すオブジェクトを返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

reference 関数は Bicep ファイルで使用できますが、通常は必要はありません。 代わりに、リソースのシンボリック名を使用します。

次の例では、ストレージ アカウントをデプロイします。 ストレージ アカウントのシンボリック名 `stg` を使用してプロパティを返します。

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

テンプレートにデプロイされていない既存のリソースからプロパティを取得するには、`existing` キーワードを使用します。

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
output blobAddress string = stg.properties.primaryEndpoints.blob
```

存在しないリソースを参照しようとすると、`NotFound`エラーが発生し、デプロイは失敗します。

詳細については、「[Reference resources](./compare-template-syntax.md#reference-resources) (参照リソース)」と「[JSON template reference function](../templates/template-functions-resource.md#reference) (JSON テンプレート reference 関数)」を参照してください。

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

リソースの一意の識別子を返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

resourceId 関数は Bicep ファイルで使用できますが、通常は必要はありません。 代わりに、リソースのシンボリック名を使用して、`id` プロパティにアクセスします。

リソース名があいまいであるか、同じ Bicep ファイル内でプロビジョニングされていないときに、この関数を使用します。 返される ID の形式は、デプロイがリソース グループ、サブスクリプション、管理グループ、またはテナントのスコープで行われるかどうかによって異なります。

次に例を示します。

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageID string = stg.id
```

Bicep ファイルにデプロイされていないリソースのリソース ID を取得するには、既存のキーワードを使用します。

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
    name: storageAccountName
}

output storageID string = stg.id
```

詳細については、「[JSON template resourceId function](../templates/template-functions-resource.md#resourceid) (JSON テンプレート resourceId 関数)」を参照してください

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

サブスクリプション レベルでデプロイされたリソースの一意の識別子を返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

subscriptionResourceId 関数は Bicep ファイルで使用できますが、通常は必要はありません。 代わりに、リソースのシンボリック名を使用して、`id` プロパティにアクセスします。

識別子は、次の形式で返されます。

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>解説

この関数を使用すると、リソース グループではなく、[サブスクリプションにデプロイ](deploy-to-subscription.md)されているリソースのリソース ID を取得できます。 返される ID は、リソース グループ値が含まれていないという点で、[resourceId](#resourceid) 関数から返される値とは異なります。

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID の例

次の Bicep ファイルでは、組み込みのロールが割り当てられます。 リソース グループまたはサブスクリプションにデプロイできます。 また、subscriptionResourceId 関数を使って、組み込みロールのリソース ID を取得することができます。

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: guid(resourceGroup().id, principalId, roleDefinitionId[builtInRoleType].id)
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

テナント レベルでデプロイされたリソースの一意の識別子を返します。

名前空間: [az](bicep-functions.md#namespaces-for-functions)。

tenantResourceId 関数は Bicep ファイルで使用できますが、通常は必要はありません。 代わりに、リソースのシンボリック名を使用して、`id` プロパティにアクセスします。

識別子は、次の形式で返されます。

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

組み込みのポリシー定義は、テナント レベルのリソースです。 組み込みのポリシー定義を参照するポリシー割り当てをデプロイするには、tenantResourceId 関数を使用します。

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

## <a name="next-steps"></a>次のステップ

* 現在のデプロイから値を取得するには、「[Deployment value functions](./bicep-functions-deployment.md) (デプロイ値関数)」を参照してください。
* リソースの種類を作成するときに指定した回数反復処理を行う場合は [Bicep での反復ループ](loops.md)を参照してください。
