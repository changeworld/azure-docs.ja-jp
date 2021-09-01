---
title: mydumper または myloader を使用して大規模なデータベースを Azure Database for MySQL に移行する
description: この記事では、mydumper または myloaderAzure というツールを使用して Azure Database for MySQL でデータベースをバックアップして復元する一般的な 2 つの方法について説明します
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e295e967b9164e9ab4744d18f407a18feb32481e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323170"
---
# <a name="migrate-large-databases-to-azure-database-for-mysql-using-mydumpermyloader"></a>mydumper または myloader を使用して大規模なデータベースを Azure Database for MySQL に移行する

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL は、高可用な MySQL データベースをクラウドで実行し、管理し、スケーリングするために使用されるマネージド サービスです。 1 TB を超える MySQL データベースを Azure Database for MySQL に移行する際は、次の利点を持つ [mydumper や myloader](https://centminmod.com/mydumper.html) などのコミュニティ ツールを使用することを検討してください。

* 並列処理により、移行時間が短縮されます。
* 高価な文字セットの変換ルーチンを回避することで、パフォーマンスが向上します。
* テーブルやメタデータなどのファイルの出力形式が異なるため、データの表示や解析が容易になります。 すべてのスレッドでスナップショットが維持されるため、整合性が保たれます。
* プライマリとレプリカ ログの位置が正確になります。
* データベースやテーブルの包含または除外を指定する PERL 互換正規表現 (PCRE) をサポートしているため、管理が容易です。
* スキーマとデータが一緒になります。 他の論理移行ツールのように、これらを別々に扱う必要はありません。

このクイックスタートでは、mydumper や myloader を使用して MySQL データベースをインストール、バックアップ、および復元する方法について説明します。

## <a name="prerequisites"></a>前提条件

MySQL データベースの移行を開始する前に、次のことを行う必要があります。

1. [Azure portal](./flexible-server/quickstart-create-server-portal.md) を使用して、Azure Database for MySQL サーバーを作成する。

2. [Azure portal](../virtual-machines/linux/quick-create-portal.md) を使用して、Linux を実行する Azure VM を作成します (Ubuntu を推奨)。
    > [!Note]
    > ツールをインストールする前に、次の点を考慮してください。
    >
    > * ソースがオンプレミスにあり、Azure に (ExpressRoute を使用して) 高帯域で接続されている場合、ツールを Azure VM にインストールすることを検討してください。<br> 
    > * ソースとターゲットの間の帯域幅に問題がある場合は、ソースの近くに mydumper をインストールし、ターゲット サーバーの近くに myloader をインストールします。 ツール **[Azcopy](../storage/common/storage-use-azcopy-v10.md)** を使用して、オンプレミスまたはその他のクラウド ソリューションから Azure にデータを移動することができます。

3. 次の手順を実行して、mysql クライアントをインストールします。

4. 

    * 次のコマンドを実行して、Linux を実行している Azure VM のパッケージ インデックスを更新します。
        ```bash
        $ sudo apt update
        ```
    * 次のコマンドを実行して、mysql クライアント パッケージをインストールします。
        ```bash
        $ sudo apt install mysql-client
        ```

## <a name="install-mydumpermyloader"></a>mydumper や myloader をインストールする

mydumper や myloader をインストールするには、次の手順を実行します。

1. OS のディストリビューションに応じて、次のコマンドを実行して、mydumper や myloader に適したパッケージをダウンロードします。
2. 
    ```bash
    $ wget https://github.com/maxbube/mydumper/releases/download/v0.10.1/mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Note]
    > $(lsb_release -cs) は、ご自身のディストリビューションを特定するのに役立ちます。

3. mydumper の .deb パッケージをインストールするには、次のコマンドを実行します。

    ```bash
    $ dpkg -i mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Tip]
    > お使いの Linux ディストリビューションによってインストーラは異なるため、パッケージをインストールする際に使用するコマンドも異なります。 mydumper や myloader は、Fedora、RedHat、Ubuntu、Debian、CentOS、openSUSE、MacOSX のディストリビューションで使用できます。 詳細については、 **[mydumper のインストール方法](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)** に関するページを参照してください

## <a name="create-a-backup-using-mydumper"></a>mydumper を使用してバックアップを作成する

* mydumper を使用してバックアップを作成するには、次のコマンドを実行します。

    ```bash
    $ mydumper --host=<servername> --user=<username> --password=<Password> --outputdir=./backup --rows=100000 --compress --build-empty-files --threads=16 --compress-protocol --trx-consistency-only --ssl  --regex '^(<Db_name>\.)' -L mydumper-logs.txt
    ```

このコマンドでは、次の変数を使用します。

* **--host:** 接続先のホスト
* **--user:** 必要なアクセス許可を持つユーザー名 
* **--password:** ユーザー パスワード
* **--rows:** テーブルをこの行数のチャンクに分割します
* **--outputdir:** 出力ファイルのダンプ先ディレクトリ
* **--regex:** データベースの照合に使用する正規表現。
* **--trx-consistency-only:** トランザクションの整合性のみ
* **--threads:** 使用するスレッドの数。規定値は 4 です。 コンピューターの仮想コアの 2 倍の値を使用することを推奨します。

    >[!Note] 
    >mydumper で使用できるその他のオプションの詳細については、**mydumper --help** を実行してください。 詳細については [mydumper や myloader のドキュメント](https://centminmod.com/mydumper.html)を参照してください<br>
    複数のデータベースを並列でダンプするには、次の例に示すように正規表現変数を変更します: **regex ’^(DbName1\.|DbName2\.)**

## <a name="restore-your-database-using-myloader"></a>myloader を使用してデータベースを復元する

* mydumper を使用してバックアップしたデータベースを復元するには、次のコマンドを実行します。

    ```bash
    $ myloader --host=<servername> --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=500 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

このコマンドでは、次の変数を使用します。

* **--host:** 接続先のホスト
* **--user:** 必要なアクセス許可を持つユーザー名 
* **--password:** ユーザー パスワード
* **--directory:** バックアップが保存されている場所。 
* **--queries-per-transaction:** 500 以下の値に設定することをお勧めします
* **--threads:** 使用するスレッドの数。規定値は 4 です。 コンピューターの仮想コアの 2 倍の値を使用することを推奨します

> [!Tip]
> myloader で使用できるその他のオプションの詳細については、**myloader --help** を実行してください

データベースを復元した後は、ソース データベースとターゲット データベースの間のデータの整合性を検証することをお勧めします。

> [!Note]
> mydumper や myloader ツールに関する問題やフィードバックは、 **[こちら](https://github.com/maxbube/mydumper/issues)** から送信してください。

## <a name="next-steps"></a>次のステップ

* [GitHub で mydumper や myloader プロジェクト](https://github.com/maxbube/mydumper)の詳細を確認する。
* [大規模な MySQL データベースを移行する方法](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)について確認します。
* [チュートリアル: Azure Database for MySQL のダウンタイムを最小限に抑えた移行 - 単一サーバーから Azure Database for MySQL への移行 - フレキシブル サーバー](howto-migrate-single-flexible-minimum-downtime.md)
* データイン レプリケーションの詳細については、「[データを Azure Database for MySQL フレキシブル サーバーにレプリケートする](flexible-server/concepts-data-in-replication.md)」および [Azure Database for MySQL フレキシブル サーバーのデータイン レプリケーションを構成する](./flexible-server/how-to-data-in-replication.md)方法に関する記事を参照してください
* よく発生する[移行エラー](./howto-troubleshoot-common-errors.md)