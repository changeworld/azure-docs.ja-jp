---
title: Microsoft Sentinel ウォッチリストのテンプレートのスキーマ | Microsoft Docs
description: Microsoft Sentinel の各組み込みウォッチリストのテンプレート内で使用されるスキーマについて説明します。
author: batamig
ms.author: bagold
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 64c4ece5e98bbdb085a5512451422988f103a3f4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711137"
---
# <a name="microsoft-sentinel-built-in-watchlist-template-schemas-public-preview"></a>Microsoft Sentinel 組み込みウォッチリストのテンプレートのスキーマ (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel が提供する各組み込みウォッチリストのテンプレート内で使用されるスキーマについて詳しく説明します。 詳細については、[テンプレートを使用した新しいウォッチリストの作成](watchlists.md#create-a-new-watchlist-using-a-template-public-preview)に関するページをご覧ください。

> [!IMPORTANT]
> Microsoft Sentinel ウォッチリストのテンプレートは、現在プレビューの段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>


## <a name="high-value-assets"></a>価値の高い資産

価値の高い資産ウォッチリストには、組織内で重要な価値を持つデバイス、リソースなどの資産が一覧表示され、次のフィールドが含まれています。

| フィールド名 | Format                              | 例                                                                                                                                | 必須/省略可能 |
| ---------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **資産の種類** | String                              | `Device`, `Azure resource`, `AWS resource`, `URL`, `SPO`, `File share`, `Other`                                                                      | Mandatory          |
| **資産 ID**   | 文字列。資産の種類によって異なります | `/subscriptions/d1d8779d-38d7-4f06-91db-9cbc8de0176f/resourceGroups/SOC-Purview/providers/Microsoft.Storage/storageAccounts/purviewadls` | Mandatory          |
| **資産名** | String                              | `Microsoft.Storage/storageAccounts/purviewadls`                                                                                          | 省略可能           |
| **資産の完全修飾ドメイン名** | FQDN                                | `Finance-SRv.local.microsoft.com`                                                                                                        | Mandatory          |
| **IP アドレス** | IP                                  | `1.1.1.1`                                                                                                                                | 省略可能           |
| **タグ**       | List                                | `["SAW user","Blue Ocean team"] `                                                                                                        | 省略可能           |
| | | | |

## <a name="vip-users"></a>VIP ユーザー

VIP ユーザー ウォッチリストには、組織内で大きな影響を与える従業員のユーザー アカウントが一覧表示され、次の値が含まれています。

| フィールド名          | Format | 例                                             | 必須/省略可能 |
| ------------------- | ------ | --------------------------------------------------- | ------------------ |
| **ユーザー ID**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 省略可能           |
| **ユーザー AAD オブジェクト ID**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 省略可能           |
| **ユーザー オンプレミス SID**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 省略可能           |
| **ユーザー プリンシパル名** | UPN    | `JeffL@seccxp.ninja`                                  | Mandatory          |
| **タグ**                | List   | `["SAW user","Blue Ocean team"]`                      | 省略可能           |
| | | | |

## <a name="network-mapping"></a>ネットワーク マッピング

ネットワーク マッピング ウォッチリストには、IP サブネットとそれぞれの組織コンテキストが一覧表示され、次のフィールドが含まれています。

| フィールド名 | Format       | 例                      | 必須/省略可能 |
| ---------- | ------------ | ---------------------------- | ------------------ |
| **IP サブネット**  | サブネット範囲 |` 198.51.100.0/24 - 198….../22` | Mandatory          |
| **範囲名** | String       | `DMZ`                          | 省略可能           |
| **タグ**       | List         | `["Example","Example"]`        | 省略可能           |
| | | | |

## <a name="terminated-employees"></a>退職した従業員

退職した従業員ウォッチリストには、退職した従業員または退職しようとしている従業員が一覧表示され、次のフィールドが含まれています。

| フィールド名          | Format                                                                          | 例                              | 必須/省略可能 |
| ------------------- | ------------------------------------------------------------------------------- | ------------------------------------ | ------------------ |
| **ユーザー ID**     | UID                                                                             | `52322ec8-6ebf-11eb-9439-0242ac130002` | 省略可能           |
| **ユーザー AAD オブジェクト ID**  | SID                                                                             | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e` | 省略可能           |
| **ユーザー オンプレミス SID**    | SID                                                                             | `S-1-12-1-4141952679-1282074057-123`   | 省略可能           |
| **ユーザー プリンシパル名** | UPN                                                                             | `JeffL@seccxp.ninja`                  | Mandatory          |
| **UserState**           | String <br><br>`Notified` または `Terminated` を使用することをお勧めします | `Terminated`                           | Mandatory          |
| **通知日**  | Timestamp - 日付                                                                 | `01.12.20`                             | 省略可能           |
| **退職日**    | Timestamp - 日付                                                                 | `01.01.21`                            | Mandatory          |
| **タグ**                | List                                                                            | `["SAW user","Amba Wolfs team"]`       | 省略可能           |
| | | | |


## <a name="identity-correlation"></a>ID 相関関係

ID 相関関係ウォッチリストには、同じユーザーに属する関連ユーザー アカウントが一覧表示され、次のフィールドが含まれます。

| フィールド名                       | Format  | 例                                             | 必須/省略可能 |
| -------------------------------- | ------- | --------------------------------------------------- | ------------------ |
| **ユーザー ID**                  | UID     | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 省略可能           |
| **ユーザー AAD オブジェクト ID**               | SID     | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 省略可能           |
| **ユーザー オンプレミス SID**                 | SID     | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 省略可能           |
| **ユーザー プリンシパル名**              | UPN     | `JeffL@seccxp.ninja`                                  | Mandatory          |
| **従業員 ID**                      | String  | `8234123`                                             | 省略可能           |
| **電子メール**                            | Email   | `JeffL@seccxp.ninja`                                  | 省略可能           |
| **関連付けられている特権アカウント ID** | UID/SID | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 省略可能           |
| **関連付けられている特権アカウント**    | UPN     | `Admin@seccxp.ninja`                                  | 省略可能           |
| **タグ**                             | List    | `["SAW user","Amba Wolfs team"]`                      | 省略可能           |
| | | | |

## <a name="service-accounts"></a>サービス アカウント

サービス アカウント ウォッチリストには、サービス アカウントとその所有者が一覧表示され、次のフィールドが含まれています。

| フィールド名                | Format | 例                                             | 必須/省略可能 |
| ------------------------- | ------ | --------------------------------------------------- | ------------------ |
| **サービス識別子**        | UID    | `1111-112123-12312312-123123123`                      | 省略可能           |
| **サービス AAD オブジェクト ID**     | SID    | `11123-123123-123123-123123`                          | 省略可能           |
| **サービスのオンプレミス SID**       | SID    | `S-1-12-1-3123123-123213123-12312312-2916039507`      | 省略可能           |
| **サービス プリンシパル名**    | UPN    | `myserviceprin@contoso.com`                           | Mandatory          |
| **所有者のユーザー ID**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 省略可能           |
| **所有者のユーザー AAD オブジェクト ID**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 省略可能           |
| **所有者のユーザー オンプレミス SID**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 省略可能           |
| **所有者のユーザー プリンシパル名** | UPN    | `JeffL@seccxp.ninja`                                  | Mandatory          |
| **タグ**                      | List   | `["Automation Account","GitHub Account"]`             | 省略可能           |
| | | | |

## <a name="next-steps"></a>次のステップ

詳細については、「[Microsoft Azure Sentinel ウォッチリストを使用する](watchlists.md)」を参照してください。
