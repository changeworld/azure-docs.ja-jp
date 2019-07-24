---
title: Azure Key Vault の一般的なセキュリティ属性
description: Azure Key Vault を評価するための一般的なセキュリティ属性のチェックリスト
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000193"
---
# <a name="security-attributes-for-azure-key-vault"></a>Azure Key Vault のセキュリティ属性

この記事では、Azure Key Vault に組み込まれているセキュリティ属性について説明します。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | すべてのオブジェクトが暗号化されます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | すべての通信が暗号化された API 呼び出しを介して行われます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーが Key Vault 内のすべてのキーを制御します。 ハードウェア セキュリティ モジュール (HSM) バックアップ キーが指定されている場合、FIPS レベル 2 HSM により、キー、証明書、またはシークレットが保護されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | HTTPS を使用します。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | Virtual Network (VNet) サービス エンドポイントを使用。 |
| VNet インジェクションのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| はい | VNet ファイアウォール規則を使用。 |
| 強制トンネリングのサポート| いいえ |  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics を使用します。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | Key Vault アクセス ポリシーを使用します。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | Log Analytics を使用します。 |
| データ プレーンのログ記録と監査| はい | Log Analytics を使用します。 |

## <a name="access-controls"></a>アクセス制御

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 制御/管理プレーンのアクセス制御 | はい | Azure Resource Manager のロールベースのアクセス制御 (RBAC) |
| データ プレーンのアクセス制御 (すべてのサービス レベル) | はい | Key Vault アクセス ポリシー |