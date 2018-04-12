---
title: Azure Database for MySQL のサポートされているバージョン
description: サポートされている Azure Database for MySQL バージョンについて説明します。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: 53c8d51ddf9b7465a99b8b0685d7f6ce177fc526
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>サポートされている Azure Database for MySQL サーバー バージョン
Azure Database for MySQL は、InnoDB エンジンを使用して [MySQL Community Edition](https://www.mysql.com/products/community/) から開発されました。  Azure Database for MySQL では現在、次のバージョンがサポートされています。

## <a name="mysql-version-5638"></a>MySQL バージョン 5.6.38
MySQL 5.6.38 の機能強化と修正については、MySQL の[ドキュメント](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html)を参照してください。

## <a name="mysql-version-5720"></a>MySQL バージョン 5.7.20
MySQL 5.7.20 の機能強化と修正については、MySQL の[ドキュメント](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.html)を参照してください。

> [!NOTE]
> サービスでは、接続をサーバー インスタンスにリダイレクトするためにゲートウェイが使用されます。 接続が確立すると、MySQL には、MySQL サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MySQL のバージョンが表示されます。 MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。 

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
このサービスでは、マイナー バージョンの更新プログラムの適用が自動管理されます。 メジャー バージョンのアップグレードには対応していません ( MySQL 5.6 から MySQL 5.7 へのアップグレードなど)。

## <a name="next-steps"></a>次の手順

**サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください
