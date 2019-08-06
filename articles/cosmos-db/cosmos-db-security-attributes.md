---
title: Azure Cosmos DB のセキュリティ属性
description: Azure Cosmos DB を評価するためのセキュリティ属性のチェックリスト
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 50711f8675e1b8aca6b9f90925ea921d22020ddd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442551"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Azure Cosmos DB のセキュリティ属性

この記事では、Azure Cosmos DB に組み込まれているセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | すべての Cosmos DB データベースとバックアップは、既定で暗号化されます。「[Azure Cosmos DB でのデータの暗号化](database-encryption-at-rest.md)」を参照してください。 カスタマー マネージド キーによるサーバー側での暗号化はサポートされていません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Cosmos DB のすべてのデータは、転送中に暗号化されます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| はい | Tables API Premium でのみ。 この機能は、すべての API でサポートされるわけではありません。 「[Azure Cosmos DB の概要:Table API](table-introduction.md)」を参照してください。 |
| API 呼び出しの暗号化| はい | Azure Cosmos DB へのすべての接続で HTTPS がサポートされます。 Azure Cosmos DB では、TLS 1.2 接続もサポートされますが、これはまだ実施されていません。 お客様が自分の側で下位レベルの TLS をオフにすると、Cosmos DB に確実に接続できます。  |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| はい | VNet サービス エンドポイントを使用して、仮想ネットワーク (VNet) の特定のサブネットからのアクセスのみを許可するように Azure Cosmos DB アカウントを構成できます。 VNet へのアクセスとファイアウォール規則を組み合わせることもできます。  「[仮想ネットワークから Azure Cosmos DB にアクセスする](VNet-service-endpoint.md)」を参照してください。 |
| ネットワークの分離とファイアウォールのサポート| はい | ファイアウォールのサポートを使用して、承認された IP アドレスのセット、IP アドレスの範囲、およびクラウド サービスからのアクセスのみを許可するように Azure Cosmos アカウントを構成できます。 「[Azure Cosmos DB で IP ファイアウォールを構成する](how-to-configure-firewall.md)」を参照してください。|
| 強制トンネリングのサポート| はい | 仮想マシンが配置されている VNet のクライアント側で構成できます。   |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Cosmos DB に送信されたすべての要求が記録されます。 [Azure 監視](../azure-monitor/overview.md)、Azure メトリック、Azure 監査ログがサポートされています。  データ プレーン要求、クエリのランタイム統計、クエリ テキスト、MongoDB 要求に対応する情報を記録することができます。 アラートを設定することもできます。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | データベース アカウント レベルでは「はい」です。データ プレーン レベルでは、Cosmos DB では、リソース トークンとキー アクセスが使用されます。 |
| Authorization| はい | マスター キー (プライマリおよびセカンダリ) とリソース トークンを持つ Azure Cosmos アカウントでサポートされます。 マスター キーを使用して、データに対して、読み取り/書き込みアクセスまたは読み取り専用アクセスを実行できます。 リソース トークンを使用して、ドキュメントやコンテナーなどのリソースに、限られた時間だけアクセスできます。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | ファイアウォール、VNet、キー アクセス、IAM などのアカウント レベルの操作の Azure アクティビティ ログ。 |
| データ プレーンのログ記録と監査 | はい | コンテナーの作成、スループットのプロビジョニング、インデックス作成ポリシー、ドキュメントの CRUD 操作などのコンテナー レベルの診断監視のログ記録。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB の追加のセキュリティ属性

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| クロスオリジン リソース共有 (CORS) | はい | 「[クロスオリジン リソース共有 (CORS) の構成](how-to-configure-cross-origin-resource-sharing.md)」を参照してください。 |
