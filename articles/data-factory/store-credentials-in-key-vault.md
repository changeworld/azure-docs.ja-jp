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
ms.date: 12/14/2017
ms.author: jingwang
ms.openlocfilehash: 145c2bc0556010389e78e523fde6fd4b9063f930
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault への資格情報の格納

[Azure Key Vault](../key-vault/key-vault-whatis.md) 内のデータ ストアに資格情報を格納することができます。 Azure Data Factory は、データ ストアを使用するアクティビティの実行時に、資格情報を取得します。

現時点では、[Dynamics コネクタ](connector-dynamics-crm-office-365.md)、[Salesforce コネクタ](connector-salesforce.md)、および新しく有効になったいくつかのコネクタで、この機能がサポートされます。 今後、さらに追加される予定です。 詳細については、各コネクタのトピックを確認してください。 この機能をサポートするシークレット フィールドでは、次のような注意事項が説明に表示されます。"*このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時にコピー アクティビティでそこからプルするかを選択できます。詳しくは、「Azure Key Vault への資格情報の格納」をご覧ください。*"

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

## <a name="prerequisites"></a>前提条件

この機能は、データ ファクトリのサービス ID に依存しています。 [データ ファクトリのサービス ID](data-factory-service-identity.md) からの使用方法と、データ ファクトリに関連付けられていることを確認する方法について説明します。

## <a name="steps"></a>手順

Azure Key Vault に格納されている資格情報を参照するには、次の手順に従う必要があります。

1. ファクトリと共に生成された "サービス ID アプリケーション ID" の値をコピーして、[データ ファクトリのサービス ID を取得](data-factory-service-identity.md#retrieve-service-identity)します。
2. サービス ID に、Azure Key Vault へのアクセス権を付与します。 キー コンテナー -> [アクセス制御]-> [追加] で、このサービス ID アプリケーション ID を検索して**閲覧者**以上のアクセス許可を追加します。 この指定されたファクトリで、キー コンテナー内のシークレットにアクセスできます。
3. Azure Key Vault をポイントするリンクされたサービスを作成します。 「[Azure Key Vault のリンクされたサービス](#azure-key-vault-linked-service)」をご覧ください。
4. データ ストアのリンクされたサービスを作成します。その内部で、キー コンテナーに格納されている対応するシークレットを参照します。 「[キー コンテナーに格納された資格情報の参照](#reference-credential-stored-in-key-vault)」をご覧ください。

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

## <a name="reference-credential-stored-in-key-vault"></a>キー コンテナーに格納された資格情報の参照

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