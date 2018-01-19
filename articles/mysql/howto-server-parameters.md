---
title: "Azure Database for MySQL のサーバー パラメーターの構成方法 | Microsoft Docs"
description: "この記事では、Azure ポータルを使用して Azure Database for MySQL で使用できる MySQL サーバー パラメータを構成する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/10/2017
ms.openlocfilehash: f3b32c1f6b33bc60b50f1496414a300db468dc92
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure ポータルを使用して Azure Database for MySQL のサーバー パラメーターを構成する方法

Azure Database for MySQL では、いくつかのサーバー パラメーターの構成をサポートします。 このトピックでは、Azure ポータルを使用してこれらのパラメーターを構成する方法について説明します。 すべてのサーバー パラメーターを調整できるわけではありません。 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Azure ポータルの [サーバー パラメーター] に移動する
1. Azure ポータルにサインインし、お使いの Azure Database for MySQL サーバーを探します。
2. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL の [サーバー パラメーター] ページを開きます。
3. 調整が必要な設定を探します。 **[説明]** 列を確認して、目的と許可される値を理解します。 
4. **[保存]** をクリックして変更を保存します。

![Azure ポータルの [サーバー パラメーター] ブレード](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>構成可能なサーバー パラメーターの一覧

サポートされるサーバー パラメーターの一覧は、拡大を続けています。 Azure ポータルのサーバー パラメーターのタブを使用して定義を取得し、お使いのアプリケーションの要件に基づいてサーバー パラメーターを構成します。 

## <a name="nonconfigurable-server-parameters"></a>構成不可能なサーバー パラメーター
InnoDB バッファー プールと最大接続数は構成できず、[価格レベル](concepts-service-tiers.md)に関連付けられています。 

| **価格レベル** | **InnoDB バッファー プール (MB)** | **最大接続数** |
| :------------------------ | :-------- | :----------- |
| Basic 50 | 1024 | 50 | 
| Basic 100  | 2560 | 100 | 
| Standard 100 | 2560 | 200 | 
| Standard 200 | 5120 | 400 | 
| Standard 400 | 10240 | 800 | 
| Standard 800 | 20480 | 1600 |

次に示す追加のサーバー パラメーターは、システム内で構成不可能です <br>
 Basic レベルの innodb_file_per_table: オフ<br>
 innodb_flush_log_at_trx_commit = 1<br>
 sync_binlog = 1<br>
 innodb_log_file_size = 512 MB<br>
 
ここに表示されていないその他のサーバー パラメーターはすべて、バージョン [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) および [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) の MySQL の既定値に設定されます。

## <a name="next-steps"></a>次の手順
- [Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)
