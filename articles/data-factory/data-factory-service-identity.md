---
title: マネージド ID
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse でのマネージド ID の使用方法について説明します。
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: 8e1eaad6d25623c9cb1697137fdb69340cb6aeca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846123"
---
# <a name="managed-identity-for-azure-data-factory-and-azure-synapse"></a>Azure Data Factory および Azure Synapse 用のマネージド ID 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事は、マネージド ID (旧称はマネージド サービス ID/MSI) について、さらにそれが Azure Data Factory および Azure Synapse でどのように機能するのかについて理解するのに役立ちます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要

マネージド ID により、資格情報を管理する必要がなくなります。 マネージド ID では、Azure Active Directory (Azure AD) 認証をサポートするリソースに接続するときに、サービス インスタンスの ID が提供されます。 たとえば、サービスでは、マネージド ID を使用することで、データ開発者が安全に資格情報を格納できる [Azure Key Vault](../key-vault/general/overview.md) などのリソースにアクセスしたり、ストレージ アカウントにアクセスしたりできるようになります。 サービスでは、マネージド ID を使用して Azure AD トークンを取得します。

次の 2 種類のマネージド ID がサポートされています。 

- **システム割り当て**: サービス インスタンスでマネージド ID を直接有効にできます。 サービスの作成時にシステム割り当てマネージド ID を許可すると、そのサービス インスタンスのライフサイクルに関連付けられている ID が Azure AD で作成されます。 その ID を使用して Azure AD にトークンを要求できるのは、必然的に、その Azure リソースのみとなります。 したがって、リソースが削除されると、その ID も Azure によって自動的に削除されます。 Azure Synapse Analytics では、Synapse ワークスペースと共に、システム割り当てマネージド ID を作成する必要があります。
- **ユーザー割り当て:** スタンドアロンの Azure リソースとしてマネージド ID を自分で作成することもできます。 [ユーザー割り当てマネージド ID を作成](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)して、それをデータ ファクトリまたは Synapse ワークスペースの 1 つまたは複数のインスタンスに割り当てることができます。 ユーザー割り当てマネージド ID の場合、ID は、それを使用するリソースとは別に管理されます。

マネージド ID には、次の利点があります。

- [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)。この場合、マネージド ID は Azure Key Vault の認証に使用されます。
- Azure Blob storage、Azure Data Explorer、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics、REST、Databricks アクティビティ、Web アクティビティなどのマネージ ID 認証を使用して、データ ストアまたはコンピューティングにアクセスします。 詳細については、コネクタとアクティビティに関する記事をご覧ください。
- マネージド ID は、Azure Key Vault に格納されているカスタマー マネージド キーを使用してデータやメタデータを暗号化/暗号化解除する場合にも使用されます。こうして、二重暗号化が提供されます。 

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID 

>[!NOTE]
> システム割り当てマネージド ID は、ドキュメント内の他の場所や、Data Factory Studio および Synapse Studio の UI では、下位互換性を考慮して "マネージド ID" とも呼ばれています。 "ユーザー割り当てマネージド ID" を表すときには、そのように明示的に呼ぶことにします。 

### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a>システム割り当てマネージド ID を生成する

システム割り当てマネージド ID は、次のように生成されます。

