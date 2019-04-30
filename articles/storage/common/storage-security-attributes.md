---
title: Azure Storage の一般的なセキュリティ属性
description: Azure Storage を評価するための一般的なセキュリティ属性のチェックリスト
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 33765d57f1a0e5788011b2d9d9c2f57d06713ddb
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680691"
---
# <a name="common-security-attributes-for-azure-storage"></a>Azure Storage の一般的なセキュリティ属性

セキュリティは、Azure サービスのすべての要素に統合されています。 この記事では、Azure Storage に組み込まれている一般的なセキュリティ属性について説明します。 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい |  |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | 標準の HTTPS/TLS 機構をサポートします。  サービスに転送するデータは、ユーザーが事前に暗号化することもできます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | 「[Azure Key Vault で顧客が管理するキーを Storage Service Encryption に使用する](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい |  |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| 該当なし |  |
| ネットワークの分離/ファイアウォールのサポート| はい | |
| 強制トンネリングのサポート | 該当なし |  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Monitor のメトリックが利用できるようになり、ログのプレビューが開始されました |

## <a name="iam-support"></a>IAM サポート

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| アクセス管理 - 認証| はい | Azure Active Directory、共有キー、共有アクセス トークン。 |
| アクセス管理 - 承認| はい | RBAC、POSIX ACL、SAS トークンを使用した承認をサポートします |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査 | はい | Azure Resource Manager アクティビティ ログ |
| データ プレーンのログ記録と監査| はい | サービスの診断ログおよび Azure Monitor のログのプレビューが開始されました  |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | Azure Resource Manager API によりリソース プロバイダーのバージョン管理をサポートします |