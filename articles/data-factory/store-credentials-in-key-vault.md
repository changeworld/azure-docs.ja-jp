---
title: "Azure Key Vault への資格情報の格納 | Microsoft Docs"
description: "Azure Data Factory で実行時に自動的に取得できる、Azure Key Vault で使用されたデータ ストアの資格情報を格納する方法を説明します。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 51318a6bb4f88759984531b005c65712b6817ec0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault への資格情報の格納

データ ストアや計算のための資格情報を [Azure Key Vault](../key-vault/key-vault-whatis.md) に格納することができます。 Azure Data Factory は、データ ストア/計算を使うアクティビティの実行時に、資格情報を取得します。

現時点では、カスタム アクティビティを除くすべてのアクティビティの種類が、この機能をサポートしています。 具体的なコネクタの構成について詳しくは、[各コネクタ トピック](copy-activity-overview.md#supported-data-stores-and-formats)の「リンクされたサービスのプロパティ」セクションをご覧ください。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

## <a name="prerequisites"></a>前提条件

この機能は、データ ファクトリのサービス ID に依存しています。 [データ ファクトリのサービス ID](data-factory-service-identity.md) からの使用方法と、データ ファクトリに関連付けられていることを確認する方法について説明します。

## <a name="steps"></a>手順

Azure Key Vault に格納されている資格情報を参照するには、次の手順に従う必要があります。

1. ファクトリと共に生成された "サービス ID アプリケーション ID" の値をコピーして、**[データ ファクトリのサービス ID を取得](data-factory-service-identity.md#retrieve-service-identity)**します。
2. **サービス ID に、Azure Key Vault へのアクセス権を付与します。** キー コンテナーで、[アクセス ポリシー] -> [新規追加] を選択し、このサービス ID アプリケーション ID を検索して、[シークレットのアクセス許可] ドロップダウンで **Get** アクセス許可を付与します。 この指定されたファクトリで、キー コンテナー内のシークレットにアクセスできます。
3. **Azure Key Vault をポイントするリンクされたサービスを作成します。** 「[Azure Key Vault のリンクされたサービス](#azure-key-vault-linked-service)」をご覧ください。
4. **データ ストアのリンクされたサービスを作成します。その内部で、キー コンテナーに格納されている対応するシークレットを参照します。** 「[キー コンテナーに格納されたシークレットの参照](#reference-secret-stored-in-key-vault)」をご覧ください。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault のリンクされたサービス

Azure Key Vault のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | type プロパティは **AzureKeyVault** に設定する必要があります。 | [はい] |
| baseUrl | Azure Key Vault の URL を指定します。 | [はい] |

**例:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>キー コンテナーに格納されたシークレットの参照

キー コンテナーのシークレットを参照するリンクされたサービスのフィールドを構成する場合は、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | フィールドの type プロパティは **AzureKeyVaultSecret** に設定する必要があります。 | [はい] |
| secretName | Azure Key Vault のシークレットの名前。 | [はい] |
| secretVersion | Azure Key Vault のシークレットのバージョン。<br/>指定しない場合は、常に最新バージョンのシークレットが使用されます。<br/>指定した場合は、その特定のバージョンに固定されます。| いいえ  |
| store | 資格情報の格納に使用する Azure Key Vault のリンクされたサービスを表します。 | [はい] |

**例: ("password" のセクションをご覧ください)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。