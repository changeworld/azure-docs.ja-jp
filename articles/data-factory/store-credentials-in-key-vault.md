---
title: "Azure Key Vault への資格情報の格納 | Microsoft Docs"
description: "Azure Data Factory で実行時に自動的に取得できる、Azure Key Vault で使用されたデータ ストアの資格情報を格納する方法を説明します。"
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault への資格情報の格納

[Azure Key Vault](../key-vault/key-vault-whatis.md) 内のデータ ストアに資格情報を格納することができます。 Azure Data Factory は、データ ストアを使用するアクティビティの実行時に、資格情報を取得します。

> [!NOTE]
> 現時点では、[Dynamics コネクタ](connector-dynamics-crm-office-365.md)でのみこの機能がサポートされます。 

## <a name="steps"></a>手順

データ ファクトリを作成する際に、ファクトリの作成と共にサービス ID が作成されます。 サービス ID は、Azure Activity Directory に登録されているマネージ アプリケーションであり、この特定のデータ ファクトリを表します。 [Azure Portal] -> 自分のデータ ファクトリ -> [プロパティ] でサービス ID 情報を検索できます。 

- サービス ID
- サービス ID のテナント
- サービス ID アプリケーション ID

Azure Key Vault に格納されている資格情報を参照するには、次の手順に従う必要があります。

1. データ ファクトリと共に生成された "サービス ID アプリケーション ID" をコピーします。
2. サービス ID に、Azure Key Vault へのアクセス権を付与します。 キー コンテナー -> [アクセス制御]-> [追加] で、このサービス ID アプリケーション ID を検索して閲覧者のアクセス許可を追加します。 この指定されたファクトリで、キー コンテナー内のシークレットにアクセスできます。
3. Azure Key Vault をポイントするリンクされたサービスを作成します。 「[Azure Key Vault のリンクされたサービス](#azure-key-vault-linked-service)」をご覧ください。
4. データ ストアのリンクされたサービスを作成します。その内部で、キー コンテナーに格納されている対応するシークレットを参照します。 「[キー コンテナーに格納された資格情報の参照](#reference-credential-stored-in-key-vault)」をご覧ください。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault のリンクされたサービス

Azure Key Vault のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureKeyVault** に設定する必要があります。 | あり |
| baseUrl | Azure Key Vault の URL (DNS 名) を指定します。 | あり |

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

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | フィールドの type プロパティは **AzureKeyVaultSecret** に設定する必要があります。 | あり |
| secretName | Azure Key Vault のシークレットの名前。 | あり |
| secretVersion | Azure Key Vault のシークレットのバージョン。<br/>指定しない場合は、常に最新バージョンのシークレットが使用されます。<br/>指定した場合は、その特定のバージョンに固定されます。| いいえ |
| store | 資格情報の格納に使用する Azure Key Vault のリンクされたサービスを表します。 | あり |

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
