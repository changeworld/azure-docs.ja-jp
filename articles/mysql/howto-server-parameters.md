---
title: サーバー パラメーターの構成 - Azure portal - Azure Database for MySQL
description: この記事では、Azure ポータルを使用して Azure Database for MySQL で使用できる MySQL サーバー パラメータを構成する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: bd0a867cce9b2a9ad793b491b9042034ef5810f5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605153"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure ポータルを使用して Azure Database for MySQL のサーバー パラメーターを構成する方法

Azure Database for MySQL では、いくつかのサーバー パラメーターの構成をサポートします。 この記事では、Azure Portal を使用してこれらのパラメーターを構成する方法について説明します。 すべてのサーバー パラメーターを調整できるわけではありません。

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Azure ポータルの [サーバー パラメーター] に移動する

1. Azure ポータルにサインインし、お使いの Azure Database for MySQL サーバーを探します。
2. **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL サーバーの [サーバー パラメーター] ページを開きます。
![Azure Portal の [サーバー パラメーター] ページ](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 調整が必要な設定を探します。 **[説明]** 列を確認して、目的と許可される値を理解します。
![列挙ドロップ ダウン](./media/howto-server-parameters/3-toggle_parameter.png)
4. **[保存]** をクリックして変更を保存します。
![変更の保存または破棄](./media/howto-server-parameters/4-save_parameters.png)
5. パラメーターの新しい値を保存した場合は、 **[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
![すべて既定値にリセット](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>構成可能なサーバー パラメーターの一覧

サポートされるサーバー パラメーターの一覧は、拡大を続けています。 Azure ポータルのサーバー パラメーターのタブを使用して定義を取得し、お使いのアプリケーションの要件に基づいてサーバー パラメーターを構成します。

## <a name="non-configurable-server-parameters"></a>構成不可能なサーバー パラメーター

InnoDB バッファー プールのサイズは構成できず、ご自分の[価格レベル](concepts-service-tiers.md)に関連付けられています。

|**価格レベル**|**仮想コア数**|**InnoDB バッファー プールのサイズ (MB) <br>(最大 4 TB のストレージをサポートするサーバー)**| **InnoDB バッファー プールのサイズ (MB) <br>(最大 16 TB のストレージをサポートするサーバー)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|General Purpose| 2| 3584| 7168|
|General Purpose| 4| 7680| 15360|
|General Purpose| 8| 15360| 30720|
|General Purpose| 16| 31232| 62464|
|General Purpose| 32| 62976| 125952|
|General Purpose| 64| 125952| 251904|
|メモリ最適化| 2| 7168| 14336|
|メモリ最適化| 4| 15360| 30720|
|メモリ最適化| 8| 30720| 61440|
|メモリ最適化| 16| 62464| 124928|
|メモリ最適化| 32| 125952| 251904|

次に示す追加のサーバー パラメーターは、システム内で構成できません。

|**パラメーター**|**固定値**|
| :------------------------ | :-------- |
|Basic レベルの innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

ここに表示されていないその他のサーバー パラメーターはすべて、バージョン [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) および [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) の MySQL の既定値に設定されます。

## <a name="working-with-the-time-zone-parameter"></a>タイム ゾーン パラメーターを使用する

### <a name="populating-the-time-zone-tables"></a>タイム ゾーン テーブルに入力する

サーバーのタイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。

> [!NOTE]
> MySQL Workbench から `mysql.az_load_timezone` コマンドを実行するとき、場合によっては、`SET SQL_SAFE_UPDATES=0;` を利用し、最初にセーフ アップデート モードをオフにする必要があります。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> タイム ゾーン テーブルにデータが正しく入力されるようにするには、サーバーを再起動する必要があります。 サーバーを再起動するには、[Azure portal](howto-restart-server-portal.md) または [CLI](howto-restart-server-cli.md) を使用します。

利用可能なタイム ゾーン値を表示するには、次のコマンドを実行します。

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>グローバル レベルのタイム ゾーンを設定する

Azure Portal の **[サーバー パラメーター]** ページからグローバル レベルのタイム ゾーンを設定できます。 下の画像では、グローバル タイム ゾーンが値 "US/Pacific" に設定されています。

![タイム ゾーン パラメーターを設定する](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>セッション レベルのタイム ゾーンを設定する

セッション レベルのタイム ゾーンは、MySQL コマンド ラインや MySQL Workbench などのツールから `SET time_zone` コマンドを実行することで設定できます。 下の例では、タイム ゾーンが **US/Pacific** タイム ゾーンに設定されます。

```sql
SET time_zone = 'US/Pacific';
```

[日付と時刻関数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)については MySQL ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)
