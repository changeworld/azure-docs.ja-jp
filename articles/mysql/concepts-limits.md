---
title: "Azure Database for MySQL の制限事項 | Microsoft Docs"
description: "Azure Database for MySQL のプレビュー中の制限事項について説明します。"
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/11/2018
ms.openlocfilehash: f0f9a10f987f19d8ae77a07038cffe23446856fd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL の制限事項
Azure Database for MySQL サービスはパブリック プレビューの段階です。 以降のセクションでは、容量、ストレージ エンジンのサポート、権限のサポート、データ操作ステートメントのサポート、およびデータベース サービスの機能に関する制限事項について説明します。 MySQL データベース エンジンに適用できる[一般的な制限事項](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)も確認してください。

## <a name="service-tier-maximums"></a>サービス レベルの最大値
Azure Database for MySQL では、サーバーを作成するときに複数のサービス レベルから選択します。 詳細については、[各サービス レベルで使用できる内容](concepts-service-tiers.md)に関するページをご覧ください。  

プレビュー中の各サービス レベルの接続数、コンピューティング ユニット数、およびストレージ数の最大値は次のとおりです。 

|                            |                   |
| :------------------------- | :---------------- |
| **最大接続数**        |                   |
| Basic: 50 コンピューティング ユニット     | 50 接続    |
| Basic 100 コンピューティング ユニット    | 100 接続   |
| Standard 100 コンピューティング ユニット | 200 接続   |
| Standard 200 コンピューティング ユニット | 400 接続   |
| Standard 400 コンピューティング ユニット | 800 接続   |
| Standard 800 コンピューティング ユニット | 1600 接続  |
| **最大コンピューティング ユニット数**      |                   |
| Basic サービス レベル         | 100 コンピューティング ユニット |
| Standard サービス レベル      | 800 コンピューティング ユニット |
| **最大ストレージ**            |                   |
| Basic サービス レベル         | 1 TB (テラバイト)              |
| Standard サービス レベル      | 1 TB (テラバイト)              |

接続数が多すぎると、次のエラーが発生する可能性があります。
> ERROR 1040 (08004): Too many connections

## <a name="storage-engine-support"></a>ストレージ エンジンのサポート

### <a name="supported"></a>サポートされています
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>サポートされていません
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>権限のサポート

### <a name="unsupported"></a>サポートされていません
- DBA ロール  DBA ロールでは、多くのサーバー パラメーターおよび設定によって、誤ってサーバー パフォーマンスを低下させたり、DBMS の ACID プロパティを負数にしてしまったりする恐れがあります。 そのため、製品レベルのサービス整合性と SLA を維持するために、DBA ロールは顧客に公開していません。 新しいデータベース インスタンスの作成時に構成される既定のユーザー アカウントによって、顧客は管理データベース インスタンスでほとんどの DDL および DML ステートメントを実行できます。 
- SUPER 権限  同様に、SUPER 権限 ([SUPER 権限について](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)の記述を参照) も制限されています。

## <a name="data-manipulation-statement-support"></a>データ操作ステートメントのサポート

### <a name="supported"></a>サポートされています
- LOAD DATA INFILE - サポートされています。ただし、UNC パス (XSMB 経由でマウントされた Azure ストレージ) にリダイレクトされる [LOCAL] パラメーターを指定する必要があります。

### <a name="unsupported"></a>サポートされていません
- SELECT ...INTO OUTFILE

## <a name="preview-functional-limitations"></a>プレビュー中の機能制限

### <a name="scale-operations"></a>スケール操作
- サービス レベル間でのサーバーの動的スケーリングは現在サポートされていません。 つまり、Basic サービス レベルと Standard サービス レベルの間での切り替えです。
- 現時点では、事前に作成されたサーバーのストレージをオンデマンドで動的に増やすことはできません。
- サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。

### <a name="point-in-time-restore"></a>ポイントインタイム リストア
- 別のサービス レベルやコンピューティング ユニットおよびストレージ サイズに復元することはできません。
- 削除されたサーバーへの復元はサポートされていません。

## <a name="functional-limitations"></a>機能制限

### <a name="subscription-management"></a>サブスクリプション管理
- サブスクリプションとリソース グループ間での事前作成されたサーバーの動的な移動は現在サポートされていません。

## <a name="current-known-issues"></a>現時点での既知の問題
- MySQL サーバー インスタンスでは、接続が確立された後に不正確なサーバー バージョンが表示されます。 正しいサーバー インスタンス バージョンを取得するには、MySQL プロンプトで select version(); コマンドを使用します。

## <a name="next-steps"></a>次の手順
- [各サービス レベルで使用できる内容について](concepts-service-tiers.md)
- [サポートされている MySQL Database バージョン](concepts-supported-versions.md)
