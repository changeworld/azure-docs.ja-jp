---
title: 個人情報の名前付きエンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: c50beef5c9c5dcae7edd487e8bf3d192ba557865
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662721"
---
> [!NOTE]
> 保護されている医療情報 (PHI) を検出するには、`domain=phi` パラメーターとモデル バージョン `2020-04-01` 以降を使用します。
>
> 例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
`/v3.1-preview.1/entities/recognition/pii` エンドポイントに要求を送信すると、次のエンティティ カテゴリが返されます。

| カテゴリ   | サブカテゴリ | 説明                          | 開始モデル バージョン | Notes |
|------------|-------------|--------------------------------------|------------------------|---|
| Person     | 該当なし         | 人の名前。  | `2019-10-01`  | `domain=phi` でも返されます。 |
| PersonType | 該当なし         | 人物の職種または職務。 | `2020-02-01` | |
| PhoneNumber | 該当なし | 電話番号 (米国および EU の電話番号のみ)。 | `2019-10-01` | `domain=phi` でも返されます。 |
|Organization  | 該当なし | 企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関。  | `2019-10-01` | 国籍と宗教は、このエンティティ型には含まれていません。  |
|Organization | 医療 | 医療関係の企業および団体。 | `2020-04-01` |  |
|Organization | 証券取引 | 証券取引団体。 | `2020-04-01` |  |
| Organization | スポーツ | スポーツ関連の組織。 | `2020-04-01` |  |
| Address | 該当なし | 完全な郵送先住所。  | `2020-04-01` | `domain=phi` でも返されます。 |
| EU の GPS 座標 | 該当なし | 欧州連合内の場所の GPS 座標。  | `2019-10-01` |  |
| Email | 該当なし | メール アドレス。 | `2019-10-01` | `domain=phi` でも返されます。   |
| URL | 該当なし | Web サイトへの URL。 | `2019-10-01` | `domain=phi` でも返されます。 |
| IP | 該当なし | ネットワーク IP アドレス。 | `2019-10-01` | `domain=phi` でも返されます。 |
| DateTime | 該当なし | 日付と時刻。 | `2019-10-01` |  | 
| DateTime | Date | カレンダーの日付。 | `2019-10-01` | `domain=phi` でも返されます。 |
| Quantity | 該当なし | 数値と数量です。 | `2019-10-01` |  |
| Quantity | Age | 年齢。 | `2019-10-01` | | |

## <a name="azure-information"></a>Azure の情報

このエンティティ カテゴリには、認証情報や接続文字列などの特定可能な Azure 情報が含まれます。 モデル バージョン `2019-10-01` 以降で使用できます。 `domain=phi` パラメーターで返されません。

| サブカテゴリ                           | 説明                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 認証キー             | Azure Cosmos DB サーバーの認証キー。                           |
| Azure IAAS データベース接続文字列および Azure SQL 接続文字列 | Azure IaaS (サービスとしてのインフラストラクチャ) データベースの接続文字列と SQL 接続文字列。 |
| Azure SQL 接続文字列           | Azure SQL Database 内のデータベースの接続文字列。                                |
| Azure IoT 接続文字列           | Azure IoT の接続文字列。                        |
| Azure 発行設定パスワード        | Azure 発行設定のパスワード。                                        |
| Azure Redis Cache 接続文字列   | Redis Cache の接続文字列。                             |
| Azure SAS                             | Azure SaaS (サービスとしてのソフトウェア) の接続文字列。                     |
| Azure Service Bus 接続文字列   | Azure Service Bus の接続文字列。                                 |
| Azure Storage アカウント キー             | Azure Storage アカウントのアカウント キー。                                   |
| Azure Storage アカウント キー (汎用)   | Azure Storage アカウントの汎用アカウント キー。                           |
| SQL Server 接続文字列          | SQL Server を実行するコンピューターの接続文字列。                                         |

## <a name="identification"></a>識別

[!INCLUDE [supported identification entities](./identification-entities.md)]