- **Azure portal または PowerShell** を使用してデータ ファクトリまたは Synapse ワークスペースを作成すると、マネージド ID が常に自動的に作成されます。
- **SDK** を使用してデータ ファクトリまたはワークスペースを作成すると、作成用のファクトリ オブジェクトで "Identity = new FactoryIdentity()" を、またはワークスペース オブジェクトで "Identity = new ManagedIdentity" を指定した場合にのみマネージド ID が作成されます。  [.NET のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-dot-net.md#create-a-data-factory)の例をご覧ください。
- **REST API** を使用してデータ ファクトリまたは Synapse ワークスペースを作成すると、要求本文で "Identity" セクションを指定した場合にのみマネージド ID が作成されます。 [REST のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-rest-api.md#create-a-data-factory)の例をご覧ください。

「[マネージド ID の取得](#retrieve-managed-identity)」の手順に従ってサービス インスタンスにマネージド ID がまだ関連付けられていない場合は、ID イニシエーターを使用してプログラムでそれを更新することで、マネージド ID を明示的に生成できます。

- [PowerShell を使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-powershell)
- [REST API を使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-rest-api)
- [Azure Resource Manager テンプレートを使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [SDK を使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- マネージド ID は変更できません。 マネージド ID が既に存在するサービス インスタンスを更新しても影響はなく、マネージド ID が変更されることはありません。
>- ファクトリまたはワークスペース オブジェクトで "identity" パラメーターを指定せずに、または REST 要求本文で "identity" セクションを指定せずに、マネージド ID が既に存在するサービス インスタンスを更新した場合、エラーが発生します。
>- サービス インスタンスを削除すると、関連付けられているマネージド ID も削除されます。

#### <a name="generate-system-assigned-managed-identity-using-powershell"></a>PowerShell を使用してシステム割り当てマネージド ID を生成する

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

**Set-AzDataFactoryV2** コマンドを呼び出すと、"Identity" フィールドが新たに生成されます。

```powershell
PS C:\> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

**New-AzSynapseWorkspace** コマンドを呼び出すと、"Identity" フィールドが新たに生成されるのがわかります。

```powershell
PS C:\> $creds = New-Object System.Management.Automation.PSCredential ("ContosoUser", $password)
PS C:\> New-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName> -Location <region> -DefaultDataLakeStorageAccountName <storageAccountName> -DefaultDataLakeStorageFileSystem <fileSystemName> -SqlAdministratorLoginCredential $creds

DefaultDataLakeStorage           : Microsoft.Azure.Commands.Synapse.Models.PSDataLakeStorageAccountDetails
ProvisioningState                : Succeeded
SqlAdministratorLogin            : ContosoUser
VirtualNetworkProfile            :
Identity                         : Microsoft.Azure.Commands.Synapse.Models.PSManagedIdentity
ManagedVirtualNetwork            :
PrivateEndpointConnections       : {}
WorkspaceUID                     : <workspaceUid>
ExtraProperties                  : {[WorkspaceType, Normal], [IsScopeEnabled, False]}
ManagedVirtualNetworkSettings    :
Encryption                       : Microsoft.Azure.Commands.Synapse.Models.PSEncryptionDetails
WorkspaceRepositoryConfiguration :
Tags                             :
TagsTable                        :
Location                         : <region>
Id                               : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/
                                   Microsoft.Synapse/workspaces/<workspaceName>
Name                             : <workspaceName>
Type                             : Microsoft.Synapse/workspaces
```
---

#### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>REST API を使用してシステム割り当てマネージド ID を生成する

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

> [!NOTE]
> ファクトリ オブジェクトで **identity** パラメーターを指定せずに、または REST 要求本文で **identity** セクションを指定せずに、マネージド ID が既に存在するサービス インスタンスの更新を試みた場合、エラーが発生します。

要求本文の "identity" セクションで、次の API を呼び出します。

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**要求本文**: add "identity": { "type":"SystemAssigned" } を追加します。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**応答**: マネージド ID が自動的に作成され、これに応じて "identity" セクションが設定されます。

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

> [!NOTE]
> ワークスペース オブジェクトで **identity** パラメーターを指定せずに、または REST 要求本文で **identity** セクションを指定せずに、マネージド ID が既に存在するサービス インスタンスの更新を試みた場合、エラーが発生します。

要求本文の "identity" セクションで、次の API を呼び出します。

```
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**要求本文**: add "identity": { "type":"SystemAssigned" } を追加します。

```json
{
    "name": "<workspaceName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**応答**: マネージド ID が自動的に作成され、これに応じて "identity" セクションが設定されます。

```json
{
    "name": "<workspaceName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2021-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Synapse/workspaces/<workspaceName>",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>"
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用してシステム割り当てマネージド ID を生成する

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
**テンプレート**: add "identity": { "type":"SystemAssigned" } を追加します。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
**テンプレート**: add "identity": { "type":"SystemAssigned" } を追加します。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<workspaceName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Synapse/workspaces",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-sdk"></a>SDK を使用してシステム割り当てマネージド ID を生成する

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Identity=new FactoryIdentity() を指定して、create_or_update 関数を呼び出します。 .NET を使用したサンプル コードは以下のとおりです。

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
```csharp
Workspace workspace = new Workspace
{
    Identity = new ManagedIdentity
    {
        Type = ResourceIdentityType.SystemAssigned
    },
    DefaultDataLakeStorage = new DataLakeStorageAccountDetails
    {
        AccountUrl = <defaultDataLakeStorageAccountUrl>,
        Filesystem = <DefaultDataLakeStorageFilesystem>
    },
    SqlAdministratorLogin = <SqlAdministratorLoginCredentialUserName>
    SqlAdministratorLoginPassword = <SqlAdministratorLoginCredentialPassword>,
    Location = <region>
};
client.Workspaces.CreateOrUpdate(resourceGroupName, workspaceName, workspace);
```
---

### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a>システム割り当てマネージド ID を取得する

マネージド ID は、Azure portal から取得することも、プログラムによって取得することもできます。 以降のセクションでは、いくつかの例を示します。

>[!TIP]
> マネージド ID が表示されない場合は、ご利用のサービス インスタンスを更新して[マネージド ID を生成](#generate-managed-identity)してください。

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Azure portal を使用してシステム割り当てマネージド ID を取得する

Azure portal -> ご自分のデータ ファクトリまたは Synapse ワークスペース -> [プロパティ] でマネージド ID 情報を検索できます。

- マネージド ID オブジェクト ID
- マネージド ID テナント (Azure Data Factory にのみ適用可能)

マネージ ID 情報は、Azure Blob、Azure Data Lake Storage、Azure Key Vault などのマネージド ID 認証をサポートする、リンクされたサービスを作成するときにも表示されます。

アクセス許可を付与する場合は、Azure リソースの [アクセス制御 (IAM)] タブ -> [ロールの割り当ての追加] -> [アクセスの割り当て先] -> [システム割り当てマネージド ID] の [データ ファクトリ] を選択 -> ファクトリ名で選択するか、または、一般に、オブジェクト ID またはデータ ファクトリ名 (マネージド ID 名) を使用して、この ID を検索することができます。 マネージ ID のアプリケーション ID を取得する必要がある場合は、PowerShell を使用できます。

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>PowerShell を使用してシステム割り当てマネージド ID を取得する

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
特定のサービス インスタンスを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。 **PrincipalId** を使用してアクセス権を付与します。

```powershell
PS C:\> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

アプリケーション ID を取得するには、上のプリンシパル ID をコピーし、プリンシパル ID をパラメーターとして指定して次の Azure Active Directory コマンドを実行します。

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
特定のサービス インスタンスを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。 **PrincipalId** を使用してアクセス権を付与します。

```powershell
PS C:\> (Get-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName>).Identity

IdentityType   PrincipalId                          TenantId                            
------------   -----------                          --------                            
SystemAssigned cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

アプリケーション ID を取得するには、上のプリンシパル ID をコピーし、プリンシパル ID をパラメーターとして指定して次の Azure Active Directory コマンドを実行します。

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : <workspaceName>
Id                    : cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05
Type                  : ServicePrincipal
```
---

#### <a name="retrieve-managed-identity-using-rest-api"></a>REST API を使用したマネージド ID の取得

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
特定のサービス インスタンスを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。

要求で次の API を呼び出します。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**応答**:次の例のような応答が返されます。 "identity" セクションに値が適宜入力されます。

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> ARM テンプレートからマネージド ID を取得するには、ARM JSON に **outputs** セクションを追加します。

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
特定のサービス インスタンスを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。

要求で次の API を呼び出します。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**応答**:次の例のような応答が返されます。 "identity" セクションに値が適宜入力されます。

```json
{
  "properties": {
    "defaultDataLakeStorage": {
      "accountUrl": "https://exampledatalakeaccount.dfs.core.windows.net",
      "filesystem": "examplefilesystem"
    },
    "encryption": {
      "doubleEncryptionEnabled": false
    },
    "provisioningState": "Succeeded",
    "connectivityEndpoints": {
      "web": "https://web.azuresynapse.net?workspace=%2fsubscriptions%2{subscriptionId}%2fresourceGroups%2f{resourceGroupName}%2fproviders%2fMicrosoft.Synapse%2fworkspaces%2f{workspaceName}",
      "dev": "https://{workspaceName}.dev.azuresynapse.net",
      "sqlOnDemand": "{workspaceName}-ondemand.sql.azuresynapse.net",
      "sql": "{workspaceName}.sql.azuresynapse.net"
    },
    "managedResourceGroupName": "synapseworkspace-managedrg-f77f7cf2-XXXX-XXXX-XXXX-c4cb7ac3cf4f",
    "sqlAdministratorLogin": "sqladminuser",
    "privateEndpointConnections": [],
    "workspaceUID": "e56f5773-XXXX-XXXX-XXXX-a0dc107af9ea",
    "extraProperties": {
      "WorkspaceType": "Normal",
      "IsScopeEnabled": false
    },
    "publicNetworkAccess": "Enabled",
    "cspWorkspaceAdminProperties": {
      "initialWorkspaceAdminObjectId": "3746a407-XXXX-XXXX-XXXX-842b6cf1fbcc"
    },
    "trustedServiceBypassEnabled": false
  },
  "type": "Microsoft.Synapse/workspaces",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}",
  "location": "eastus",
  "name": "{workspaceName}",
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47",
    "principalId": "cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05"
  },
  "tags": {}
}
```

> [!TIP] 
> ARM テンプレートからマネージド ID を取得するには、ARM JSON に **outputs** セクションを追加します。

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.Synapse/workspaces', parameters('<workspaceName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
---

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

Azure Active Directory では、ユーザー割り当てマネージド ID の作成、削除、管理などを行うことができます。 詳細については、「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」をご覧ください。 

ユーザー割り当てマネージド ID を使用するには、まず、サービス インスタンスで UAMI の[資格情報を作成](credentials.md)する必要があります。

## <a name="next-steps"></a>次の手順

- [資格情報を作成する](credentials.md)。

マネージド ID を使用する状況と方法を紹介する次のトピックをご覧ください。

- [Azure Key Vault に資格情報を格納する](store-credentials-in-key-vault.md)。
- [Azure リソース認証用のマネージド ID を使用して Azure Data Lake Store との間でデータを双方向にコピーする](connector-azure-data-lake-store.md)。

Azure Data Factory および Azure Synapse のマネージド ID の基になっている、Azure リソースのマネージド ID の詳細については、[Azure リソースのマネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関する記事をご覧ください。
