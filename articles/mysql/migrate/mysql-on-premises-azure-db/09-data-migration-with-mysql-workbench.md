---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行: MySQL Workbench を使用したデータ移行'
description: セットアップ ガイドのすべての手順に従って、次の手順をサポートするための環境を作成します。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 2b3dc8702251a6fcc53386cb17cbe44a45e59db2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292973"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-data-migration-with-mysql-workbench"></a>オンプレミスの MySQL から Azure Database for MySQL への移行: MySQL Workbench を使用したデータ移行

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[データの移行](08-data-migration.md)

## <a name="setup"></a>セットアップ

セットアップ ガイドのすべての手順に従って、次の手順をサポートするための環境を作成します。

## <a name="configuring-server-parameters-source"></a>サーバー パラメーターの構成 (ソース)

選択した移行の種類 (オフラインかオンライン) によっては、データの高速エグレスをサポートするためにサーバー パラメーターを変更するかどうかを評価する必要があります。 オンラインで実行している場合は、`binlog` レプリケーションを実行し、データを単独で同期する可能性が高いため、サーバー パラメーターに対して何も行う必要はありません。 ただし、オフライン移行を実行している場合は、アプリケーション トラフィックを停止したら、サーバー パラメーターをワークロードのサポートからエクスポートのサポートに切り替えることができます。

## <a name="configuring-server-parameters-target"></a>サーバー パラメーターの構成 (ターゲット)

Azure Database for MySQL へのインポート プロセスを開始する前に、サーバー パラメーターを確認します。 サーバー パラメーターを取得して設定するには、[Azure portal](../../howto-server-parameters.md) を使用するか、[MySQL 用の Azure PowerShell コマンドレット](../../howto-configure-server-parameters-using-powershell.md)を呼び出します。

次の PowerShell スクリプトを実行して、すべてのパラメーターを取得します。

```
\[Net.ServicePointManager\]::SecurityProtocol = \[Net.SecurityProtocolType\]::Tls
12

Install-Module -Name Az.MySql
Connect-AzAccount
$rgName = "{RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";
Get-AzMySqlConfiguration -ResourceGroupName $rgName -ServerName $serverName
```

- mysql ツールで同じことを行うには、[CA ルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)を c:\\temp にダウンロードします (このディレクトリを作成します)。

    > [!NOTE]
    > 証明書は変更される場合があります。 最新の証明書情報については、「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](../../howto-configure-ssl.md)」を参照してください。
    
- コマンド プロンプトで次を実行します。必ずトークンを更新してください。

```
mysql --host {servername}.mysql.database.azure.com --database mysql --user 
{u sername}@{servername} -p --ssl-ca=c:\\temp\\BaltimoreCyberTrustRoot.crt.cer
-A -e "SHOW GLOBAL VARIABLES;" \> c:\\temp\\settings\_azure.txt
```

