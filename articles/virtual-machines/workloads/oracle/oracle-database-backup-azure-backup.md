---
title: Azure Backup を使用して Azure Linux VM で Oracle Database 19c データベースをバックアップおよび回復する
description: Azure Backup サービスを使用して Oracle Database 19c データベースをバックアップおよび回復する方法について説明します。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: fd6826370a0292190fc2534585fdb08f7982f447
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698700"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Azure Backup を使用して Azure Linux VM で Oracle Database 19c データベースをバックアップおよび回復する

**適用対象:** :heavy_check_mark: Linux VM 

この記事では、Azure Backup を使用して、データベース ファイルと高速回復領域が含まれている、VM ディスクのディスク スナップショットを作成する方法について説明します。 Azure Backup を使用すると、バックアップとして適した完全なディスク スナップショットを作成できます。これらは [Recovery Services コンテナー](../../../backup/backup-azure-recovery-services-vault-overview.md)に格納されます。  また、Azure Backup は、アプリケーション整合性バックアップを提供することで、追加の修正なしでデータを復元できるようにもします。 アプリケーション整合性データの復元により復元時間が短縮され、迅速に実行状態に戻ることができます。

> [!div class="checklist"]
>
> * アプリケーション整合性バックアップを使用してデータベースをバックアップする
> * 復旧ポイントからデータベースを復元および復旧する
> * 復旧ポイントから VM を復元する

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

バックアップと復旧プロセスを実行するには、最初にインストール済みの Oracle Database 19c のインスタンスがある Linux VM を作成する必要があります。 VM の作成に現在使用されている Marketplace イメージは、**Oracle:oracle-database-19-3:oracle-database-19-0904:latest** です。 このチュートリアルを完了するには、「[Oracle データベースのクイック作成ガイド](./oracle-database-quick-create.md)」の手順に従って Oracle データベースを作成してください。


## <a name="prepare-the-environment"></a>環境の準備

環境を準備するには、次の手順に従います。

