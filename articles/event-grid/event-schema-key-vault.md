---
title: Azure Key Vault 用の Azure Event Grid イベント スキーマ
description: Azure Event Grid で Azure Key Vault のイベント用に提供されているプロパティとスキーマについて説明します
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082867"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault 用の Azure Event Grid イベント スキーマ (プレビュー)

この記事では、現在プレビュー段階にある、[Azure Key Vault](../key-vault/index.yml) のイベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

## <a name="available-event-types"></a>使用可能なイベントの種類

Azure Key Vault アカウントでは、以下の種類のイベントが生成されます。

| イベントのフル ネーム | イベントの表示名 | 説明 |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Certificate New Version Created (証明書の新しいバージョンが作成されました) | 新しい証明書または証明書の新しいバージョンが作成されたときにトリガーされます。 |
| Microsoft.KeyVault.CertificateNearExpiry | Certificate Near Expiry (証明書の有効期限が近づいています) | 現在のバージョンの証明書が有効期限切れになろうとしているときにトリガーされます。 (既定値は有効期限の 30 日前です。) |
| Microsoft.KeyVault.CertificateExpired | 証明書の有効期限が切れた | 証明書の有効期限が切れたときにトリガーされます。 |
| Microsoft.KeyVault.KeyNewVersionCreated | New Version Created (新しいバージョンが作成されました) | 新しいキーまたはキーの新しいバージョンが作成されたときにトリガーされます。 |
| Microsoft.KeyVault.KeyNearExpiry | Key Near Expiry (キーの有効期限が近づいています) | 現在のバージョンのキーが有効期限切れになろうとしているときにトリガーされます。 (既定値は有効期限の 30 日前です。) |
| Microsoft.KeyVault.KeyExpired | Key Expired (キーの有効期限が切れました) | キーの有効期限が切れたときにトリガーされます。 |
| Microsoft.KeyVault.SecretNewVersionCreated | Secret New Version Created (シークレットの新しいバージョンが作成されました) | 新しいシークレットまたはシークレットの新しいバージョンが作成されたときにトリガーされます。 |
| Microsoft.KeyVault.SecretNearExpiry | Secret Near Expiry (シークレットの有効期限が近づいています) | 現在のバージョンのシークレットが有効期限切れになろうとしているときにトリガーされます。 (既定値は有効期限の 30 日前です。) |
| Microsoft.KeyVault.SecretExpired | Secret Expired (シークレットの有効期限が切れました) | シークレットの有効期限が切れたときにトリガーされます。 |

## <a name="event-examples"></a>イベントの例

次の例は、**Microsoft.KeyVault.SecretNewVersionCreated** のスキーマを示しています。

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | 種類 | 説明 |
| ---------- | ----------- |---|
| id | string | このイベントをトリガーしたオブジェクトの ID |
| vaultName | string | このイベントをトリガーしたオブジェクトのキー コンテナー名 |
| objectType | string | このイベントをトリガーしたオブジェクトの種類 |
| objectName | string | このイベントをトリガーしたオブジェクトの名前 |
| version | string | このイベントをトリガーしたオブジェクトのバージョン |
| nbf | number | このイベントをトリガーしたオブジェクトの 1970-01-01T00:00:00Z 以後の開始日 (秒単位) |
| exp | number | このイベントをトリガーしたオブジェクトの 1970-01-01T00:00:00Z 以後の有効期限 (秒単位) |


## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成方法の詳細については、「[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)」を参照してください。
* Key Vault の Event Grid との統合の詳細については、「[Azure Event Grid での Key Vault の監視 (プレビュー)](../key-vault/event-grid-overview.md)」を参照してください。
* Key Vault の Event Grid との統合に関するチュートリアルについては、「[Azure Event Grid でキー コンテナー通知を受信して応答する (プレビュー)](../key-vault/event-grid-tutorial.md)」を参照してください。
* Key Vault と Azure Automation に関する追加のガイダンスについては、以下を参照してください。
    - [Azure Key Vault とは](../key-vault/key-vault-overview.md)
    - [Azure Event Grid での Key Vault の監視 (プレビュー)](../key-vault/event-grid-overview.md)
    - [Azure Event Grid でキー コンテナー通知を受信して応答する (プレビュー)](../key-vault/event-grid-tutorial.md)
    - [Azure Automation の概要](../automation/index.yml)
