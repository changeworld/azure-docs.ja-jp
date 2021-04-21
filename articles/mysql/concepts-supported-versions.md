---
title: サポートされているバージョン - Azure Database for MySQL
description: Azure Database for MySQL サービスでサポートされている MySQL サーバーのバージョンについて説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1be15c16a1897797326ea869c34c3590ffb07691
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363871"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>サポートされている Azure Database for MySQL サーバー バージョン

Azure Database for MySQL は、InnoDB ストレージ エンジンを使用して [MySQL Community Edition](https://www.mysql.com/products/community/) から開発されました。 このサービスは、コミュニティでサポートされている現在のメジャー バージョン (MySQL 5.6、5.7、8.0) をすべてサポートしています。 MySQL では、X.Y.Z の名前付けスキームが使用されています。ここで、X はメジャー バージョン、Y はマイナー バージョン、Z はバグ修正リリースです。 スキームの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)をご覧ください。



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>特定の MySQL バージョンが実行されているゲートウェイ ノードに接続する

シングルサーバー デプロイ オプションでは、ゲートウェイを使用してサーバー インスタンスに接続がリダイレクトされます。 接続が確立すると、MySQL には、MySQL サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MySQL のバージョンが表示されます。 MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。 Azure Database for MySQL サービス アーキテクチャ内のゲートウェイの詳細については、「[接続のアーキテクチャ](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture)」を参照してください。

Azure Database for MySQL ではメジャー バージョン v5.6、v5.7、v8.0 がサポートされているため、Azure Database for MySQL に接続するための既定のポート 3306 では MySQL クライアント バージョン 5.6 (最小の共通分母) が実行され、サポートされている 3 つすべてのメジャー バージョンのサーバーへの接続に対応しています。 ただし、アプリケーションに、特定のメジャー バージョン (たとえば、v5.7 や v8.0) に接続する要件がある場合は、サーバー接続文字列のポートを変更することによって実行できます。

Azure Database for MySQL サービスでは、ゲートウェイ ノードは、v5.7 クライアントの場合はポート 3308、v8.0 クライアントの場合はポート 3309 でリッスンします。 言い換えると、v 5.7 ゲートウェイ クライアントに接続する場合は、完全修飾サーバー名とポート 3308 を使用して、クライアント アプリケーションからサーバーに接続する必要があります。 同様に、v8.0 ゲートウェイ クライアントに接続する場合は、完全修飾サーバー名とポート 3309 を使用してサーバーに接続することができます。 次の例を確認すると、さらにわかりやすくなります。

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="異なるバージョンのゲートウェイの MySQL を使用した接続の例":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL では、現在、次の MySQL メジャーおよびマイナー バージョンがサポートされています。


| Version | [単一サーバー](overview.md) <br/> 現行のマイナー バージョン |[フレキシブル サーバー (プレビュー)](/azure/mysql/flexible-server/overview) <br/> 現行のマイナー バージョン  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL バージョン 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (廃止) | サポートされていません|
|MySQL バージョン 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL バージョン 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

[バージョン サポート ポリシーのドキュメント](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)で、廃止されたバージョンのバージョン サポート ポリシーを参照してください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
このサービスでは、バグ修正バージョンの更新プログラムの適用が自動管理されます。 たとえば、5.7.20 から 5.7.21 などです。  

現在、メジャー バージョンのアップグレードは、MySQL v5.6 から v5.7 へのアップグレードがサポートされています。 詳細については、[メジャー バージョンのアップグレードの実行方法](how-to-major-version-upgrade.md)に関する記事を参照してください。 5\.7 から 8.0 にアップグレードする場合は、[ダンプを実行し、新しいエンジンのバージョンで作成されたサーバーにそれを復元](./concepts-migrate-dump-restore.md)することをお勧めします。

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL のバージョン管理ポリシーの詳細については、[このドキュメント](concepts-version-policy.md)を参照してください。
- **サービス レベル** に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください
