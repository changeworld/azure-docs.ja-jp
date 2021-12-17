---
title: アプリのバックアップ
description: Azure App Service でアプリのバックアップを作成する方法を説明します。 手動またはスケジュールされたバックアップを実行します。 接続済データベースを含めることにより、バックアップをカスタマイズします。
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 09/02/2021
ms.custom: seodec18
ms.openlocfilehash: f1bd37c1b3557a8106981377f9ed75c50a28c773
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433055"
---
# <a name="back-up-your-app-in-azure"></a>Azure でのアプリのバックアップ

[Azure App Service](overview.md) のバックアップと復元の機能により、アプリのバックアップを手動またはスケジュール設定により簡単に作成できます。 バックアップが無期限に保持されるように構成できます。 以前の状態のスナップショットにアプリを復元するには、既存のアプリを上書きするか、別のアプリに対して復元を行います。

アプリをバックアップから復元する方法については、 [Azure でのアプリの復元](web-sites-restore.md)に関するページを参照してください。

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>バックアップ対象

App Service によって、アプリで使用するようにユーザーが構成した Azure ストレージ アカウントとコンテナーに、次の情報をバックアップできます。

* アプリの構成
* ファイルのコンテンツ
* アプリに接続されているデータベース

次のデータベース ソリューションがバックアップ機能でサポートされています。

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [アプリ内 MySQL](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)

> [!NOTE]
> 各バックアップは増分更新ではなく、アプリの完全なオフライン コピーです。
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>要件および制限

* バックアップと復元の機能を使用するには、**Standard**、**Premium**、**Isolated** のいずれかのレベルの App Service プランが必要です。 上位レベルを使用するための App Service プランの拡張の詳細については、 [Azure でのアプリのスケールアップ](manage-scale-up.md)に関するページを参照してください。 **Premium** レベルと **Isolated** レベルでは、**Standard** レベルよりも多くの回数の日次バックアップが可能です。
* バックアップするアプリと同じサブスクリプション内に Azure ストレージ アカウントとコンテナーが必要です。 Azure ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../storage/common/storage-account-overview.md)」をご覧ください。
* 最大 10 GB のアプリとデータベースのコンテンツをバックアップでき、そのうち最大 4 GB までをデータベースのバックアップとすることが可能です。 バックアップのサイズがこの制限を超えた場合、エラーが発生します。
* [TLS が有効になっている Azure Database for MySQL](../mysql/concepts-ssl-connection-security.md) のバックアップはサポートされていません。 バックアップが構成されている場合、バックアップ エラーが発生します。
* [TLS が有効になっている Azure Database for PostgreSQL](../postgresql/concepts-ssl-connection-security.md) のバックアップはサポートされていません。 バックアップが構成されている場合、バックアップ エラーが発生します。
* アプリ内 MySQL データベースは、構成しなくても自動的にバックアップされます。 接続文字列を追加するなど、アプリ内 MySQL データベースを手動で設定すると、バックアップが正しく動作しない場合があります。
* バックアップの保存先として、[ファイアウォールが有効なストレージ アカウント](../storage/common/storage-network-security.md)を使用することは、サポートされていません。 バックアップが構成されている場合、バックアップ エラーが発生します。
* [プライベート エンドポイントが有効なストレージ アカウント](../storage/common/storage-private-endpoints.md)をバックアップと復元に使用することはサポートされていません。

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>手動バックアップの作成

