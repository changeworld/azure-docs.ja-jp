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
ms.date: 10/8/2017
ms.openlocfilehash: dc5ad012398b7d07886cd22c20975e61f820d7e0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Azure Database for MySQL の制限事項 (プレビュー)
Azure Database for MySQL サービスはパブリック プレビューの段階です。 次のセクションでは、データベース サービス容量と機能の制限について説明します。 MySQL データベース エンジンに適用できる[一般的な制限事項](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)も確認してください。

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
| Standard 400 コンピューティング ユニット | 400 接続   |
| Standard 800 コンピューティング ユニット | 1600 接続  |
| **最大コンピューティング ユニット数**      |                   |
| Basic サービス レベル         | 100 コンピューティング ユニット |
| Standard サービス レベル      | 800 コンピューティング ユニット |
| **最大ストレージ**            |                   |
| Basic サービス レベル         | 1 TB (テラバイト)              |
| Standard サービス レベル      | 1 TB (テラバイト)              |

接続数が多すぎると、次のエラーが発生する可能性があります。
> ERROR 1040 (08004): Too many connections

## <a name="preview-functional-limitations"></a>プレビュー中の機能制限

### <a name="scale-operations"></a>スケール操作
- サービス レベル間でのサーバーの動的スケーリングは現在サポートされていません。 つまり、Basic サービス レベルと Standard サービス レベルの間での切り替えです。
- 現時点では、事前に作成されたサーバーのストレージをオンデマンドで動的に増やすことはできません。
- サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。

### <a name="subscription-management"></a>サブスクリプション管理
- サブスクリプションとリソース グループ間での事前作成されたサーバーの動的な移動は現在サポートされていません。

### <a name="point-in-time-restore"></a>ポイントインタイム リストア
- 別のサービス レベルやコンピューティング ユニットおよびストレージ サイズに復元することはできません。
- 破棄されたサーバーへの復元はサポートされていません。

## <a name="next-steps"></a>次のステップ
- [各サービス レベルで使用できる内容について](concepts-service-tiers.md)
- [サポートされている MySQL Database バージョン](concepts-supported-versions.md)
