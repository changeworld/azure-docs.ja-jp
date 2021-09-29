---
title: Dynamics でデータをコピーする (Microsoft Dataverse)
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics パイプラインで Copy アクティビティを使用して、Microsoft Dynamics CRM または Microsoft Dynamics 365 (Microsoft Dataverse) からサポートされているシンク データ ストアに、あるいはサポートされているソース データ ストアから Dynamics CRM または Dynamics 365 にデータをコピーする方法について説明します。
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.author: jianleishen
author: jianleishen
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6f95e117865ccf9d242d595ec98b66d7cd344a85
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597684"
---
# <a name="copy-data-from-and-to-dynamics-365-microsoft-dataverse-or-dynamics-crm"></a>Dynamics 365 (Microsoft Dataverse) または Dynamics CRM との間でデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
この記事では、Azure Data Factory または Synapse パイプラインで Copy アクティビティを使用して、Microsoft Dynamics 365 および Microsoft Dynamics CRM との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

このコネクタは、次のアクティビティでサポートされます。

- [サポートされるソースとシンクのマトリックス](copy-activity-overview.md)に従う[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Dynamics 365 (Microsoft Dataverse) または Dynamics CRM から、サポート対象の任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のソース データ ストアから Dynamics 365 (Microsoft Dataverse) または Dynamics CRM にデータをコピーすることもできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表を参照してください。

>[!NOTE]
>2020 年 11 月に Common Data Service は [Microsoft Dataverse](/powerapps/maker/data-platform/data-platform-intro) に名前が変更されました。 この記事は、最新の用語を反映して更新されています。 

この Dynamics コネクタでは、オンラインとオンプレミスの両方で Dynamics バージョン 7 から 9 がサポートされます。 具体的には次のとおりです。
- バージョン 7 は、Dynamics CRM 2015 にマップされます。
- バージョン 8 は、Dynamics CRM 2016 および初期バージョンの Dynamics 365 にマップされます。
- バージョン 9 は、以降のバージョンの Dynamics 365 にマップされます。


Dynamics のバージョンおよび製品でサポートされている認証の種類と構成については、次の表を参照してください。

| Dynamics のバージョン | 認証の種類 | リンクされたサービスの例 |
|:--- |:--- |:--- |
| Dataverse <br/><br/> Dynamics 365 Online <br/><br/> Dynamics CRM Online | Azure Active Directory (Azure AD) サービス プリンシパル <br/><br/> Office 365 | [オンライン Dynamics と Azure AD サービス プリンシパルまたは Office 365 認証](#dynamics-365-and-dynamics-crm-online) |
| インターネットに接続する展開 (IFD) に対応したオンプレミス Dynamics 365 <br/><br/> IFD 対応オンプレミス Dynamics CRM 2016 <br/><br/> IFD 対応オンプレミス Dynamics CRM 2015 | IFD | [IFD と IFD 認証に対応したオンプレミス Dynamics](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

>[!NOTE]
>[リージョンの探索サービスが非推奨](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)になったため、Office 365 認証の使用時に[グローバル探索サービス](/powerapps/developer/data-platform/webapi/discover-url-organization-web-api#global-discovery-service)を利用するようにサービスがアップグレードされました。

> [!IMPORTANT]
>テナントとユーザーが Azure Active Directory で[条件付きアクセス](../active-directory/conditional-access/overview.md)に関して構成されている場合、または多要素認証が必要な場合、およびその両方の場合は、Office 365 認証の種類を使用できません。 このような状況では、Azure Active Directory (Azure AD) サービス プリンシパル認証を使用する必要があります。

具体的には、Dynamics 365 では、次のアプリケーションの種類がサポートされます。
- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing このコネクタでは、Finance、Operations、Talent など、その他のアプリケーションの種類はサポートされていません。

>[!TIP]
>Dynamics 365 Finance and Operations からデータをコピーするには、[Dynamics AX コネクタ](connector-dynamics-ax.md)を使用できます。

この Dynamics コネクタは [Dynamics XRM ツール](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)に基づいて構築されています。

## <a name="prerequisites"></a>前提条件
このコネクタを Azure AD サービス プリンシパル認証で使用するには、Dataverse または Dynamics でサーバー間 (S2S) 認証を設定する必要があります。 最初に、アプリケーション ユーザー (サービス プリンシパル) を Azure Active Directory に登録します。 これを行う方法については、[こちら](../active-directory/develop/howto-create-service-principal-portal.md)を参照してください。 アプリケーションの登録時に、Dataverse または Dynamics でそのユーザーを作成し、アクセス許可を付与する必要があります。 これらのアクセス許可は直接付与することも、Dataverse または Dynamics でアクセス許可が付与されているチームにアプリケーション ユーザーを追加して間接的に付与することもできます。 Dataverse で認証するアプリケーション ユーザーを設定する方法の詳細については、[こちら](/powerapps/developer/data-platform/use-single-tenant-server-server-authentication)を参照してください。 


## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-dynamics-365-using-ui"></a>UI を使用して Dynamics 365 のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Dynamics 365 のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Dynamics を検索し、Dynamics 365 コネクタを選択します。

    :::image type="content" source="media/connector-dynamics-crm-office-365/dynamics-crm-office-365-connector.png" alt-text="Dynamics 365 コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-dynamics-crm-office-365/configure-dynamics-crm-office-365-linked-service.png" alt-text="Dynamics 365 のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Dynamics に固有のエンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Dynamics のリンクされたサービスでは、次のプロパティがサポートされます。

### <a name="dynamics-365-and-dynamics-crm-online"></a>オンラインの Dynamics 365 および Dynamics CRM

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、"Dynamics"、"DynamicsCrm"、"CommonDataServiceForApps" のいずれかに設定する必要があります。 | はい |
| deploymentType | Dynamics インスタンスの展開の種類。 オンライン Dynamics の場合、値を "Online" にする必要があります。 | はい |
| serviceUri | Dynamics インスタンスのサービス URL。ブラウザーからアクセスしたときに使用したものと同じ。 "https://\<organization-name>.crm[x].dynamics.com" などです。 | はい |
| authenticationType | Dynamics サーバーに接続する認証の種類。 有効な値は "AADServicePrincipal" と "Office365" です。 | はい |
| servicePrincipalId | Azure AD アプリケーションのクライアント ID。 | はい (認証が "AADServicePrincipal" の場合) |
| servicePrincipalCredentialType | サービス プリンシパル認証に使用する資格情報の種類。 有効な値は "ServicePrincipalKey" と "ServicePrincipalCert" です。 | はい (認証が "AADServicePrincipal" の場合) |
| servicePrincipalCredential | サービス プリンシパルの資格情報。 <br/><br/>資格情報の種類として "ServicePrincipalKey" を使用する場合、`servicePrincipalCredential` には、リンク サービスのデプロイ時にサービスによって暗号化される文字列を設定できます。 または、Azure Key Vault 内のシークレットへの参照を設定できます。 <br/><br/>資格情報として "ServicePrincipalCert" を使用する場合、`servicePrincipalCredential` は Azure Key Vault 内の証明書への参照である必要があります。 | はい (認証が "AADServicePrincipal" の場合) |
| username | Dynamics に接続するためのユーザー名。 | はい (認証が "Office365" の場合) |
| password | username として指定したユーザー アカウントのパスワード。 このフィールドを "SecureString" でマークして安全に保存するか、[Azure Key Vault に保存されているシークレットを参照します](store-credentials-in-key-vault.md)。 | はい (認証が "Office365" の場合) |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 値を指定しない場合、プロパティでは既定の Azure 統合ランタイムが使用されます。 | いいえ |

>[!NOTE]
>以前の Dynamics コネクタでは、省略可能な **organizationName** プロパティを使用してオンラインの Dynamics CRM または Dynamics 365 インスタンスを識別していました。 そのプロパティはまだ機能しますが、代わりに新しい **serviceUri** プロパティを指定して、インスタンス検出のパフォーマンスを向上させることをお勧めします。

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>例:Azure AD サービス プリンシパルとキー認証を使用したオンライン Dynamics

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>例:Azure AD サービス プリンシパルと証明書認証を使用したオンライン Dynamics

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```
#### <a name="example-dynamics-online-using-office-365-authentication"></a>例:Office 365 認証を使用したオンライン Dynamics

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>IFD 対応 Dynamics 365 および Dynamics CRM (オンプレミス)

オンライン Dyanmics と比べた場合に追加されたプロパティは、**hostName** と **port** です。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、"Dynamics"、"DynamicsCrm"、"CommonDataServiceForApps" のいずれかに設定する必要があります。 | はい。 |
| deploymentType | Dynamics インスタンスの展開の種類。 IFD 対応オンプレミス Dynamics の場合、値を "OnPremisesWithIfd" にする必要があります。| はい。 |
| hostName | オンプレミス Dynamics サーバーのホスト名。 | はい。 |
| port | オンプレミス Dynamics サーバーのポート。 | いいえ。 既定値は 443 です。 |
| organizationName | Dynamics インスタンスの組織の名前。 | はい。 |
| authenticationType | Dynamics サーバーに接続する認証の種類。 IFD 対応オンプレミス Dynamics の場合、"Ifd" を指定します。 | はい。 |
| username | Dynamics に接続するためのユーザー名。 | はい。 |
| password | username に指定したユーザー アカウントのパスワード。 このフィールドを "SecureString" でマークすることで安全に保存できます。 または、Key Vault にパスワードを格納して、データ コピーの実行時にコピー アクティビティがそこからプルするようにできます。 詳しくは、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」をご覧ください。 | はい。 |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 値を指定しない場合、プロパティでは既定の Azure 統合ランタイムが使用されます。 | いいえ |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>例:IFD 認証を使用する IFD 対応オンプレミス Dynamics

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Dynamics データセット でサポートされるプロパティの一覧を示します。

Dynamics との間でのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、"DynamicsEntity"、"DynamicsCrmEntity"、"CommonDataServiceForAppsEntity" のいずれかに設定する必要があります。 |はい |
| entityName | 取得するエンティティの論理名。 | ソースの場合は「いいえ」 (アクティビティ ソースが "query" として指定されている場合)、シンクの場合は「はい」 |

#### <a name="example"></a>例

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Dynamics ソースとシンクの種類でサポートされるプロパティの一覧を示します。

### <a name="dynamics-as-a-source-type"></a>ソースの種類としての Dynamics

Dynamics からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、"DynamicsSource"、"DynamicsCrmSource"、"CommonDataServiceForAppsSource" のいずれかに設定する必要があります。 | はい |
| query | FetchXML は、オンラインおよびオンプレミスの Dynamics で使用される独自のクエリ言語です。 次の例を参照してください。 詳細については、「[FetchXML を使用したクエリの構築](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8))」を参照してください。 | いいえ (データセットの `entityName` が指定されている場合) |

>[!NOTE]
>PK 列は、FetchXML クエリで構成する列のプロジェクションに含まれていない場合でも常にコピーされます。

> [!IMPORTANT]
>- Dynamics からデータをコピーするとき、Dynamics からシンクへの明示的な列マッピングは任意です。 ただし、決定的なコピー結果を保証するために、マッピングを強くお勧めします。
>- サービスでは、作成 UI でスキーマをインポートするときに、スキーマを推測します。 これは、ソース列リストを初期化するために、Dynamics のクエリ結果から上位の行をサンプリングすることによって行われます。 その場合、上位の行に値がない列は省略されます。 明示的なマッピングがない場合でも、コピーの実行には同じ動作が適用されます。 マッピングには、自分で確認したうえで列を追加することができます。コピーの実行時には、それが反映されます。

#### <a name="example"></a>例

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>FetchXML クエリの例

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>シンクの種類としての Dynamics

Dynamics にデータをコピーするために、コピー アクティビティの **sink** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは "DynamicsSink"、"DynamicsCrmSink"、"CommonDataServiceForAppsSink" のいずれかに設定する必要があります。 | はい。 |
| writeBehavior | 操作の書き込み動作。 この値は "Upsert" にする必要があります。 | はい |
| alternateKeyName | upsert を実行するためにエンティティに定義されている代替キー名。 | いいえ。 |
| writeBatchSize | 各バッチで Dynamics に書き込まれたデータの行数。 | いいえ。 既定値は 10 です。 |
| ignoreNullValues | 書き込み操作時に、キー フィールド以外の入力データからの null 値を無視するかどうか。<br/><br/>有効な値は **TRUE** と **FALSE** です。<ul><li>**TRUE**: upsert または更新操作を行うときに、対象オブジェクト内のデータが変更されないようにします。 挿入操作を実行するときに、定義済みの既定値を挿入します。</li><li>**FALSE**: upsert または更新操作を行うときに、対象オブジェクト内のデータを null 値に更新します。 挿入操作を実行するときに、null 値を挿入します。</li></ul> | いいえ。 既定値は **FALSE** です。 |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

>[!NOTE]
>Dynamics シンクでのシンク **writeBatchSize** とコピー アクティビティ **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** のどちらでも、既定値は 10 です。 そのため、既定で 100 個のレコードが同時に Dynamics に送信されます。

Dynamics 365 オンラインでは、[1 組織あたりの同時バッチ呼び出し数が 2](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations) という制限があります。 この制限を超えた場合、最初の要求が実行される前に "サーバー ビジー" 例外がスローされます。 このような同時呼び出しの帯域幅調整を避けるには、**writeBatchSize** を 10 以下に保ちます。

**writeBatchSize** と **parallelCopies** の最適な組み合わせは、エンティティのスキーマによって異なります。 スキーマの要素には、列の数や行のサイズに加えて、これらの呼び出しにフックされるプラグイン、ワークフロー、またはワークフロー アクティビティの数が含まれます。 **writeBatchSize** (10) &times; **parallelCopies** (10) の既定の設定は、Dynamics サービスによる推奨設定です。 この値は、ほとんどの Dynamics エンティティに対して機能しますが、最高のパフォーマンスをもたらすとは限りません。 コピー アクティビティ設定の組み合わせを調整することで、パフォーマンスを調整できます。

#### <a name="example"></a>例

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="retrieving-data-from-views"></a>ビューからデータを取得する

Dynamics ビューからデータを取得するには、ビューの保存されているクエリを取得し、そのクエリを使用してデータを取得する必要があります。

異なる種類のビューを格納する 2 つのエンティティがあります。"保存されたクエリ" はシステム ビューを格納し、"ユーザー クエリ" はユーザー ビューを格納します。 ビューの情報を取得するには、次の FetchXML クエリを参照し、"TARGETENTITY" を `savedquery` または `userquery` に置き換えます。 各エンティティ型には、必要に応じてクエリに追加できる、より多くの属性が用意されています。 [savedquery エンティティ](/dynamics365/customer-engagement/web-api/savedquery)と [userquery エンティティ](/dynamics365/customer-engagement/web-api/userquery)に関する詳細を確認してください。

```xml
<fetch top="5000" >
  <entity name="<TARGETENTITY>">
    <attribute name="name" />
    <attribute name="fetchxml" />
    <attribute name="returnedtypecode" />
    <attribute name="querytype" />
  </entity>
</fetch>
```

また、フィルターを追加してビューをフィルター処理することもできます。 たとえば、アカウント エンティティの "My Active Accounts" という名前のビューを取得するには、次のフィルターを追加します。

```xml
<filter type="and" >
    <condition attribute="returnedtypecode" operator="eq" value="1" />
    <condition attribute="name" operator="eq" value="My Active Accounts" />
</filter>
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics のデータ型のマッピング

次の表は、Dynamics からデータをコピーするときの、Dynamics のデータ型からサービスの中間データ型へのマッピングを示しています。 コピー アクティビティがソース スキーマに、またデータ型がシンクにマップするしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関するページを参照してください。

次のマッピング表を使用して、ソースの Dynamics のデータ型に基づいて、対応する中間データ型をデータセット構造に構成します。

| Dynamics データ型 | サービスの中間データ型 | ソースとしてサポート | シンクとしてサポート |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓ ([ガイダンス](#writing-data-to-a-lookup-field)を参照) |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ ([ガイダンス](#writing-data-to-a-lookup-field)を参照) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓ ([ガイダンス](#writing-data-to-a-lookup-field)を参照) |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics データ型の **AttributeType.CalendarRules**、**AttributeType.MultiSelectPicklist**、**AttributeType.PartyList** はサポートされていません。

## <a name="writing-data-to-a-lookup-field"></a>ルックアップ フィールドへのデータの書き込み

Customer や Owner のように、複数のターゲットがあるルックアップ フィールドにデータを書き込むには、次のガイダンスと例に従ってください。

1. フィールド値と対応するターゲット エンティティ名の両方がソースに含まれるようにします。
   - すべてのレコードが同じターゲット エンティティにマップされる場合は、次のいずれかの条件を確認してください。
      - ターゲット エンティティ名を格納する列がソース データにあること。
      - コピー アクティビティのソースで、ターゲット エンティティを定義するための列を追加済みであること。
   - 異なるレコードが異なるターゲット エンティティにマップされる場合は、対応するターゲット エンティティ名を格納する列がソース データにあることを確認してください。

1. 値とエンティティ参照の両方の列をソースからシンクにマップします。 エンティティ参照列は、特別な命名パターン `{lookup_field_name}@EntityReference` を持つ仮想列にマップする必要があります。 この列は、実際には Dynamics に存在しません。 この列が、指定されたマルチターゲット ルックアップ フィールドのメタデータ列であることを示すために使用されます。

たとえば、ソースに次の 2 つの列があるとします。

- **GUID** 型の **CustomerField** 列。これは、Dynamics のターゲット エンティティの主キー値です。
- **String** 型の **Target** 列。これは、ターゲット エンティティの論理名です。

また、そのようなデータを、シンク Dynamics のエンティティ フィールド **CustomerField** (**Customer** 型) にコピーするとします。

コピー アクティビティの列マッピングで、次のように 2 つの列をマップします。

- **CustomerField** を **CustomerField** に。 このマッピングは通常のフィールド マッピングです。
- **Target** を **CustomerField\@EntityReference** に。 シンク列は、エンティティ参照を表す仮想列です。 そのようなフィールド名は、スキーマをインポートしても現れないため、マッピングに入力します。

:::image type="content" source="./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png" alt-text="Dynamics のルックアップ フィールド: 列マッピング":::

すべてのソース レコードが同じターゲット エンティティにマップされ、ソース データにターゲット エンティティ名が含まれていない場合、簡単な方法は次のとおりです。コピー アクティビティのソースで、列を追加します。 パターン `{lookup_field_name}@EntityReference` を使用して新しい列に名前を付け、値をターゲット エンティティ名に設定してから、通常どおりの列マッピングに進みます。 ソースとシンクで列名が同一の場合、コピー アクティビティでは既定で名前によって列がマップされるため、明示的な列マッピングをスキップすることもできます。

:::image type="content" source="./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png" alt-text="Dynamics のルックアップ フィールド: エンティティ参照列の追加":::

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Copy アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