1. [VM に接続します](#connect-to-the-vm)。
1. [Azure Files のストレージを設定します](#setup-azure-files-storage-for-the-oracle-archived-redo-log-files)
1. [データベースを準備します](#prepare-the-databases)。

### <a name="connect-to-the-vm"></a>VM に接続します

1. VM で Secure Shell (SSH) セッションを作成するには、次のコマンドを使用します。 IP アドレスとのホスト名の組み合わせを VM の `<publicIpAddress>` の値で置き換えます。
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. *ルート* ユーザーに切り替えます。

   ```bash
   sudo su -
   ```
    
1. oracle ユーザーを */etc/sudoers* ファイルに追加します。

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="setup-azure-files-storage-for-the-oracle-archived-redo-log-files"></a>Oracle のアーカイブされた再実行ログ ファイル用に Azure Files のストレージを設定する

Oracle データベースのアーカイブされた再実行ログファイルには、過去に取得したデータベースのスナップショットからロールフォワードするために必要なコミットされたトランザクションが格納されており、データベースの復旧において重要な役割を果たしています。 ARCHIVELOG モードの場合、オンラインの再実行ログファイルがいっぱいになって切り替わると、データベースによってその内容がアーカイブされます。 バックアップと合わせて、これはデータベースが失われたときにポイントインタイム リストアを実現するために必要となります。  
   
Oracle では、再実行ログファイルをさまざまな場所にアーカイブする機能を提供しています。業界のベスト プラクティスでは、これをホスト ストレージから分離し、独立したスナップショットで保護できるよう、保存先のうち少なくとも 1 つをリモート ストレージにすることが推奨されています。 Azure Files は、これらの要件に最適です。

Azure Files のファイル共有は、SMB や NFS (プレビュー) プロトコルを使用して、通常のファイル システム コンポーネントとして、Linux や Windows VM に接続できるストレージです。 
   
アーカイブ用のログ ストレージとして使用するために、SMB 3.0 プロトコル (推奨) を使用して Linux 上で Azure Files ファイル共有をセットアップするには、[Linux で Azure Files を使用する攻略ガイド](../../../storage/files/storage-how-to-use-files-linux.md)に従ってください。 設定が完了したら、このガイドに戻り、残りのすべての手順を完了します。

### <a name="prepare-the-databases"></a>データベースを準備する

この手順は、[Oracle データベースの作成に関するクイックスタート](./oracle-database-quick-create.md)に従っていること、`vmoracle19c` という名前の VM で実行されている `oratest1` という名前の Oracle インスタンスがあること、および標準の Oracle 構成ファイル "/etc/oratab" に依存する標準の Oracle "oraenv" スクリプトを使用してシェル セッションで環境変数を設定していることを前提としています。

VM 上の各データベースに対して次の手順を実行します。

1. ユーザーを *oracle* ユーザーに切り替えます。
 
   ```bash
    sudo su - oracle
    ```
    
1. 接続する前に、`oraenv` スクリプトを実行して環境変数 ORACLE_SID を設定する必要があります。これを実行すると、ORACLE_SID 名の入力を求められます。
    
    ```bash
    $ . oraenv
    ```

1.   Azure Files 共有を、追加のデータベース アーカイブ ログ ファイルの保存先として追加します
     
     この手順では、Azure ファイル共有を構成し、Linux VM に (たとえば `/backup` という名前のマウント ポイント ディレクトリの下に) マウントしていることを前提としています。

     VM にインストールされている各データベースについて、以下を例として使用して、データベース SID に基づく名前のサブディレクトリを作成します。
     
     この例では、マウント ポイント名は `/backup` で、SID は `oratest1` であるため、サブディレクトリ `/backup/oratest1` を作成し、所有権を oracle ユーザーに変更します。 マウント ポイント名とデータベース SID には、 **/backup/SID** を置き換えてください。 

     ```bash
     sudo mkdir /backup/oratest1
     sudo chown oracle:oinstall /backup/oratest1
     ```
    
1. データベースに接続します。
    
   ```bash
   sqlplus / as sysdba
   ```

1.  データベースがまだ実行されていない場合は、起動します。 
   
    ```bash
    SQL> startup
    ```
   
1. データベースの最初のアーカイブ ログの保存先を、前に作成したファイル共有ディレクトリに設定します。

   ```bash
   SQL> alter system set log_archive_dest_1='LOCATION=/backup/oratest1' scope=both;
   ```

1. データベースの回復ポイントの目標 (RPO) を定義します。

    一貫した RPO を実現するためには、オンラインの再実行ログファイルをアーカイブする頻度を考慮する必要があります。 アーカイブ ログの生成頻度は、以下によって制御されます。
    - オンラインの再実行ログファイルのサイズ。 オンライン ログファイルは満杯になると、切り替えられてアーカイブされます。 オンライン ログファイルのサイズが大きいほど、満杯になるまでの時間が長くなり、アーカイブの生成頻度が低下します。
    - ARCHIVE_LAG_TARGET パラメーターの設定では、現在のオンライン ログファイルを切り替えてアーカイブするまでに許容される最大秒数を制御します。 

    切り替えとアーカイブの頻度と、それに伴うチェックポイント操作を最小限に抑えるために、Oracle のオンラインの再実行ログファイルは一般的にかなり大きなサイズ (1,024 M、4,096 M、8,192 M など) になっています。 ビジー状態のデータベース環境では、数秒から数分ごとにログの切り替えとアーカイブが行われますが、あまり活発でないデータベースでは、最新のトランザクションがアーカイブされるまでに数時間から数日かかることもあり、アーカイブの頻度は劇的に低下します。 このため、一貫した RPO を実現するために、ARCHIVE_LAG_TARGET を設定することが推奨されます。 ARCHIVE_LAG_TARGET の設定値としては、データベースの復旧操作によって障害発生時から 5 分以内に復旧できるよう、5 分 (300 秒) が妥当です。

    ARCHIVE_LAG_TARGET を設定するには:

    ```bash 
    SQL> alter system set archive_lag_target=300 scope=both;
    ```

    RPO ゼロで Azure に可用性の高い Oracle データベースをデプロイする方法について理解を深めたい場合は、[Oracle Database のリファレンス アーキテクチャ](./oracle-reference-architecture.md)を参照してください。

1.  データベースが、オンライン バックアップを有効にするためのアーカイブ ログ モードになっていることを確認します。

     最初にログ アーカイブの状態を確認します。

     ```bash
     SQL> SELECT log_mode FROM v$database;

     LOG_MODE
     ------------
     NOARCHIVELOG
     ```

     NOARCHIVELOG モードになっている場合は、次のコマンドを実行します。

     ```bash
     SQL> SHUTDOWN IMMEDIATE;
     SQL> STARTUP MOUNT;
     SQL> ALTER DATABASE ARCHIVELOG;
     SQL> ALTER DATABASE OPEN;
     SQL> ALTER SYSTEM SWITCH LOGFILE;
     ```
1. テーブルを作成して、バックアップおよび復元操作をテストします。
   ```bash
   SQL> create user scott identified by tiger quota 100M on users;
   SQL> grant create session, create table to scott;
   SQL> connect scott/tiger
   SQL> create table scott_table(col1 number, col2 varchar2(50));
   SQL> insert into scott_table VALUES(1,'Line 1');
   SQL> commit;
   SQL> quit
   ```
## <a name="using-azure-backup"></a>Azure Backup の使用 

Azure Backup サービスは、データをバックアップし、それを Microsoft Azure クラウドから回復するための、シンプルで安全かつコスト効率の高いソリューションを提供します。 Azure Backup では、元のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 バックアップは、復旧ポイントの管理機能をビルトインで備えた Recovery Services コンテナーに格納されます。 構成とスケーラビリティは単純で、バックアップは最適化され、必要に応じて簡単に復元することができます。

Azure Backup サービスでは、Oracle や MySQL などのさまざまなアプリケーションの Windows および Linux VM のバックアップ中にアプリケーションの整合性を確保するための[フレームワーク](../../../backup/backup-azure-linux-app-consistent.md)が提供されます。 これには、ディスクのスナップショットを作成する前に (アプリケーションを停止するための) 事前スクリプトを呼び出すことと、スナップショットの完了後に (アプリケーションを停止解除するための) 事後スクリプトを呼び出すことが含まれます。 

このフレームワークが拡張され、Oracle のような一部のアプリケーション用にパッケージ化された事前スクリプトと事後スクリプトが Azure Backup サービスによって提供され、Linux イメージに事前に読み込まれるため、何もインストールする必要はありません。 Azure Backup ユーザーはアプリケーションに名前を付けるだけで済み、その後は Azure VM バックアップによって関連する事前および事後スクリプトが自動的に呼び出されます。 パッケージ化された事前スクリプトと事後スクリプトは Azure Backup チームによって管理されるので、ユーザーにはこれらのスクリプトのサポート、所有権、および有効性が保証されています。 現在のところ、拡張フレームワークでサポートされているアプリケーションは *Oracle* と *MySQL* です。

> [!Note]
> 拡張フレームワークにより、バックアップが実行されるごとに、VM にインストールされているすべての Oracle データベースで事前スクリプトと事後スクリプトが実行されます。 
>
> **workload.conf** ファイル内のパラメーター `configuration_path` は、Oracle /etc/oratab ファイル (または oratab 構文に従うユーザー定義ファイル) の場所を示します。 詳細については、「[アプリケーション整合性バックアップを設定する](#set-up-application-consistent-backups)」を参照してください。
> 
> Azure Backup は、# (コメントとして扱われます) または +ASM (Oracle Automatic Storage Management インスタンス) で始まる行を除き、configuration_path が指すファイルにリストされている各データベースに対してバックアップ前後のスクリプトを実行します。
> 
> Azure Backup 拡張フレームワークにより、ARCHIVELOG モードで動作する Oracle データベースのオンライン バックアップが作成されます。 事前および事後のスクリプトでは、ALTER DATABASE BEGIN/END BACKUP コマンドを使用して、アプリケーションの整合性が実現されます。 
>
> データベース バックアップの整合性を保つには、スナップショットを開始する前に NOARCHIVELOG モードのデータベースをクリーンにシャットダウンする必要があります。


このセクションでは、Azure Backup フレームワークを使用して、実行中の VM と Oracle データベースのアプリケーション整合性スナップショットを作成します。 このデータベースはバックアップ モードになるため、Azure Backup で VM ディスクのスナップショットを作成している間、トランザクション全体で一貫性のあるオンライン バックアップを実行できます。 スナップショットはストレージの完全なコピーであり、増分または書き込み時コピーのスナップショットではないため、データベースの復元先として有効なメディアです。 Azure Backup アプリケーション整合性スナップショットを使用する利点は、データベースのサイズに関係なく非常に高速であり、スナップショットが Recovery Services コンテナーに転送されるのを待たずに、作成後すぐに復元操作に使用できることです。

Azure Backup を使用してデータベースをバックアップするには、次の手順を実行します。

1. [アプリケーション整合性バックアップ用に環境を準備します](#prepare-the-environment-for-an-application-consistent-backup)。
1. [アプリケーション整合性バックアップを設定します](#set-up-application-consistent-backups)。
1. [VM のアプリケーション整合性バックアップをトリガーします](#trigger-an-application-consistent-backup-of-the-vm)。

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>アプリケーション整合性バックアップ用に環境を準備する

> [!Note] 
> Oracle データベースでは、最小限の特権を使用して職務を分離するために、ジョブの役割の分離が採用されています。 これは、個別のオペレーティング システム グループを個別のデータベース管理者ロールに関連付けることで実現されます。 その後、オペレーティング システム ユーザーには、オペレーティング システム グループのメンバーシップに応じて、異なるデータベース特権を付与できます。 
>
> `SYSBACKUP` データベース ロール (汎用名は OSBACKUPDBA) は、データベースでバックアップ操作を実行するための限定的な特権を提供するために使用され、Azure Backup で必要とされます。
>
> Oracle のインストール時に、SYSBACKUP ロールと関連付ける推奨のオペレーティング システム グループ名は `backupdba` ですが、任意の名前を使用することができるので、最初に ORACLE SYSBACKUP ロールを表すオペレーティング システム グループの名前を決定する必要があります。

1. *oracle* ユーザーに切り替えます。
   ```bash
   sudo su - oracle
   ```

1. Oracle 環境を設定します。
   ```bash
   export ORACLE_SID=oratest1
   export ORAENV_ASK=NO
   . oraenv
   ```

1. Oracle SYSBACKUP ロールを表すオペレーティング システム グループの名前を決定します。
   ```bash
   grep "define SS_BKP" $ORACLE_HOME/rdbms/lib/config.c
   ```
   出力は次のようになります。 
   ```output
   #define SS_BKP_GRP "backupdba"
   ```

   出力の二重引用符で囲まれた値 (この例では `backupdba`) は、Oracle SYSBACKUP ロールが外部で認証されている Linux オペレーティング システム グループの名前です。 この値を書き留めておきます。

1. 次のコマンドを実行して、オペレーティング システム グループが存在することを確認します。 \<group name\> を、前のコマンドで返された値 (引用符を除く) に置き換えてください。
   ```bash
   grep <group name> /etc/group
   ```
   出力は次のようになります。この例では `backupdba` を使用しています。 
   ```output
   backupdba:x:54324:oracle
   ```

   > [!IMPORTANT] 
   > 出力が手順 3 で取得した Oracle オペレーティング システム グループの値と一致しない場合は、Oracle SYSBACKUP ロールを表すオペレーティング システム グループを作成する必要があります。 `<group name>` を、手順 3 で取得したグループ名に置き換えてください:
   >   ```bash
   >   sudo groupadd <group name>
   >   ```

1. 前の手順で検証または作成したオペレーティング システム グループに属する新しいバックアップ ユーザー `azbackup` を作成します。 \<group name\> を、検証したグループの名前に置き換えてください:

   ```bash
   sudo useradd -g <group name> azbackup
   ```

1. 新しいバックアップ ユーザーの外部認証を設定します。 

   バックアップ ユーザー `azbackup` は、外部認証を使用してデータベースにアクセスできる必要があるため、パスワードの入力が求められないようにする必要があります。 これを行うには、`azbackup` を介して外部で認証されるデータベース ユーザーを作成する必要があります。 データベースでは、検索する必要があるユーザー名にプレフィックスが使用されます。

   VM にインストールされている各データベースで、次の手順を実行します。
 
   sqlplus を使用してデータベースにログインし、外部認証の既定の設定を確認します。
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   出力は次の例のようになります。ここでは、データベース ユーザー名のプレフィックスとして `ops$` と表示されます。 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   `azbackup` ユーザーに対する外部認証用にデータベース ユーザー ***ops$azbackup*** を作成し、sysbackup 特権を付与します。
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > `ORA-46953: The password file is not in the 12.2 format.` ステートメントの実行時にエラー `GRANT` が発生した場合は、次の手順に従って orapwd ファイルを 12.2 形式に移行します。これは、VM 上のすべての Oracle データベースに対して実行する必要がある点に注意してください。
   >
   > 1. sqlplus を終了します。
   > 1. 古い形式のパスワード ファイルを新しい名前に変えます。
   > 1. パスワード ファイルを移行します。
   > 1. 古いファイルを削除します。
   > 1. 次のコマンドを実行します。
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapworatest1 $ORACLE_HOME/dbs/orapworatest1.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapworatest1 input_file=$ORACLE_HOME/dbs/orapworatest1.tmp
   >    rm $ORACLE_HOME/dbs/orapworatest1.tmp
   >    ```
   >
   > 1. sqlplus で `GRANT` 操作を再実行します。
   >
   
1. バックアップ メッセージをデータベース警告ログに記録するストアド プロシージャを作成します。

   VM にインストールされているそれぞれのデータベースに対し、以下を実行します。

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```

### <a name="set-up-application-consistent-backups"></a>アプリケーション整合性バックアップを設定する  

1. まず、ルート ユーザーに切り替えます。
   ```bash
   sudo su -
   ```

1. "/ etc/azure" フォルダーがあるかどうかを確認します。 存在しない場合は、アプリケーション整合性バックアップの作業ディレクトリを作成します。

   ```bash
   if [ ! -d "/etc/azure" ]; then
      mkdir /etc/azure
   fi
   ```

1. フォルダー内に "workload.conf" があるかどうかを確認します。 存在しない場合は、 */etc/azure* ディレクトリに、次の内容を含む *workload.conf* という名前のファイルを作成します。これは `[workload]` で始まる必要があります。 ファイルが既に存在する場合は、ちょうど次の内容に一致するようにフィールドを編集してください。 そうでない場合は、次のコマンドを実行すると、ファイルが作成され、内容が設定されます。

   ```bash
   echo "[workload]
   workload_name = oracle
   configuration_path = /etc/oratab
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

   > [!IMPORTANT]
   > workload.conf で使用される形式は次のとおりです。
   > * パラメーター **workload_name** は、データベースのワークロードの種類を決定するために Azure Backup によって使用されます。 この例では、oracle に設定することで、Azure Backup で Oracle データベースの正しい事前および事後の整合性コマンドを実行することができます。
   > * パラメーター **timeout** は、各データベースでストレージ スナップショットを完了するまでの最大時間を秒単位で示します。
   > * パラメーター **linux_user** は、データベースの休止操作を実行するために Azure Backup によって使用される、Linux ユーザー アカウントを示します。 このユーザー (`azbackup`) は、以前に作成しました。
   > * パラメーター **configuration_path** は、VM 上のテキスト ファイルの絶対パス名を示します。各行には、VM 上で実行されているデータベース インスタンスが一覧表示されます。 これは通常、データベースのインストール中に Oracle によって生成される `/etc/oratab` ファイルですが、次の形式規則に従っていれば、任意の名前を持つ任意のファイルを指定できます。
   >   * 各フィールドはコロン文字 (`:`) で区切られたテキスト ファイルである
   >   * 各行の最初のフィールドは、ORACLE_SID の名前である
   >   * 各行の 2 番目のフィールドが、その ORACLE_SID の ORACLE_HOME の絶対パス名である
   >   * これらの最初の 2 つのフィールドに続くすべてのテキストは無視されます
   >   * 行がポンドやハッシュ文字 (`#`) で始まる場合、行全体がコメントとして無視されます
   >   * 最初のフィールドに自動ストレージ管理インスタンスを示す値 (`+ASM`) がある場合、これは無視されます。 


### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>VM のアプリケーション整合性バックアップをトリガーする

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1.  Azure portal で、リソース グループ **rg-oracle** に移動し、仮想マシン **vmoracle19c** をクリックします。

1.  **[バックアップ]** ブレードで、リソース グループ **rg-oracle** に **myVault** という名前の新しい **Recovery Services コンテナー** を作成します。
    **[バックアップ ポリシーの選択]** では、 **[(新規) DailyPolicy]** を使用します。 バックアップの頻度または保持期間の範囲を変更する場合は、代わりに **[新しいポリシーの作成]** を選択します。

    ![Recovery Services コンテナーの追加ページ](./media/oracle-backup-recovery/recovery-service-01.png)

1.  続行するには、 **[バックアップの有効化]** をクリックします。

    > [!IMPORTANT]
    > **[バックアップの有効化]** をクリックしても、スケジュールされた時間に達するまでバックアップ プロセスは開始されません。 即時バックアップをセットアップするには、次の手順を完了します。

1. リソース グループ ページで、新しく作成した Recovery Services コンテナー **myVault** をクリックします。 ヒント:ページを更新して表示する必要がある場合があります。

1.  **myVault のバックアップ項目** ブレードの **[バックアップ項目数]** の下で、バックアップ項目の数を選択します。

    ![Recovery Services コンテナー myVault の詳細ページ](./media/oracle-backup-recovery/recovery-service-02.png)

1.  **バックアップ項目 (Azure 仮想マシン)** ブレードのページ右側で省略記号 (**...**) ボタンをクリックし、**[今すぐバックアップ]** をクリックします。

    ![Recovery Services コンテナーの [今すぐバックアップ] コマンド](./media/oracle-backup-recovery/recovery-service-03.png)

1.  [バックアップの保持期間] の既定値をそのまま使用し、 **[OK]** ボタンをクリックします。 バックアップ プロセスが完了するまで待ってから、 

    バックアップ ジョブの状態を表示するには、 **[バックアップ ジョブ]** をクリックします。

    ![Recovery Services コンテナーのジョブ ページ](./media/oracle-backup-recovery/recovery-service-04.png)

    次のイメージには、バックアップ ジョブの状態が表示されます。

    ![状態が表示された Recovery Services コンテナーのジョブ ページ](./media/oracle-backup-recovery/recovery-service-05.png)
    
    スナップショットの実行には数秒しかかかりませんが、コンテナーへの転送には時間がかかることがあります。また、転送が完了するまで、バックアップ ジョブは完了しません。

1. アプリケーション整合バックアップのため、ログ ファイル内のすべてのエラーに対処します。 ログ ファイルは /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log にあります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Recovery Services コンテナーを作成します。

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

1. VM のバックアップ保護を有効にします。

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

1. 既定のスケジュール (午前 5 時 (UTC)) でバックアップがトリガーされるのを待機するのではなく、今すぐ実行するようにバックアップをトリガーします。 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   `az backup job list` と `az backup job show` を使用して、バックアップ ジョブの進行状況を監視できます。

---

## <a name="recovery"></a>Recovery

データベースを復旧するには、これらの手順を実行します。

1. [データベース ファイルを削除します](#remove-the-database-files)。
1. [Recovery Services コンテナーから復元スクリプトを生成します](#generate-a-restore-script-from-the-recovery-services-vault)。
1. [復元ポイントをマウントします](#mount-the-restore-point)。
1. [復旧を実行します](#perform-recovery)。

### <a name="remove-the-database-files"></a>データベース ファイルを削除する 

回復プロセスのテスト方法については、この記事の後半で学習します。 回復プロセスをテストするには、先にデータベース ファイルを削除する必要があります。

1.  oracle ユーザーに切り替えます。
    ```bash
    su - oracle
    ```

1. Oracle インスタンスをシャットダウンします。

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

1.  障害をシミュレートするために、データベースのデータファイルと contolfiles を削除します。

    ```bash
    cd /u02/oradata/ORATEST1
    rm -f *.dbf *.ctl
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Recovery Services コンテナーから復元スクリプトを生成する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal で、Recovery Services コンテナー項目 *myVault* を検索して選択します。

    ![Recovery Services コンテナー myVault のバックアップ項目](./media/oracle-backup-recovery/recovery-service-06.png)

1. **[概要]** ブレードで **[バックアップ項目]** を選択し、 **[Azure 仮想マシン]** を選択します。これには、0 以外のバックアップ項目の数が表示されている必要があります。

    ![Recovery Services コンテナー Azure Virtual Machine のバックアップ項目の数](./media/oracle-backup-recovery/recovery-service-07.png)

1. [バックアップ項目 (Azure Virtual Machines)] ページに、VM **vmoracle19c** が表示されます。 右側にある省略記号をクリックしてメニューを表示し、 **[ファイルの回復]** を選択します。

    ![Recovery Services コンテナーファイル回復ページのスクリーンショット](./media/oracle-backup-recovery/recovery-service-08.png)

1. [**ファイルの回復 (プレビュー)**] ウィンドウで、**[スクリプトのダウンロード]** をクリックします。 次に、クライアント コンピューター上のフォルダーにダウンロードされた (.py) ファイルを保存します。 スクリプトを実行するためのパスワードが生成されます。 後で使用するために、パスワードをファイルにコピーします。 

    ![スクリプト ファイルのダウンロードの保存オプション](./media/oracle-backup-recovery/recovery-service-09.png)

1. .py ファイルを VM にコピーします。

    次の例では、secure copy (scp) コマンドを使用してファイルを VM に移動する方法を示しています。 コンテンツをクリップボードにコピーしてから、VM に設定されている新しいファイルにコンテンツを貼り付けることもできます。

    > [!IMPORTANT]
    > 次の例では、IP アドレスとフォルダーの値を確実に更新します。 値は、ファイルが保存されているフォルダーにマップする必要があります。
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

VM の復旧ポイントをリストする場合は、az backup recoverypoint list を使用します。 この例では、myVault という名前の Recovery Services コンテナーで保護されている vmoracle19c という名前の VM の最新の復旧ポイントを選択します。

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

VM に復旧ポイントを接続またはマウントするスクリプトを取得する場合は、az backup restore files mount-rp を使用します。 次の例では、myVault という名前の Recovery Services コンテナーで保護されている vmoracle19c という名前の VM のスクリプトを取得します。

myRecoveryPointName は、前述のコマンドで取得した復旧ポイントの名前に置き換えます。

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

次の例のように、スクリプトがダウンロードされ、パスワードが表示されます。

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

.py ファイルを VM にコピーします。

次の例では、secure copy (scp) コマンドを使用してファイルを VM に移動する方法を示しています。 コンテンツをクリップボードにコピーしてから、VM に設定されている新しいファイルにコンテンツを貼り付けることもできます。

> [!IMPORTANT]
> 次の例では、IP アドレスとフォルダーの値を確実に更新します。 値は、ファイルが保存されているフォルダーにマップする必要があります。
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>復元ポイントをマウントする

1. ルート ユーザーに切り替えます。
   ```bash
   sudo su -
   ``````
1. 復元マウント ポイントを作成し、それにスクリプトをコピーします。

    次の例では、スナップショットのマウント先の */restore* ディレクトリを作成し、ファイルをディレクトリに移動して、ルート ユーザーが所有し、実行可能ファイルになるように、ファイルを変更します。

    ```bash 
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    ここで、スクリプトを実行してバックアップを復元します。 Azure portal で生成されたパスワードを入力するように求められます。 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    次の例では、前のスクリプトを実行した後に表示されるものを示しています。 続行するかどうかをたずねるメッセージが表示されたら、**Y** を入力します。

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

1. マウントされたボリュームへのアクセスが確定されます。

    終了するには、**q** を入力し、マウントされたボリュームを検索します。 追加されたボリュームのリストを作成するには、コマンド プロンプトで、**df -h** と入力します。
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>回復の実行
VM 上の各データベースに対して次の手順を実行します。

1. 不足しているデータベース ファイルを元の場所に復元します。

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/oradata/ORATEST1
    cp * /u02/oradata/ORATEST1
    cd /u02/oradata/ORATEST1
    chown -R oracle:oinstall *
    ```
1. oracle ユーザーに切り替えます
   ```bash
   sudo su - oracle
   ```
1. データベース インスタンスを起動し、読み取り用に controlfile をマウントします。
   ```bash
   sqlplus / as sysdba
   SQL> startup mount
   SQL> quit
   ```

1. sysbackup を使用してデータベースに接続します:
   ```bash
   sqlplus / as sysbackup
   ```
1. データベースの自動復旧を開始します:

   ```bash
   SQL> recover automatic database until cancel using backup controlfile;
   ```
   > [!IMPORTANT]
   > 復元されたデータベース制御ファイルに記録されている Oracle システム変更番号 (SCN) で復元を停止しないよう RECOVER AUTOMATIC DATABASE コマンドに通知するため、USING BACKUP CONTROLFILE 構文を指定することが重要であることに注意してください。 復元されたデータベース制御ファイルは、データベースの他の部分と一緒にスナップショットされており、そこに記録されている SCN は、スナップショット時点のものです。 この時点以降に記録されたトランザクションがあるかもしれないため、データベースにコミットされた最後のトランザクションの時点に復旧する必要があります。

   復旧が正常に完了すると、`Media recovery complete` というメッセージが表示されます。 しかし、BACKUP CONTROLFILE 句を使用している場合、復旧コマンドではオンライン ログ ファイルが無視されるため、特定の時点への復旧の完了に必要な現在のオンラインの再実行ログに変更が加えられている可能性があります。 このような状況では、次のようなメッセージが表示される場合があります。

   ```output
   SQL> recover automatic database until cancel using backup controlfile;
   ORA-00279: change 2172930 generated at 04/08/2021 12:27:06 needed for thread 1
   ORA-00289: suggestion :
   /u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc
   ORA-00280: change 2172930 for thread 1 is in sequence #13
   ORA-00278: log file
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc' no
   longer needed for this recovery
   ORA-00308: cannot open archived log
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc'
   ORA-27037: unable to obtain file status
   Linux-x86_64 Error: 2: No such file or directory
   Additional information: 7

   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   ```
   
   > [!IMPORTANT]
   > 現在のオンラインの再実行ログファイルが失われたり破損していることで使用できない場合は、この時点で復旧を取り消すことができます。 

   これを修正するには、アーカイブされていない現在のオンライン ログを特定し、プロンプトで完全修飾ファイル名を指定します。


   新しい SSH 接続を開きます 
   ```bash
   ssh azureuser@<IP Address>
   ```
   oracle ユーザーに切り替えて Oracle SID を設定します
   ```bash
   sudo su - oracle
   export ORACLE_SID=oratest1
   ```
   
   データベースに接続し、次のクエリを実行してオンライン ログファイルを見つけます 
   ```bash
   sqlplus / as sysdba
   SQL> column member format a45
   SQL> set linesize 500  
   SQL> select l.SEQUENCE#, to_char(l.FIRST_CHANGE#,'999999999999999') as CHK_CHANGE, l.group#, l.archived, l.status, f.member
   from v$log l, v$logfile f
   where l.group# = f.group#;
   ```

   出力はこのようになります。 
   ```output
   SEQUENCE#  CHK_CHANGE           GROUP# ARC STATUS            MEMBER
   ---------- ---------------- ---------- --- ---------------- ---------------------------------------------
           13          2172929          1 NO  CURRENT          /u02/oradata/ORATEST1/redo01.log
           12          2151934          3 YES INACTIVE         /u02/oradata/ORATEST1/redo03.log
           11          2071784          2 YES INACTIVE         /u02/oradata/ORATEST1/redo02.log
   ```
   CURRENT と表示されているオンライン ログのログファイルのパスとファイル名をコピーします。この例では `/u02/oradata/ORATEST1/redo01.log` です。 復旧コマンドを実行している ssh セッションに戻り、ログファイルの情報を入力して return キーを押します。

   ```bash
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   ```

   ログファイルが適用され、復旧が完了したことが表示されます。 「CANCEL」と入力して復旧コマンドを終了します。
   ```output
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   Log applied.
   Media recovery complete.
   ```

1. データベースを開きます
   
   > [!IMPORTANT]
   > RECOVER コマンドで USING BACKUP CONTROLFILE オプションを使用する場合は、RESETLOGS オプションが必要です。 RESETLOGS は、再実行の履歴を最初にリセットすることで、データベースの新しいインカネーションを作成します。これは、以前のデータベースのインカネーションが、復旧でどれだけスキップされたかを判断する方法がないためです。

   ```bash
   SQL> alter database open resetlogs;
   ```

   
1. データベースの内容が完全に復旧されたことを確認します。

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

1. 復元ポイントのマウントを解除します。

   VM 上のすべてのデータベースが正常に復旧されたら、復元ポイントをマウント解除できます。 これは、VM 上で `unmount` コマンドを使用するか、Azure portal の [ファイルの回復] ブレードを使用して実行できます。 また、 **-clean** オプションを使用して python スクリプトを再度実行して、回復ボリュームのマウントを解除することもできます。

   VM で unmount を使用:
   ```bash
   sudo umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    ドライブをマウント解除するには、Azure Portal の **[ファイルの回復 (プレビュー)]** ブレードで **[ディスクのマウント解除]** をクリックします。

    ![[ディスクのマウント解除] コマンド](./media/oracle-backup-recovery/recovery-service-10.png)
    


## <a name="restore-the-entire-vm"></a>VM 全体の復元

削除されたファイルを Recovery Services コンテナーから復元する代わりに、VM 全体を復元することができます。

VM 全体を復元するには、次の手順を実行します。

1. [VM を停止して削除します](#stop-and-delete-the-vm)。
1. [VM を復旧します](#recover-the-vm)。
1. [パブリック IP アドレスを設定します](#set-the-public-ip-address)。
1. [データベース復旧を実行します](#perform-database-recovery)。

### <a name="stop-and-delete-the-vm"></a>VM を停止して削除する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal で、**vmoracle19c** 仮想マシンに移動して、 **[停止]** を選択します。

1. 仮想マシンが実行中ではなくなったら、 **[削除]** を選択し、 **[はい]** を選択します。

   ![コンテナー [削除] コマンド](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM を停止し、割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

1. VM を削除します。 確認を求められたら「y」と入力します。

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>VM を復旧する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal でステージング用のストレージ アカウントを作成します。

   1. Azure portal で、 **[+ リソースの作成]** を選択し、 **[ストレージ アカウント]** を検索して選択します。
    
      ![リソースの作成場所を示すスクリーンショット。](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. [ストレージ アカウントの作成] ページで、既存のリソース グループ **rg-oracle** を選択し、ストレージ アカウントに **oracrestore** という名前を付けて、[アカウントの種類] として **[ストレージ V2 (汎用 v2)]** を選択します。 [レプリケーション] を **[ローカル冗長ストレージ (LRS)]** に変更し、[パフォーマンス] を **[標準]** に設定します。 [場所] が、リソース グループ内の他のすべてのリソースと同じリージョンに設定されていることを確認します。 
    
      ![ストレージ アカウントの追加ページ](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. [確認および作成] をクリックして、 [作成] をクリックします。

1. Azure portal で、Recovery Services コンテナー項目 *myVault* を検索してクリックします。

    ![Recovery Services コンテナー myVault のバックアップ項目](./media/oracle-backup-recovery/recovery-service-06.png)
    
1.  **[概要]** ブレードで **[バックアップ項目]** を選択し、 **[Azure 仮想マシン]** を選択します。これには、0 以外のバックアップ項目の数が表示されている必要があります。

    ![Recovery Services コンテナー Azure Virtual Machine のバックアップ項目の数](./media/oracle-backup-recovery/recovery-service-07.png)

1.  [バックアップ項目 (Azure Virtual Machines)] ページに、VM **vmoracle19c** が表示されます。 VM 名をクリックします。

    ![VM の復元ページ](./media/oracle-backup-recovery/recover-vm-02.png)

1.  **[vmoracle19c]** ブレードで、整合性の種類が **[アプリケーション整合性]** の復元ポイントを選択し、右側にある省略記号 ( **...** ) をクリックしてメニューを表示します。  メニューから、 **[VM の復元]** をクリックします。

    ![[VM の復元] コマンド](./media/oracle-backup-recovery/recover-vm-03.png)

1.  **[仮想マシンの復元]** ブレードで、 **[新規作成]** および **[新しい仮想マシンの作成]** を選択します。 仮想マシン名「**vmoracle19c**」を入力し、VNet **vmoracle19cVNET** を選択します。このサブネットは、VNet の選択に基づいて自動的に設定されます。 VM の復元プロセスでは、同じリソース グループとリージョンの Azure ストレージ アカウントが必要です。 先ほど設定したストレージ アカウント **orarestore** を選択できます。

    ![構成値の復元](./media/oracle-backup-recovery/recover-vm-04.png)

1.  VM を復元するには、**[復元]** ボタンをクリックします。

1.  復元プロセスの状態を表示するには、**[ジョブ]** をクリックし、**[バックアップジョブ]** をクリックします。

    ![バックアップ ジョブの状態コマンド](./media/oracle-backup-recovery/recover-vm-05.png)

    **[進行中]** 復元操作をクリックして、復元プロセスの状態を表示します。

    ![復元プロセスの状態](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウントとファイル共有を設定するには、Azure CLI で次のコマンドを実行します。

1. VM と同じリソース グループと場所にストレージ アカウントを作成します。

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

1. 使用可能な復旧ポイントの一覧を取得します。 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

1. ストレージ アカウントに復旧ポイントを復元します。 `<myRecoveryPointName>` を、前の手順で生成された一覧の復旧ポイントに置き換えます。

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

1. 復元ジョブの詳細を取得します。 次のコマンドでは、トリガーされた復元ジョブの詳細を取得します。これには、テンプレート URI を取得するために必要な名前も含まれます。 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   出力はこの `(Note down the name of the restore job)` のようになります。

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

1. VM を再作成するために使用する URI の詳細を取得します。 前の手順の復元ジョブ名を `<RestoreJobName>` に置き換えます。

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   出力は次のようになります。

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   テンプレート BLOB の URI の末尾にあるテンプレートの名前 (この例では `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json`) と、BLOB コンテナーの名前 (`vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2`) が示されています。 

   次のコマンドでこれらの値を使用して、VM を作成するための準備として変数を割り当てます。 SAS キーは、30 分間の期間でストレージ コンテナーに対して生成されます。  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   ここで、テンプレートをデプロイして VM を作成します。

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   VM の名前の入力を求めるメッセージが表示されます。

---

### <a name="set-the-public-ip-address"></a>パブリック IP アドレスを設定する

VM が復元されたら、元の IP アドレスを新しい VM に再割り当てする必要があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1.  Azure portal で、仮想マシン **vmoracle19c** に移動します。 vmoracle19c-nic-XXXXXXXXXXXX のような新しいパブリック IP と NIC が割り当てられていますが、DNS アドレスはないことがわかります。 元の VM が削除されると、そのパブリック IP と NIC が保持され、次の手順で新しい VM に再アタッチされます。

    ![パブリック IP アドレスのリスト](./media/oracle-backup-recovery/create-ip-01.png)

1.  VM を停止する

    ![IP アドレスを作成する](./media/oracle-backup-recovery/create-ip-02.png)

1.  **[ネットワーク]** に移動します

    ![IP アドレスを関連付ける](./media/oracle-backup-recovery/create-ip-03.png)

1.  **[ネットワーク インターフェイスの接続]** をクリックし、元のパブリック IP アドレスがまだ関連付けられている元の NIC の **vmoracle19cVMNic を選択し、 **[OK]** をクリックします

    ![リソースの種類と NIC の値を選択する](./media/oracle-backup-recovery/create-ip-04.png)

1.  次に、VM 復元操作で作成された NIC はプライマリ インターフェイスとして構成されているため、デタッチする必要があります。 **[ネットワーク インターフェイスのデタッチ]** をクリックし、**vmoracle19c-nic-XXXXXXXXXXXX** のような新しい NIC を選択し、 **[OK]** をクリックします

    ![[ネットワーク インターフェイスのデタッチ] を選択する場所を示すスクリーンショット。](./media/oracle-backup-recovery/create-ip-05.png)
    
    再作成した VM には、元の IP アドレスとネットワーク セキュリティ グループの規則に関連付けられている元の NIC が使用されるようになります
    
    ![IP アドレスの値](./media/oracle-backup-recovery/create-ip-06.png)
    
1.  **[概要]** に戻り、 **[開始]** をクリックします 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM を停止し、割り当てを解除します。

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

1. 現在の、復元によって生成された VM NIC を一覧表示します

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   出力は次のようになります。復元によって生成された NIC 名が `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf` として表示されます

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

1. `<VMName>VMNic` の名前 (この場合は `vmoracle19cVMNic`) を持つ元の NIC をアタッチします。 元のパブリック IP アドレスはこの NIC にまだアタッチされており、NIC が再アタッチされると VM に復元されます。 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

1. 復元によって生成された NIC をデタッチします

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```
1. VM を起動します。

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="perform-database-recovery"></a>データベース復旧を実行する
最初に、VM に再接続します。

```azurecli
ssh azureuser@<publicIpAddress>
```

VM 全体が復元されたら、それぞれに対して次の手順を実行して、VM 上の各データベースを復旧することが重要です。

1. 自動開始で VM の起動時にデータベースの起動が試みられたために、インスタンスが実行されていることがあります。 ただし、データベースには復旧が必要であり、マウント ステージでのみ発生する可能性があるため、最初に準備のシャットダウンが実行され、次にマウント ステージが開始されます。

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    ```
    
1. データベース復旧を実行する
   > [!IMPORTANT]
   > 復元されたデータベース制御ファイルに記録されている Oracle システム変更番号 (SCN) で復元を停止しないよう RECOVER AUTOMATIC DATABASE コマンドに通知するため、USING BACKUP CONTROLFILE 構文を指定することが重要であることに注意してください。 復元されたデータベース制御ファイルは、データベースの他の部分と一緒にスナップショットされており、そこに記録されている SCN は、スナップショット時点のものです。 この時点以降に記録されたトランザクションがあるかもしれないため、データベースにコミットされた最後のトランザクションの時点に復旧する必要があります。
    
    ```bash
    SQL> recover automatic database using backup controlfile until cancel;
    ```
   最後に使用可能なアーカイブ ログ ファイルが適用されたら、「`CANCEL`」と入力して復旧を終了します。

1. データベースを開きます
   > [!IMPORTANT]
   > RECOVER コマンドで USING BACKUP CONTROLFILE オプションを使用する場合は、RESETLOGS オプションが必要です。 RESETLOGS は、再実行の履歴を最初にリセットすることで、データベースの新しいインカネーションを作成します。これは、以前のデータベースのインカネーションが、復旧でどれだけスキップされたかを判断する方法がないためです。
   
    ```bash 
    SQL> alter database open resetlogs;
    ```
   

1. データベースの内容が復旧されたことを確認します。

    ```bash
    SQL> select * from scott.scott_table;
    ```

これで、Azure Linux VM での Oracle Database 19c のデータベースのバックアップと復旧が完了しました。

Oracle コマンドと概念の詳細については、Oracle のドキュメント (以下を含む) を参照してください。

   * [データベース全体の Oracle ユーザー管理のバックアップの実行](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-database-backups.html#GUID-65C5E03A-E906-47EB-92AF-6DC273DBD0A8)
   * [ユーザー管理データベースの完全復旧の実行](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-66D07694-533F-4E3A-BA83-DD461B68DB56)
   * [Oracle STARTUP コマンド](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/STARTUP.html#GUID-275013B7-CAE2-4619-9A0F-40DB71B61FE8)
   * [Oracle RECOVER コマンド](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-54B59888-8683-4CD9-B144-B0BB68887572)
   * [Oracle ALTER DATABASE コマンド](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-DATABASE.html#GUID-8069872F-E680-4511-ADD8-A4E30AF67986)
   * [Oracle LOG_ARCHIVE_DEST_n パラメーター](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/LOG_ARCHIVE_DEST_n.html#GUID-10BD97BF-6295-4E85-A1A3-854E15E05A44)
   * [Oracle ARCHIVE_LAG_TARGET パラメーター](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ARCHIVE_LAG_TARGET.html#GUID-405D335F-5549-4E02-AFB9-434A24465F0B)


## <a name="delete-the-vm"></a>VM の削除

VM が必要なくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

1. コンテナー内のバックアップの論理的な削除を無効にする

    ```azurecli
    az backup vault backup-properties set --name myVault --resource-group rg-oracle --soft-delete-feature-state disable
    ```

1. VM の保護を停止し、バックアップを削除する

    ```azurecli
    az backup protection disable --resource-group rg-oracle --vault-name myVault --container-name vmoracle19c --item-name vmoracle19c --delete-backup-data true --yes
    ```

1. すべてのリソースを含むリソース グループを削除する

    ```azurecli
    az group delete --name rg-oracle
    ```

## <a name="next-steps"></a>次のステップ

[チュートリアル:高可用性 VM の作成](../../linux/create-cli-complete.md)

[VM デプロイ Azure CLI サンプルを探索する](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
