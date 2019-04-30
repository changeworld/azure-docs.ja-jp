---
title: Azure Backup の一般的なセキュリティ属性
description: Azure Backup を評価するための一般的なセキュリティ属性のチェックリスト
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679689"
---
# <a name="common-security-attributes-for-azure-backup"></a>Azure Backup の一般的なセキュリティ属性

セキュリティは、Azure サービスのすべての要素に統合されています。 この記事では、Azure Backup に組み込まれている共通のセキュリティ属性について説明します。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | ストレージ アカウントに対して Storage Service Encryption を使用します。 |
| 転送中の暗号化: <ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet-VNet 暗号化</ul>| いいえ  | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ  |  |
| 列レベルの暗号化 (Azure Data Services)| いいえ  |  |
| API 呼び出しの暗号化| はい |  |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ  |  |
| VNet インジェクションのサポート| いいえ  |  |
| ネットワークの分離/ファイアウォールのサポート| はい | VM バックアップでは、強制トンネリングがサポートされます。 VM 内で実行されるワークロードでは、強制トンネリングはサポートされません。 |
| 強制トンネリングのサポート | いいえ  |  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics は、診断ログを通じてサポートされます。 詳細については、[Log Analytics を使用した Azure Backup で保護されているワークロードの監視](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)に関するページを参照してください。 |

## <a name="iam-support"></a>IAM サポート

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| アクセス管理 - 認証| はい | 認証は Azure Active Directory を介して行われます。 |
| アクセス管理 - 承認| はい | カスタマーが作成した RBAC ロールとビルトインの RBAC ロールが使用されます。 詳細については、[ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](/azure/backup/backup-rbac-rs-vault)に関するページを参照してください。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | Azure Portal からカスタマーがトリガーしたすべてのアクションがアクティビティ ログに記録されます。 |
| データ プレーンのログ記録と監査| いいえ  | Azure Backup データ プレーンには、直接アクセスできません。  |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい|  |