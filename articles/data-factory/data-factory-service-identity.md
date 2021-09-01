---
title: Data Factory のマネージド ID
description: Azure Data Factory のマネージド ID について説明します。
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a350553659ea6028e3fb2079f790d14ae1653a86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746565"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory のマネージド ID

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事は、Data Factory のマネージド ID (以前は、マネージド サービス ID/MSI と呼ばれていた) の概要と機能について理解するのに役立ちます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要

データ ファクトリのマネージド ID により、データ エンジニアは資格情報を管理する必要がなくなります。 マネージド ID では、Azure Active Directory (Azure AD) 認証をサポートするリソースに接続するときに、Data Factory インスタンスの ID が提供されます。 たとえば、Data Factory では、マネージド ID を使用することで、データ開発者が安全に資格情報を格納できる [Azure Key Vault](../key-vault/general/overview.md) などのリソースにアクセスしたり、ストレージ アカウントにアクセスしたりできるようになります。 Data Factory では、マネージド ID を使用して Azure AD トークンが取得されます。

Data Factory では次の 2 種類のマネージド ID がサポートされています。 

- **システム割り当て:** Data factory では、サービス インスタンスに対して直接マネージド ID を有効にすることができます。 データ ファクトリの作成時にシステム割り当てマネージド ID を許可すると、そのサービス インスタンスのライフサイクルに関連付けられている ID が Azure AD で作成されます。 その ID を使用して Azure AD にトークンを要求できるのは、必然的に、その Azure リソースのみとなります。 したがって、リソースが削除されると、その ID も Azure によって自動的に削除されます。
- **ユーザー割り当て:** スタンドアロンの Azure リソースとしてマネージド ID を自分で作成することもできます。 [ユーザー割り当てマネージド ID を作成](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)して、それをデータ ファクトリの 1 つまたは複数のインスタンスに割り当てることができます。 ユーザー割り当てマネージド ID の場合、ID は、それを使用するリソースとは別に管理されます。



Data Factory のマネージド ID には、次の利点があります。

- [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)。この場合、データ ファクトリのマネージド ID は Azure Key Vault の認証に使用されます。
- Azure Blob storage、Azure Data Explorer、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics、REST、Databricks アクティビティ、Web アクティビティなどのマネージ ID 認証を使用して、データ ストアまたはコンピューティングにアクセスします。 詳細については、コネクタとアクティビティに関する記事をご覧ください。
- ユーザー割り当てマネージド ID は、Azure Key Vault に格納されているカスタマー マネージド キーを使用してデータ ファクトリのメタデータを暗号化/暗号化解除する場合にも使用されます。こうして、二重暗号化が提供されます。 

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID 

>[!NOTE]
> システム割り当てマネージド ID は、旧バージョンとの下位互換性のために、データ ファクトリのドキュメントおよびデータ ファクトリ UI では "マネージド ID" とも呼ばれます。 "ユーザー割り当てマネージド ID" を表すときには、そのように明示的に呼ぶことにします。 

#### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a>システム割り当てマネージド ID を生成する

Data Factory のシステム割り当てマネージド ID は、次のように生成されます。

