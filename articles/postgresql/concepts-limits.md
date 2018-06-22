---
title: Azure Database for PostgreSQL の制限事項
description: この記事では、Azure Database for PostgreSQL の制限 (接続数やストレージ エンジンのオプションなど) について説明します。
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/04/2018
ms.openlocfilehash: 5cd829236d8d8a58e68f7bf766790aa3f0cb656e
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757418"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の制限事項
次のセクションでは、データベース サービス容量と機能の制限について説明します。

## <a name="maximum-connections"></a>最大接続数
価格レベルと仮想コアごとの最大接続数は次のとおりです。 

|**価格レベル**| **仮想コア数**| **最大接続数** |
|---|---|---|
|Basic| 1| 50 |
|Basic| 2| 100 |
|汎用| 2| 150|
|汎用| 4| 250|
|汎用| 8| 480|
|汎用| 16| 950|
|汎用| 32| 1500|
|メモリ最適化| 2| 150|
|メモリ最適化| 4| 250|
|メモリ最適化| 8| 480|
|メモリ最適化| 16| 950|

接続数が制限を超えると、次のエラーが表示される場合があります。
> FATAL:  sorry, too many clients already

Azure システムでは、Azure Database for PostgreSQL サーバーを監視する 5 つの接続が必要です。 

## <a name="functional-limitations"></a>機能制限
### <a name="scale-operations"></a>スケール操作
1.  価格レベル間でのサーバーの動的スケーリングは現在サポートされていません。 つまり、Basic、汎用、メモリ最適化の各レベル間の切り替えはサポートされません。
2.  現在、サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。

### <a name="subscription-management"></a>サブスクリプション管理
- サブスクリプションとリソース グループ間でのサーバーの動的な移動は現在サポートされていません。

### <a name="point-in-time-restore-pitr"></a>ポイントインタイム リストア (PITR)
1.  PITR 機能を使うと、基になっているサーバーと同じ構成で新しいサーバーが作成されます。
2.  削除されたサーバーへの復元はサポートされていません。

## <a name="next-steps"></a>次の手順
- [各価格レベルで使用できる内容](concepts-pricing-tiers.md)について理解します
- [サポートされている PostgreSQL Database バージョン](concepts-supported-versions.md)について学習します
- [Azure Portal を使用して Azure Database for PostgreSQL でサーバーをバックアップして復元する方法](howto-restore-server-portal.md)を確認します
