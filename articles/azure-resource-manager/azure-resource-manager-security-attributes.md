---
title: Azure Resource Manager の一般的なセキュリティ属性
description: Azure Resource Manager を評価するための一般的なセキュリティ属性のチェックリスト
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002253"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Azure Resource Manager のセキュリティ属性

この記事では、Azure Resource Manager に組み込まれているセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい |  |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | HTTPS/TLS。 |
| 暗号化キーの処理 (CMK や BYOK など)| 該当なし | Azure Resource Manager では、制御データのみが格納され、ユーザーのコンテンツは格納されません。 |
| 列レベルの暗号化 (Azure Data Services)| はい | |
| API 呼び出しの暗号化| はい | |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| いいえ |  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| いいえ | |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | [Azure Active Directory](/azure/active-directory) ベース。|
| 承認| はい | |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | アクティビティ ログでは、リソースに対して実行された書き込み操作 (PUT、POST、DELETE) がすべて公開されます。「[リソースのアクションを監査するアクティビティ ログの表示](resource-group-audit.md)」を参照してください。 |
| データ プレーンのログ記録と監査| 該当なし | |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  |
