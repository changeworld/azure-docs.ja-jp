---
title: Azure Data Factory サービス ID | Microsoft Docs
description: Azure Data Factory のデータ ファクトリ サービス ID について説明します。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: ba965b0610525f66c24dd6d5bad3fd113a6b6758
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050867"
---
# <a name="azure-data-factory-service-identity"></a>Azure Data Factory サービス ID

この記事は、データ ファクトリ　サービス ID についてとそのしくみを理解するために役立ちます。

## <a name="overview"></a>概要

データ ファクトリの作成時に、ファクトリの作成に加えてサービス ID を作成することができます。 サービス ID は、Azure Activity Directory に登録されているマネージ アプリケーションであり、この特定のデータ ファクトリを表します。

データ ファクトリ サービス ID は、次の 2 つの機能に役立ちます。

- [Azure Key Vault に資格情報を格納する](store-credentials-in-key-vault.md)。この場合、データ ファクトリ サービス ID は Azure Key Vault の認証に使われます。
- [Azure Data Lake Store 間でデータをコピーする](connector-azure-data-lake-store.md)。この場合、データ ファクトリ サービス ID は、サポートされる Data Lake Store 認証タイプのいずれかとして使用できます。

## <a name="generate-service-identity"></a>サービス ID の生成

データ ファクトリ サービス ID は、次のように生成されます。

- **Azure Portal または PowerShell** を介してデータ ファクトリを作成すると、サービス ID が常に自動的に作成されます。
- **SDK** を介してデータ ファクトリを作成する場合、作成用のファクトリ オブジェクトで "Identity = new FactoryIdentity()" を指定したときのみサービス ID が作成されます。 [.NET のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-dot-net.md#create-a-data-factory)の例をご覧ください。
- **REST API** を介してデータ ファクトリを作成する場合、要求本文で "Identity" セクションを指定したときのみサービス ID が作成されます。 [REST のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-rest-api.md#create-a-data-factory)の例をご覧ください。

「[サービス ID の取得](#retrieve-service-identity)」の手順に従ってデータ ファクトリにサービス ID がまだ関連付けられていない場合は、ID イニシエーターを使用してプログラムでデータ ファクトリを更新することで、サービス ID を明示的に生成できます。

- [PowerShell を使用したサービス ID の生成](#generate-service-identity-using-powershell)
- [REST API を使用したサービス ID の生成](#generate-service-identity-using-rest-api)
- [SDK を使用したサービス ID の生成](#generate-service-identity-using-sdk)

>[!NOTE]
>- サービス ID は変更できません。 サービス ID が既に存在するデータ ファクトリを更新しても影響はありません。サービス ID は変更なく保持されます。
>- ファクトリ オブジェクトで "identity" パラメーターを指定せずに、または REST 要求本文で "identity" セクションを指定せずに、サービス ID が既に存在するデータ ファクトリを更新した場合、エラーが発生します。
>- データ ファクトリを削除すると、それに従って、関連付けられているサービス ID が削除されます。

### <a name="generate-service-identity-using-powershell"></a>PowerShell を使用したサービス ID の生成

**Set-AzureRmDataFactoryV2** コマンドを再度呼び出すと、"Identity" フィールドが新たに生成されているのを確認できます。

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>REST API を使用したサービス ID の生成

要求本文の "identity" セクションを使用して、次の API を呼び出します。

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**要求本文**: "identity": { "type": "SystemAssigned" } を追加します。

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

**応答**: サービス ID が自動的に作成され、これに応じて "identity" セクションが設定されます。

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>SDK を使用したサービス ID の生成

Identity=new FactoryIdentity() を指定して、データ ファクトリの作成または更新関数を呼び出します。 .NET を使用したサンプル コードは以下のとおりです。

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>サービス ID の取得

Azure Portal から、またはプログラムによって、サービス ID を取得することができます。 以降のセクションでは、いくつかの例を示します。

>[!TIP]
> サービス ID が表示されない場合は、ファクトリを更新して[サービス ID を生成](#generate-service-identity)してください。

### <a name="retrieve-service-identity-using-azure-portal"></a>Azure Portal を使用したサービス ID の取得

[Azure Portal] -> 自分のデータ ファクトリ -> [設定] -> [プロパティ] で、サービス ID 情報を検索できます。

- サービス ID
- サービス ID のテナント
- **サービス ID アプリケーション ID** > この値をコピーします

![サービス ID の取得](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>PowerShell を使用したサービス ID の取得

特定のデータ ファクトリを取得すると、次のように、サービス ID のプリンシパル ID およびテナント ID が返されます。

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

**ApplicationId** を取得するには、プリンシパル ID をコピーし、プリンシパル ID をパラメーターとして指定して次の Azure Active Directory コマンドを実行します。ApplicationId は、アクセス権を付与するために使用します。

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>次の手順
データ ファクトリ サービス ID を使用するタイミングと方法を紹介する次のトピックを参照してください。

- [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)
- [管理対象のサービス ID 認証を使用した Azure Data Lake Store 間でのデータのコピーに関するページ](connector-azure-data-lake-store.md)

データ ファクトリ サービス ID の基になっている管理対象サービス ID の背景について詳しくは、[MSI の概要](~/articles/active-directory/msi-overview.md)に関するページをご覧ください。 