1. [Azure Portal](https://portal.azure.com) で、アプリのページに移動し、 **[バックアップ]** を選択します。 **[バックアップ]** ページが表示されます。

    ![バックアップ ページ](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > 次のメッセージが表示された場合は、メッセージをクリックして App Service プランをアップグレードした後、バックアップに進むことができます。
    > 詳細については、 [Azure でのアプリのスケールアップ](manage-scale-up.md) に関するページを参照してください。
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="App Service プランをアップグレードしてバックアップと復元の機能にアクセスすることを示すメッセージが表示されたバナーのスクリーンショット。":::
    >
    >

2. **[バックアップ]** ページで、 **[バックアップが構成されていません。アプリのバックアップを構成するには、ここをクリックしてください]** を選択します。

    ![[構成] をクリックします](./media/manage-backup/configure-start.png)

3. **[バックアップ構成]** ページで、 **[ストレージ: 未構成]** をクリックして、ストレージ アカウントを構成します。

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="[ストレージ: 未構成] の設定が選択されている [ストレージのバックアップ] セクションのスクリーンショット。":::

4. **[ストレージ アカウント]** と **[コンテナー]** を選択して、バックアップ先を選択します。 ストレージ アカウントは、バックアップするアプリと同じサブスクリプションに属する必要があります。 必要に応じて、各ページで新しいストレージ アカウントまたは新しいコンテナーを作成できます。 完了したら、 **[選択]** をクリックします。

5. 開いたままになっている **[バックアップ構成]** ページで、 **[データベースをバックアップする]** を構成し、バックアップに含めるデータベース (SQL Database または MySQL) を選択してから、 **[OK]** をクリックします。

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="バックアップに含めるという選択が示されている [データベースのバックアップ] セクションのスクリーンショット。":::

    > [!NOTE]
    > この一覧に表示されるデータベースでは、その接続文字列がアプリの **[アプリケーション設定]** ページの **[接続文字列]** セクションに存在する必要があります。 
    >
    > アプリ内 MySQL データベースは、構成しなくても自動的にバックアップされます。 接続文字列を追加するなど、アプリ内 MySQL データベースを手動で設定すると、バックアップが正しく動作しない場合があります。
    >
    >

6. **[バックアップ構成]** ページで、 **[保存]** をクリックします。
7. **[バックアップ]** ページで、 **[バックアップ]** をクリックします。

    ![BackUpNow ボタン](./media/manage-backup/manual-backup.png)

    バックアップ処理中に、進行状況についてのメッセージが表示されます。

ストレージ アカウントとコンテナーの構成が終わったら、いつでも手動バックアップを開始できます。 手動バックアップは無期限に保持されます。

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>自動バックアップの構成

1. **[バックアップ構成]** ページで、 **[スケジュールされたバックアップ]** を **オン** に設定します。

    ![自動化されたバックアップを有効にする](./media/manage-backup/scheduled-backup.png)

2. 必要に応じてバックアップのスケジュールを構成し、 **[OK]** を選択します。

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>部分バックアップの構成

アプリのすべてをバックアップしたくない場合があります。 次に例をいくつか示します。

* 古いブログの投稿や画像などの、変更されることがない静的コンテンツを含むアプリを [毎週バックアップするように設定](#configure-automated-backups) している。
* アプリのコンテンツが 10 GB を超えている (つまり、一度にバックアップできる最大量を超えている)。
* ログ ファイルはバックアップしない。

部分バックアップでは、バックアップするファイルを詳細に選択できます。

> [!NOTE]
> バックアップ内の個々のデータベースは最大 4 GB となりますが、バックアップの合計最大サイズは 10 GB です。

### <a name="exclude-files-from-your-backup"></a>バックアップからファイルを除外する

一度バックアップされており今後まったく変更されることがないログ ファイルや静止画像を含むアプリがあるとします。 このような場合には、将来のバックアップ時にそれらのフォルダーやファイルを保存しないように除外できます。 バックアップからファイルやフォルダーを除外するには、アプリの `D:\home\site\wwwroot`フォルダー内に `_backup.filter` ファイルを作成します。 このファイルに、除外するファイルやフォルダーの一覧を指定します。

ファイルにアクセスするには、`https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動します。 メッセージに従って Azure アカウントにサインインします。

バックアップから除外するフォルダーを識別します。 たとえば、強調表示されたフォルダーおよびファイルを除外したいとします。

![images フォルダー](./media/manage-backup/kudu-images.png)

`_backup.filter` という名前のファイルを作成し、前のリストをこのファイルに配置します。ただし `D:\home` は削除します。 1 つのディレクトリまたはファイルを 1 行に配置します。 したがって、ファイルの内容は次のようになります。

```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

`_backup.filter`ファイルを、[ftp](deploy-ftp.md)やその他の方法を使用して、サイトの `D:\home\site\wwwroot\` ディレクトリにアップロードします。 Kudu の `DebugConsole` を使用してファイルを直接作成し、そこにコンテンツを挿入することもできます。

通常と同じ方法 ([手動](#create-a-manual-backup)または[自動](#configure-automated-backups)) でバックアップを実行します。 これで、`_backup.filter` に指定されたファイルとフォルダーは、スケジュール設定されているか手動で開始されるかにかかわらず、将来のバックアップから除外されます。

> [!NOTE]
> [定期的なバックアップからの復元](web-sites-restore.md)と同様の手順で、サイトを部分バックアップから復元することもできます。 復元プロセスは正常に実行されます。
>
> 完全バックアップが復元されると、サイト上のすべてのコンテンツは、バックアップの中身に置き換えられます。 サイト上に存在したファイルも、バックアップに存在しなければ削除されます。 ただし、部分バックアップが復元される場合、制限されたディレクトリまたは制限されたファイルの 1 つに配置されているコンテンツは放置されます。
>

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>バックアップの保存方法

アプリの 1 つ以上のバックアップを作成すると、ストレージ アカウントとアプリの **[コンテナー]** ページに、バックアップが表示されます。 ストレージ アカウントでは、各バックアップは、バックアップ データを含む `.zip` ファイルと、`.zip` ファイルの内容のマニフェストを含む `.xml` ファイルで構成されます。 バックアップにアクセスする場合は、これらのファイルを解凍して参照でき、アプリを実際に復元する必要はありません。

アプリのデータベースのバックアップは、.zip ファイルのルートに保存されます。 SQL Database の場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。 BACPAC のエクスポートに基づいて Azure SQL Database でデータベースを作成するには、[Azure SQL Database でデータベースを作成するための BACPAC ファイルのインポート](../azure-sql/database/database-import.md)に関する記事を参照してください。

> [!WARNING]
> **websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。

## <a name="troubleshooting"></a>トラブルシューティング

**[バックアップ]** ページには、各バックアップの状態が表示されます。 失敗したバックアップをクリックすると、その失敗に関するログの詳細を取得できます。 次の表を使用すると、バックアップのトラブルシューティングを行うのに役立ちます。 エラーが表に記載されていない場合は、サポート チケットを開いてください。

| エラー | 修正 |
| - | - |
| ストレージ アクセスに失敗しました。 | バックアップ スケジュールを削除して再構成します。 または、バックアップ ストレージを再構成します。 |
| Web サイトとデータベースの合計サイズが、バックアップの {0} GB の制限を超えています。 お使いのコンテンツ サイズは {1} GB です。 | バックアップから[一部のファイルを除外する](#configure-partial-backups)か、バックアップのデータベース部分を削除して、代わりに外部提供のバックアップを使用します。 |
| サーバー {1} 上のデータベース {0} への接続中にエラーが発生しました: メソッド 'mysql_native_password' を使用した、ユーザー '\<username>' のホスト '{1}' に対する認証が、不明なデータベース '\<db-name>' というメッセージで失敗しました | データベース接続文字列を更新します。 |
| {0} を解決できません。 {1} (CannotResolveStorageAccount) | バックアップ スケジュールを削除して再構成します。 |
| ユーザー '{0}' はログインできませんでした。 | データベース接続文字列を更新します。 |
| {0} ({1}) のデータベース コピーの作成によって例外がスローされました。 データベース コピーを作成できませんでした。 | 接続文字列の中で管理ユーザーを使用します。 |
| 現在のセキュリティ コンテキストでは、サーバー プリンシパル "\<name>" はデータベース "master" にアクセスできません。 このログインで要求されたデータベース "master" を開けません。 ログインに失敗しました。 ユーザー '\<name>' はログインできませんでした。 | 接続文字列の中で管理ユーザーを使用します。 |
| SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。 インスタンス名が正しいこと、および SQL Server がリモート接続を許可するように構成されていることを確認してください。 (プロバイダー:名前付きパイプ プロバイダー、エラー:40 - SQL Server への接続を開けませんでした)。 | 接続文字列が有効であることを確認します。 データベース サーバーの設定で、アプリの[送信 IP](overview-inbound-outbound-ips.md) を許可します。 |
| ログインにより要求されたサーバー "\<name>" を開けません。 ログインに失敗しました。 | 接続文字列が有効であることを確認します。 |
| 有効な Shared Access Signature で必須のパラメーターがありません。 | バックアップ スケジュールを削除して再構成します。 |
| SSL 接続が必要です。) Please specify SSL options and retry. (SSL のオプションを指定して再試行してください。) 接続を試みるときに。 | 代わりに、Azure MySQL または Azure Postgressql の組み込みバックアップ機能を使用します。 |

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/) を使用すると、バックアップ管理をスクリプトで自動化できます。

サンプルについては、以下を参照してください。

- [Azure CLI のサンプル](samples-cli.md)
- [Azure PowerShell のサンプル](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>次の手順

アプリをバックアップから復元する方法については、 [Azure でのアプリの復元](web-sites-restore.md)に関するページを参照してください。