[付録](15-appendix.md#environment-setup)で示されているように、新しい \`settings\_azure.txt\` ファイルで、Azure Database for MySQL の既定のサーバー パラメーターを確認できます。

移行をサポートするには、ターゲットの MySQL インスタンス パラメーターを設定して、より高速なイングレスを実現します。 データ移行を開始する前に、次のサーバー パラメーターを設定する必要があります。

- `max\_allowed\_packet` – 長い行によるオーバーフローの問題を防ぐために、 パラメーターを `1073741824` (つまり 1 GB) またはデータベース内の行の最大サイズに設定します。 プルアウト (または読み取り) が必要な大きな BLOB 行がある場合は、このパラメーターの調整を検討してください。

- `innodb\_buffer\_pool\_size` – 移行中に、サーバーをポータルの価格レベルから 32 仮想コア メモリ最適化 SKU にスケールアップし、innodb\_buffer\_pool\_size を増やします。 innodb\_buffer\_pool\_size は、Azure Database for MySQL サーバーのコンピューティングをスケールアップしないと増やすことができません。 階層の最大値については、「[Azure Database for MySQL でのサーバー パラメーター](../../concepts-server-parameters.md#innodb_buffer_pool_size)」を参照してください。 メモリ最適化 32 仮想コア システムの最大値は `132070244352` です。

- `innodb\_io\_capacity` & `innodb\_io\_capacity\_max` - 移行速度の最適化のために IO 使用率を向上させるには、パラメーターを `9000` に変更します。

- `max\_connections` - スループットを向上するために複数のスレッドを生成するツールを使用している場合は、そのツールがサポートされるように接続を増やします。 既定値は `151`、最大値は `5000` です。

    > [!NOTE]
    > スケーリングは注意して実行してください。 ストレージのスケーリングなど、一部の操作は元に戻すことができません。
    
これらの設定は、次の Azure PowerShell コマンドレットを使用して更新できます。

```
Install-Module -Name Az.MySql
$rgName = " {RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";

Select-AzSubscription -Subscription "{SUBSCRIPTION\_ID}"
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName
$rgna me -ServerName $serverName -Value 1073741824  
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName 
$rgna me -ServerName $serverName -Value 9000
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 9000

\#required if You've functions

Update-AzMySqlConfiguration -Name log\_bin\_trust\_function\_creators 
-ResourceGr oupName $rgname -ServerName $serverName -Value ON
```
## <a name="data"></a>データ

### <a name="tool-choice"></a>ツールの選択

ソース システムのデータベース オブジェクトとユーザーを移行すると、移行を開始できます。 MySQL バージョン 8.0 で実行されているデータベースでは、Azure DMS を使用してワークロードを移行することはできません。 代わりに、移行ユーザーは MySQL Workbench を使用する必要があります。

### <a name="manual-import-and-export-steps"></a>手動のインポートおよびエクスポート手順

- MySQL Workbench を開き、ローカル データベースのルート ユーザーとして接続します。

- \*\*[管理]\*\* で \*\*[データ エクスポート]\*\* を選択します。 **reg\_app** スキーマを選択します。

- **[エクスポートするオブジェクト]** で、 **[Dump Stored Procedures and Functions]\(ストアド プロシージャと関数のダンプ\)** 、 **[Dump Events]\(イベントのダンプ\)** および **[Dump Triggers]\(トリガーのダンプ\)** を選択します。

- **[エクスポート オプション]** で、 **[自己完結型ファイルへのエクスポート]** を選択します。

- **[スキーマの作成を含める]** チェックボックスも選択します。 次の図を参照して、正しい mysqldump 構成を確認してください。

    ![スキーマの作成を含める](./media/image6.jpg)

    **テスト**

- これらのオプションのいずれかが使用できない場合は、[出力] ウィンドウが邪魔をしている可能性があります。 エディターのレイアウトを変更してみてください。

    ![エディターのレイアウト](./media/image7.jpg)

    **テスト**

- **[エクスポートの進行状況]** タブを選択します。

- **[エクスポートの開始]** を選択すると、MySQL Workbench が `mysqldump` ツールを呼び出します。

- 新しく作成したエクスポート スクリプトを開きます。

- `DEFINER` ステートメントを見つけて、有効なユーザーに変更するか、完全に削除します。

> [!NOTE]
> これを行うには、mysqldump コマンドで `--skip-definer` を渡します。 これは MySQL Workbench のオプションではありません。したがって、行はエクスポート コマンドで手動で削除する必要があります。 ここでは削除する 4 つの項目を示しますが、MySQL のバージョン間で (新しい予約語などを) 移行するときにその他の項目が失敗する可能性があります。

- `SET GLOBAL` ステートメントを検索し、有効なユーザーに変更するか、完全に削除します。

- `sql\_mode` が `NO\_AUTO\_CREATE\_USER` に設定されていないことを確認します。

- `hello\_world` 関数を削除します。

- MySQL Workbench で、Azure Database for MySQL への新しい接続を作成します。

    - [ホスト名] に、完全なサーバー DNS (例: `servername.mysql.database.azure.com`) を入力します。

    - ユーザー名 (例: `sqlroot@servername`) を入力します。

    - **[SSL]** タブを選択します。

    - SSL CA ファイルの場合は **、BaltimoreCyberTrustRoot.crt.cer** キー ファイルを参照します。

    - **[テスト接続]** を選択し、接続が完了していることを確認します。

    - **[OK]** を選択します。

        ![MySQL 接続ダイアログ ボックス](./media/image8.jpg)

        **MySQL 接続ダイアログ ボックスが表示されます。**

- **[ファイル] - \>[SQL スクリプトを開く]** を選択します。

- ダンプ ファイルを参照し、 **[開く]** を選択します。

- **[実行]** を選択します。

## <a name="update-applications-to-support-ssl"></a>SSL をサポートするためにアプリケーションを更新する

- Visual Studio コードで Java Server API に切り替えます。

- **launch.json** ファイルを開きます。

- **DB\_CONNECTION\_URL** を `jdbc:mysql://serverDNSname:3306/reg\_app?useUnicode=true\&useJDBCCompliantT imezoneShift=true\&useLegacyDatetimeCode=false\&serverTimezone=UTC\&verifySe rverCertificate=true\&useSSL=true\&requireSSL=true\&noAccessToProcedureBodie s=true.` に更新します。追加の SSL パラメーターに注目してください。

- **DB\_USER\_NAME** を **conferenceuser@servername** に更新します。

- デバッグ構成を開始し、アプリケーションが新しいデータベースで、ローカルで動作していることを確認します。

## <a name="revert-server-parameters"></a>サーバー パラメーターを元に戻す

次のパラメーターは、Azure Database for MySQL ターゲット インスタンス上で変更できます。 これらのパラメーターは、Azure portal を使用するか、[MySQL 用 Azure PowerShell コマンドレット](../../howto-configure-server-parameters-using-powershell.md)を使用して設定できます。

```
$rgName = "YourRGName";
$serverName = "servername";
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName 
$rgna me -ServerName $serverName -Value 536870912
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName $rgna 
me -ServerName $serverName -Value 200
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 2000
```
## <a name="change-connection-string-for-the-java-api"></a>Java API の接続文字列を変更する

- App Service Java API の接続文字列を変更するには、次のコマンドを使用します

```
$rgName = "YourRGName"; 
$app_name = "servername";
az webapp config appsettings set -g $rgName -n $app_name 
--settings DB_CONNECTION_URL={DB_CONNECTION_URL}
```

> [!NOTE]
> 接続文字列の設定には、ポータルを使用することができます。

- App Service API を再起動します

```
az webapp restart -g $rgName -n $app\_name
```
オンプレミスを Azure Database for MySQL に移行できました\!  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [移行後の管理](./10-post-migration-management.md)