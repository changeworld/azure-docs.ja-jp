---
title: Azure SQL Database 用語集 | Microsoft Docs
description: Azure SQL Database 用語集
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/26/2019
ms.openlocfilehash: d940cfa91890d26a48b682a6eb09410b3b7de045
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568082"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database 用語集

|Context|期間|詳細情報|
|:---|:---|:---|
|Azure サービス名|Azure SQL Database または SQL Database|[Azure SQL Database サービス](sql-database-technical-overview.md)|
|コンピューティング レベル|サーバーレス (プレビュー)|[サーバーレス コンピューティング レベル](sql-database-serverless.md)
||プロビジョニング済み|[サーバーレス コンピューティング レベル](sql-database-serverless.md)
|デプロイ オプション |単一データベース|[単一データベース](sql-database-single-database.md)|
||エラスティック プール|[エラスティック プール](sql-database-elastic-pool.md)|
||マネージド インスタンス|[マネージド インスタンス](sql-database-managed-instance.md)|
|サーバー オブジェクト|SQL Database サーバーまたはデータベース サーバー|[データベース サーバー](sql-database-servers.md)|
||SQL Database マネージド インスタンス サーバー、マネージド インスタンス サーバー、またはインスタンス サーバー|[マネージド インスタンス](sql-database-managed-instance.md)|
データベース オブジェクト|Azure SQL データベース|Azure SQL Database 内の任意のデータベース|
||単一データベース|単一データベース デプロイ オプションを使用して作成されたデータベース|
||プールされたデータベース|エラスティック プール内で作成されたデータベース、またはエラスティック プールに移動されたデータベース|
||インスタンス データベース|マネージド インスタンス内で作成されたデータベース|
||Basic データベース|DTU ベースの購入モデルの Basic サービス レベル内で作成されたデータベース、または Basic サービス レベルに移動されたデータベース|
||Standard データベース|DTU ベースの購入モデルの Standard サービス レベル内で作成されたデータベース、または Standard サービス レベルに移動されたデータベース|
||Premium データベース|DTU ベースの購入モデルの Premium サービス レベル内で作成されたデータベース、または Premium サービス レベルに移動されたデータベース|
||General Purpose データベース|仮想コアベースの購入モデルの General Purpose サービス レベル内で作成されたデータベース、または General Purpose サービス レベルに移動されたデータベース|
||ハイパースケール データベース|仮想コアベースの購入モデルのハイパースケール サービス レベル内で作成されたデータベース、またはハイパースケール サービス レベルに移動されたデータベース|
||Business Critical データベース|仮想コアベースの購入モデルの Business Critical サービス レベル内で作成されたデータベース、または Business Critical サービス レベルに移動されたデータベース|
||プロビジョニングされたデータベース|プロビジョニングされたコンピューティング レベルで構成されているデータベース|
|[購入モデルとリソース](sql-database-purchase-models.md)|DTU ベースの購入モデル|[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)|
||仮想コアベースの購入モデル|[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)|
||仮想コア|ハイパーバイザーによってゲスト OS に提供されるコア。|
||サービス階層|購入モデル内でのサービスのレベル|
||コンピューティング サイズ|サービス レベル内での単一データベース、エラスティック プール、またはマネージド インスタンスに対するコンピューティング リソースの量|
||ストレージ容量|単一データベース、エラスティック プール、またはマネージド インスタンスに使用可能なストレージの量|
||コンピューティング世代|サービス レベル内でのプロセッサの世代|
|データベース サーバー IP ファイアウォール規則|IP ファイアウォール規則|[IP ファイアウォール規則](sql-database-firewall-configure.md)|
||サーバーレベルの IP ファイアウォール規則|[サーバーレベルの IP ファイアウォール規則](sql-database-firewall-configure.md#overview)|
|| データベース レベルの IP ファイアウォール規則|[データベース レベルの IP ファイアウォール規則](sql-database-firewall-configure.md#overview)|
||仮想ネットワーク エンドポイントと規則|[仮想ネットワーク エンドポイントと規則](sql-database-vnet-service-endpoint-rule-overview.md)|