- **Azure portal または PowerShell** を使用してデータ ファクトリを作成すると、マネージド ID が常に自動的に作成されます。
- **SDK** を使用してデータ ファクトリを作成すると、作成用のファクトリ オブジェクトで "Identity = new FactoryIdentity()" を指定した場合にのみマネージド ID が作成されます。 [.NET のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-dot-net.md#create-a-data-factory)の例をご覧ください。
- **REST API** を使用してデータ ファクトリを作成すると、要求本文で "Identity" セクションを指定した場合にのみマネージド ID が作成されます。 [REST のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-rest-api.md#create-a-data-factory)の例をご覧ください。

「[マネージド ID の取得](#retrieve-managed-identity)」の手順に従ってデータ ファクトリにマネージド ID がまだ関連付けられていない場合は、ID イニシエーターを使用してプログラムでデータ ファクトリを更新することで、マネージド ID を明示的に生成できます。

- [PowerShell を使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-powershell)
- [REST API を使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-rest-api)
- [Azure Resource Manager テンプレートを使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [SDK を使用したマネージド ID の生成](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- マネージド ID は変更できません。 マネージド ID が既に存在するデータ ファクトリを更新しても影響はなく、マネージド ID が変更されることはありません。
>- ファクトリ オブジェクトで "identity" パラメーターを指定せずに、または REST 要求本文で "identity" セクションを指定せずに、マネージド ID が既に存在するデータ ファクトリを更新した場合、エラーが発生します。
>- データ ファクトリを削除すると、関連付けられているマネージド ID も削除されます。

##### <a name="generate-system-assigned-managed-identity-using-powershell"></a>PowerShell を使用してシステム割り当てマネージド ID を生成する

**Set-AzDataFactoryV2** コマンドを呼び出すと、"Identity" フィールドが新たに生成されます。

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

##### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>REST API を使用してシステム割り当てマネージド ID を生成する

要求本文の "identity" セクションを使用して、次の API を呼び出します。

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
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

##### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用してシステム割り当てマネージド ID を生成する

**テンプレート**: add "identity": { "type":"SystemAssigned" } を追加します。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

##### <a name="generate-system-assigned-managed-identity-using-sdk"></a>SDK を使用してシステム割り当てマネージド ID を生成する

Identity=new FactoryIdentity() を指定して、データ ファクトリの作成または更新関数を呼び出します。 .NET を使用したサンプル コードは以下のとおりです。

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

#### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a>システム割り当てマネージド ID を取得する

マネージド ID は、Azure portal から取得することも、プログラムによって取得することもできます。 以降のセクションでは、いくつかの例を示します。

>[!TIP]
> マネージド ID が表示されない場合は、ファクトリを更新して[マネージド ID を生成](#generate-managed-identity)してください。

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Azure portal を使用してシステム割り当てマネージド ID を取得する

Azure portal -> ご自分のデータ ファクトリ -> [プロパティ] でマネージド ID 情報を検索できます。

- マネージド ID オブジェクト ID
- マネージド ID のテナント

マネージ ID 情報は、Azure Blob、Azure Data Lake Storage、Azure Key Vault などのマネージド ID 認証をサポートする、リンクされたサービスを作成するときにも表示されます。

アクセス許可を付与する場合は、Azure リソースの [アクセス制御 (IAM)] タブ -> [ロールの割り当ての追加] -> [アクセスの割り当て先] -> [システム割り当てマネージド ID] の [データ ファクトリ] を選択 -> ファクトリ名で選択するか、または、一般に、オブジェクト ID またはデータ ファクトリ名 (マネージド ID 名) を使用して、この ID を検索することができます。 マネージ ID のアプリケーション ID を取得する必要がある場合は、PowerShell を使用できます。

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>PowerShell を使用してシステム割り当てマネージド ID を取得する

特定のデータ ファクトリを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。 **PrincipalId** を使用してアクセス権を付与します。

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

アプリケーション ID を取得するには、上のプリンシパル ID をコピーし、プリンシパル ID をパラメーターとして指定して次の Azure Active Directory コマンドを実行します。

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

#### <a name="retrieve-managed-identity-using-rest-api"></a>REST API を使用したマネージド ID の取得

特定のデータ ファクトリを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。

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

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

Azure Active Directory では、ユーザー割り当てマネージド ID の作成、削除、管理などを行うことができます。 詳細については、ドキュメント「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」をご覧ください。 

### <a name="credentials"></a>資格情報

ユーザー割り当てマネージド ID とサービス プリンシパルを含めることができる資格情報が導入されています。また、Azure Active Directory (AAD) 認証対応のリンクされたサービスで使用できるシステム割り当てマネージド ID も一覧表示されます。 これは、すべての AAD ベースの資格情報を統合して管理するのに役立ちます。  

次に示すのは、リンクされたサービスで認証に **ユーザー割り当てマネージド ID** を使用するための一般的な手順です。 

1. Azure portal、SDK、PowerShell、REST API を使用して、ユーザー割り当てマネージド ID をデータ ファクトリ インスタンスに関連付けます。 
   次のスクリーンショットでは、Azure portal (データ ファクトリ ブレード) を使用してユーザー割り当てマネージド ID を関連付けています。 

   :::image type="content" source="media/managed-identities/uami-azure-portal.jpg" alt-text="Azure portal を使用してユーザー割り当てマネージド ID を関連付ける方法を示すスクリーンショット。" lightbox="media/managed-identities/uami-azure-portal.jpg":::

2. データ ファクトリのユーザー インターフェイスで、対話形式で "資格情報" を作成します。 手順 1 でデータ ファクトリに関連付けたユーザー割り当てマネージド ID を選択できます。 

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-1.png" alt-text="新しい資格情報を作成する最初の手順を示すスクリーンショット。" lightbox="media/managed-identities/credential-adf-ui-create-new-1.png":::

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-2a.png" alt-text="新しい資格情報を作成する 2 番目の手順を示すスクリーンショット。" lightbox="media/managed-identities/credential-adf-ui-create-new-2a.png":::

3. リンクされたサービスを新しく作成し、認証で "ユーザー割り当てマネージド ID" を選択します

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-linked-service.png" alt-text="ユーザー割り当てマネージド ID 認証を使用した、新しいリンクされたサービスを示すスクリーンショット。" lightbox="media/managed-identities/credential-adf-ui-create-new-linked-service.png":::

> [!NOTE] 
> 上記のアクションには、SDK/PowerShell/REST API を使用できます。

## <a name="next-steps"></a>次のステップ

データ ファクトリのマネージド ID を使用する状況と方法を紹介する次のトピックをご覧ください。

- [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)
- [Azure リソース認証用のマネージド ID を使用して Azure Data Lake Store との間でデータを双方向にコピーする](connector-azure-data-lake-store.md)

データ ファクトリのマネージド ID の基になっている Azure リソースのマネージド ID の詳細については、[Azure リソースのマネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関する記事をご覧ください。
