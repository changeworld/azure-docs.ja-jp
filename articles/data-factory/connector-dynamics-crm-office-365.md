---
title: "Azure Data Factory を使用して Dynamics CRM および 365 との間でデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、Dynamics CRM および 365 からサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから Dynamics CRM および 365 にデータをコピーする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: c2de89ba3adaaa7d745731cff74269deecef03e2
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Azure Data Factory を使用して Dynamics 365/Dynamics CRM との間でデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Dynamics 365/Dynamics CRM との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[Copy Activity in V1 (V1 でのコピー アクティビティ)](v1/data-factory-data-movement-activities.md)」を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

Dynamics CRM/Dynamics 365 のデータをサポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを Dynamics CRM/Dynamics 365 にコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

具体的には、この Dynamics コネクタは、次の Dynamics のバージョンと認証の種類をサポートします。

| Dynamics のバージョン | 認証の種類 | リンクされたサービスの例 |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Office365 | [Dynamics Online + Office365 認証](#dynamics-365-and-dynamics-crm-online) |
| IFD 対応オンプレミス Dynamics 365 <br> IFD 対応オンプレミス Dynamics CRM 2016 <br> IFD 対応オンプレミス Dynamics CRM 2015 | IFD | [IFD + IFD 認証対応オンプレミス Dynamics](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD は Internet Facing Deployment の省略形です。*

> [!NOTE]
> Dynamics コネクタを使用するには、Azure Key Vault にパスワードを格納し、データ コピーの実行時に ADF コピー アクティビティがプルできるようにします。 [リンクされたサービス プロパティ](#linked-service-properties)セクションで構成する方法を参照してください。

## <a name="getting-started"></a>使用の開始

コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、Dynamics に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Dynamics のリンクされたサービスでは、次のプロパティがサポートされます。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 および Dynamics CRM Online

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Dynamics** に設定する必要があります。 | あり |
| deploymentType | Dynamics インスタンスの展開の種類。 Dynamics Online を **"Online"** にする必要があります。 | あり |
| organizationName | Dynamics インスタンスの組織の名前。 | いいえ、複数の Dynamics インスタンスがユーザーに関連付けられている場合に指定する必要があります。 |
| authenticationType | Dynamics サーバーに接続する認証の種類。 Dynamics Online を **"Office365"** に指定します。 | あり |
| username | Dynamics に接続するユーザー名を指定します。 | あり |
| パスワード | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 Azure Key Vault にパスワードを配置し、パスワードを "AzureKeyVaultSecret" として構成する必要があります。 詳しくは、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」をご覧ください。 | あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合はいいえ、シンクの場合ははい |

>[!IMPORTANT]
>Dynamics にデータをコピーするには、次の例に示すように、Dynamics の近くの場所を使用して明示的に [Azure IR を作成](create-azure-integration-runtime.md#create-azure-ir)し、リンクされたサービスで関連付けます。

**例: Office 365 の認証をを使用する Dynamics Online**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

*Dyanmics Online と対比する追加のプロパティは、"hostName" と "port" です。*

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Dynamics** に設定する必要があります。 | あり |
| deploymentType | Dynamics インスタンスの展開の種類。 IFD 対応オンプレミス Dynamics を **"OnPremisesWithIfd"** にする必要があります。| あり |
| **hostName** | オンプレミス Dynamics サーバーのホスト名。 | あり |
| **port** | オンプレミス Dynamics サーバーのポート。 | いいえ (既定値は 443) |
| organizationName | Dynamics インスタンスの組織の名前。 | あり |
| authenticationType | Dynamics サーバーに接続する認証の種類。 IFD 対応オンプレミス Dynamics を **"Ifd"** に指定します。 | あり |
| username | Dynamics に接続するユーザー名を指定します。 | あり |
| パスワード | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 Azure Key Vault にパスワードを配置し、パスワードを "AzureKeyVaultSecret" として構成する必要があります。 詳しくは、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」をご覧ください。 | あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合はいいえ、シンクの場合ははい |

>[!IMPORTANT]
>Dynamics にデータをコピーするには、次の例に示すように、Dynamics の近くの場所を使用して明示的に [Azure IR を作成](create-azure-integration-runtime.md#create-azure-ir)し、リンクされたサービスで関連付けます。

**例: IFD 認証を使用する IFD 対応オンプレミス Dynamics**

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
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Dynamics との間でデータをコピーするには、データセットの type プロパティを **DynamicsEntity** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**DynamicsEntity** に設定する必要があります。 |あり |
| entityName | 取得するエンティティの論理名。 | ソースの場合はいいえ (アクティビティ ソースの "query" が指定されている場合)、シンクの場合ははい |

> [!IMPORTANT]
>- **Dynamics からデータをコピーする場合、Dynamics データセットの "structure" セクションは必須**です。このセクションでは、コピーする Dynamics データの列名とデータ型を定義します。 詳細は、[データセット構造](concepts-datasets-linked-services.md#dataset-structure)と [Dynamics のデータ型マッピング](#data-type-mapping-for-dynamics)を参照してください。
>- **Dynamics にデータをコピーする場合、Dynamics データセットの "structure" セクションは省略可能**です。 コピー先の列は、ソース データ スキーマによって決定されます。 ソースがヘッダーのない CSV ファイルの場合、入力データセットで、CSV ファイルのフィールドに 1 つずつ順番にマップされる列名とデータ型を "structure" で指定します。

**例:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Dynamics ソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="dynamics-as-source"></a>ソースとしての Dynamics

Dynamics からデータをコピーするは、コピー アクティビティのソースの種類を **DynamicsSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **DynamicsSource** に設定する必要があります  | あり |
| クエリ  | FetchXML は、Microsoft Dynamics (オンラインおよびオンプレミス) で使用される独自のクエリ言語です。 詳細については、次の例と [FeachXML でクエリを作成する](https://msdn.microsoft.com/en-us/library/gg328332.aspx)を参照してください。 | いいえ (データセットの "entityName" が指定されている場合)  |

**例:**

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

### <a name="dynamics-as-sink"></a>シンクとしての Dynamics

Dynamics にデータをコピーするは、コピー アクティビティのシンクの種類を **DynamicsSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **DynamicsSink** に設定する必要があります  | あり |
| writeBehavior | 操作の書き込み動作。<br/>使用可能な値: **"Upsert"**。 | あり |
| writeBatchSize | 各バッチで Dynamics に書き込まれたデータの行数。 | いいえ (既定値は 10) |
| ignoreNullValues | 書き込み操作時に入力データ (キー フィールドを除く) からの null 値を無視するかどうかを示します。<br/>使用可能な値: **true** および **false**。<br>- true: アップサート/更新操作の実行時には対象オブジェクト内のデータを変更せず、挿入操作の実行時には定義済みの既定値を挿入します。<br/>- false: アップサート/更新操作の実行時には対象オブジェクト内のデータを NULL に更新し、挿入操作の実行時には NULL 値を挿入します。  | いいえ (既定値は false) |

>[!NOTE]
>Dynamics シンクのシンク writeBatchSize とコピー アクティビティ [parallelCopies](copy-activity-performance.md#parallel-copy) の既定値はどちらも 10 で、これは Dynamics に 100 個のレコードが同時に送信されることを意味します。

**例:**

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

## <a name="data-type-mapping-for-dynamics"></a>Dynamics のデータ型のマッピング

Dynamics からデータをコピーするとき、次の Dynamics のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

次のマッピング テーブルを使用して、ソースの Dynamics データ型に基づき、データセット構造に対応する ADF データ型を構成します。

| Dynamics データ型 | Data Factory の中間データ型 | ソースとしてサポート | シンクとしてサポート |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | Boolean | ✓ |  |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ |  |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ |  |
| AttributeType.Status | Int32 | ✓ |  |


> [!NOTE]
> Dynamics データ型の AttributeType.CalendarRules と AttributeType.PartyList はサポートされていません。

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。