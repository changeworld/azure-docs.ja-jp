---
title: Azure Service Fabric の一般的なセキュリティ属性
description: Azure Service Fabric を評価するための一般的なセキュリティ属性のチェックリスト
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: f6db40a35c289fa0870ac4c9e5e55b773c84f4f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679229"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Azure Service Fabric の一般的なセキュリティ属性

セキュリティは、Azure サービスのすべての要素に統合されています。 この記事では、Azure Service Fabric に組み込まれている共通のセキュリティ属性について説明します。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい |  |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | Service Fabric API は Azure Resource Manager によって呼び出されます。 有効な JSON Web トークン (JWT) が必要です。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| はい |  |
| ネットワークの分離 / ファイアウォールのサポート| はい | ネットワーク セキュリティ グループ (NSG) の使用。 |
| 強制トンネリングのサポート | はい | Azure ネットワークでは、強制トンネリングを提供します。 |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure 監視サポートとサード パーティ サポートの使用。 |

## <a name="iam-support"></a>IAM サポート

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| アクセス管理 - 認証| はい | 認証は Azure Active Directory を介して行われます。 |
| アクセス管理 - 承認| はい | SFRP 経由の呼び出しのための ID およびアクセス管理 (IAM)。 クラスター エンド ポイントは、ユーザーと管理者という 2 つのロールで直接呼び出すことができます。ユーザーは API をいずれかのロールにマッピングできます。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | コントロール プレーン操作はすべて、監査と承認のプロセスを経由して実行されます。 |
| データ プレーンのログ記録と監査| 該当なし | ユーザーはクラスターを所有します。  |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | サービス構成は Azure Deploy によってバージョン管理され、デプロイされます。 コード (アプリケーションとランタイム) は Azure Build によってバージョン管理されます。