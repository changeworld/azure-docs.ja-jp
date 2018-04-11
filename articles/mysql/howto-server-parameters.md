---
title: Azure Database for MySQL のサーバー パラメーターの構成方法
description: この記事では、Azure ポータルを使用して Azure Database for MySQL で使用できる MySQL サーバー パラメータを構成する方法について説明します。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6865663bebc84df288f4c7e2564ddb4870667c6f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure ポータルを使用して Azure Database for MySQL のサーバー パラメーターを構成する方法

Azure Database for MySQL では、いくつかのサーバー パラメーターの構成をサポートします。 この記事では、Azure Portal を使用してこれらのパラメーターを構成する方法について説明します。 すべてのサーバー パラメーターを調整できるわけではありません。 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Azure ポータルの [サーバー パラメーター] に移動する
1. Azure ポータルにサインインし、お使いの Azure Database for MySQL サーバーを探します。
2. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL の [サーバー パラメーター] ページを開きます。
![Azure Portal の [サーバー パラメーター] ページ](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 調整が必要な設定を探します。 **[説明]** 列を確認して、目的と許可される値を理解します。 
![列挙ドロップ ダウン](./media/howto-server-parameters/3-toggle_parameter.png)
4. **[保存]** をクリックして変更を保存します。
![変更の保存または破棄](./media/howto-server-parameters/4-save_parameters.png)
5. パラメーターの新しい値を保存した場合は、**[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
![すべて既定値にリセット](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>構成可能なサーバー パラメーターの一覧

サポートされるサーバー パラメーターの一覧は、拡大を続けています。 Azure ポータルのサーバー パラメーターのタブを使用して定義を取得し、お使いのアプリケーションの要件に基づいてサーバー パラメーターを構成します。 

## <a name="nonconfigurable-server-parameters"></a>構成不可能なサーバー パラメーター
InnoDB バッファー プールと最大接続数は構成できず、[価格レベル](concepts-service-tiers.md)に関連付けられています。 

|**価格レベル**| **コンピューティング世代**|**仮想コア数**|**InnoDB バッファー プール (MB)**| **最大接続数**|
|---|---|---|---|--|
|Basic| Gen 4| 1| 1024| 50|
|Basic| Gen 4| 2| 2560| 100|
|Basic| Gen 5| 1| 1024| 50|
|Basic| Gen 5| 2| 2560| 100|
|汎用| Gen 4| 2| 3584| 300|
|汎用| Gen 4| 4| 7680| 625|
|汎用| Gen 4| 8| 15360| 1250|
|汎用| Gen 4| 16| 31232| 2500|
|汎用| Gen 4| 32| 62976| 5000|
|汎用| Gen 5| 2| 3584| 300|
|汎用| Gen 5| 4| 7680| 625|
|汎用| Gen 5| 8| 15360| 1250|
|汎用| Gen 5| 16| 31232| 2500|
|汎用| Gen 5| 32| 62976| 5000|
|メモリ最適化| Gen 5| 2| 7168| 600|
|メモリ最適化| Gen 5| 4| 15360| 1250|
|メモリ最適化| Gen 5| 8| 30720| 2500|
|メモリ最適化| Gen 5| 16| 62464| 5000|

次に示す追加のサーバー パラメーターは、システム内で構成できません。

|**パラメーター**|**固定値**|
| :------------------------ | :-------- |
|Basic レベルの innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

ここに表示されていないその他のサーバー パラメーターはすべて、バージョン [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) および [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) の MySQL の既定値に設定されます。

## <a name="next-steps"></a>次の手順
- [Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)
