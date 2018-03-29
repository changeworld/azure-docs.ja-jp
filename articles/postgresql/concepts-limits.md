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
ms.date: 03/20/2018
ms.openlocfilehash: 7e06cdba7c9c9f7e5c1d621e7421a18c342c0fdb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の制限事項
次のセクションでは、データベース サービス容量と機能の制限について説明します。

## <a name="pricing-tier-maximums"></a>価格レベルの上限
Azure Database for PostgreSQL では、サーバーを作成するときに複数の価格レベルから選ぶことができます。 詳しくは、「[Azure Database for PostgreSQL の価格レベル](concepts-pricing-tiers.md)」をご覧ください。  

各価格レベルの接続数、コンピューティング ユニット数、およびストレージ数の最大値は次のとおりです。 

|価格レベル| コンピューティング世代| 仮想コア数| 最大接続数 |
|---|---|---|---|
|Basic| Gen 4| 1| 50 |
|Basic| Gen 4| 2| 100 |
|Basic| Gen 5| 1| 50 |
|Basic| Gen 5| 2| 100 |
|汎用| Gen 4| 2| 150|
|汎用| Gen 4| 4| 250|
|汎用| Gen 4| 8| 480|
|汎用| Gen 4| 16| 950|
|汎用| Gen 4| 32| 1500|
|汎用| Gen 5| 2| 150|
|汎用| Gen 5| 4| 250|
|汎用| Gen 5| 8| 480|
|汎用| Gen 5| 16| 950|
|汎用| Gen 5| 32| 1500|
|メモリ最適化| Gen 5| 2| 150|
|メモリ最適化| Gen 5| 4| 250|
|メモリ最適化| Gen 5| 8| 480|
|メモリ最適化| Gen 5| 16| 950|

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
