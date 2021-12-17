---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールのトラブルシューティング | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの使い方に関するトラブルシューティング情報を提供します。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/17/2021
ms.author: phjensen
ms.openlocfilehash: f44ad9257f6fbc6b8ca232964f9bee29cea57384
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256491"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool"></a>Azure アプリケーション整合性スナップショット ツールのトラブルシューティング

この記事では、Azure NetApp Files および Azure Large Instance で使用できる Azure アプリケーション整合性スナップショット ツールの使い方に関するトラブルシューティング情報を提供します。

コマンドの実行中に発生するおそれがある一般的な問題を以下に示します。 説明されている解決手順に従って、問題を解決してください。 問題が解決しない場合は、Azure portal からサービス要求を開き、Microsoft サポートが対応できるように SAP HANA Large Instance キューに要求を割り当てます。

## <a name="log-files"></a>ログ ファイル

AzAcSnap を使用してエラーをデバッグする上で最適な情報源の 1 つは、ログ ファイルです。  

### <a name="log-file-location"></a>ログ ファイルの保存先

ログ ファイルは、AzAcSnap 構成ファイル内で `logPath` パラメーターごとに構成されたディレクトリに格納されます。  既定の構成ファイル名は `azacsnap.json` であり、`logPath` の既定値は `"./logs"` です。つまり、`azacsnap` コマンドの実行場所を基準とした `./logs` ディレクトリにログ ファイルは書き込まれます。  `logPath` を絶対的な場所 (`/home/azacsnap/logs` など) とすると、`azacsnap` コマンドが実行された場所に関係なく、ログは `azacsnap` によって `/home/azacsnap/logs` に確実に出力されます。

### <a name="log-file-naming"></a>ログ ファイルの名前付け

ログ ファイル名は、アプリケーション名 (`azacsnap` など)、使用されているコマンド オプション (`-c`) (`backup`、`test`、`details` など)、構成ファイル名 (既定値の `azacsnap.json` など) に基づいて作成されます。  したがって、`-c backup` コマンドを使用する場合、ログ ファイル名は既定で `azacsnap-backup-azacsnap.log` となり、`logPath` によって構成されたディレクトリに書き込まれます。  

この名前付け規則は、複数の構成ファイル (データベースごとに 1 つずつ) に対応するため、および関連付けられたログ ファイルを容易に確実に見つけられるようにするために確立されました。  そのため、構成ファイル名が `SID.json` である場合、`azacsnap -c backup --configfile SID.json` オプションを使用したときの結果ファイル名は `azacsnap-backup-SID.log` になります。

### <a name="result-file-and-syslog"></a>結果ファイルと syslog

`-c backup` コマンド オプションを指定すると、AzAcSnap では、`logger` コマンドを使用して `*.result` ファイルおよびシステム ログ (`/var/log/messages`) への書き込みが行われます。  `*.result` ファイル名は、[ログ ファイル](#log-file-naming)と同じベース名を含み、[ログ ファイルと同じ場所](#log-file-location)に移動されます。  これは、次の例に示すような 1 行から成るシンプルな出力ファイルです。

`*.result` ファイルからの出力例。
```output
Database # 1 (PR1) : completed ok
```

`/var/log/messages` ファイルからの出力例。
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Azure NetApp Files との通信失敗

Azure NetApp Files との通信を検証するときに、通信が失敗するか、タイムアウトになることがあります。 AzAcSnap を実行しているシステムから次のアドレスと TCP/IP ポートへのアウトバウンド トラフィックがファイアウォール ルールによってブロックされていないことを確認してください。

- (https://)management.azure.com:443
- (https://)login.microsoftonline.com:443 

### <a name="testing-communication-using-cloud-shell"></a>Cloud Shell を使用した通信のテスト

サービス プリンシパルが正しく構成されていることをテストするには、Azure portal から Cloud Shell を使用します。 これにより、VNet または仮想マシン内のネットワーク制御をバイパスする構成が正しいかどうかがテストされます。 

**解決方法:**

1. Azure portal で [Cloud Shell](../cloud-shell/overview.md) セッションを開きます。 
1. テスト ディレクトリを作成します (例: `mkdir azacsnap`)
1. azacsnap ディレクトリに cd し、最新バージョンの azacsnap ツールをダウンロードします。
    
    ```bash
    wget https://aka.ms/azacsnapinstaller
    ```
   
    ```output
    ----<snip>----
    HTTP request sent, awaiting response... 200 OK
    Length: 24402411 (23M) [application/octet-stream]
    Saving to: ‘azacsnapinstaller’

    azacsnapinstaller 100%[=================================================================================>] 23.27M 5.94MB/s in 5.3s

    2021-09-02 23:46:18 (4.40 MB/s) - ‘azacsnapinstaller’ saved [24402411/24402411]
    ```
    
1. インストーラを実行可能にします。 (例: `chmod +x azacsnapinstaller`)
1. テスト用のバイナリを抽出します。

    ```bash
    ./azacsnapinstaller -X -d .
    ```
    
    ```output
    +-----------------------------------------------------------+
    | Azure Application Consistent Snapshot Tool Installer |
    +-----------------------------------------------------------+
    |-> Installer version '5.0.2_Build_20210827.19086'
    |-> Extracting commands into ..
    |-> Cleaning up .NET extract dir
    ```

1. Cloud Shell のアップロードまたはダウンロード アイコンを使用して、テスト用のサービス プリンシパル ファイル (例: `azureauth.json`) と AzAcSnap 構成ファイル (例: `azacsnap.json`) をアップロードします
1. Azure Cloud Shell コンソールからストレージのテストを実行します。 

    > [!NOTE]
    > テスト コマンドが完了するまで約 90 秒かかる場合があります。

    ```bash
    ./azacsnap -c test --test storage
    ```

    ```output
    BEGIN : Test process started for 'storage'
    BEGIN : Storage test snapshots on 'data' volumes
    BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
    PASSED: Task#1/1 Storage test successful for Volume
    END : Storage tests complete
    END : Test process complete for 'storage'
    ```

## <a name="problems-with-sap-hana"></a>SAP HANA に関する問題

### <a name="running-the-test-command-fails"></a>テスト コマンドの実行が失敗する

`azacsnap -c test --test hana` でテストを実行して SAP HANA との通信を検証すると、次のエラーが表示されます。

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**解決方法:**

1. 各 HANA インスタンスの構成ファイル (`azacsnap.json`など) を確認し、SAP HANA データベースの値が確実に正しくなるようにします。
1. 次のコマンドを実行することで、`hdbsql` コマンドがパス内に存在し、SAP HANA サーバーに接続できるかどうかを確認してみてください。 次の例に、コマンドの正しい実行およびその出力を示します。

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    次の例では、ユーザーの `$PATH` 内に `hdbsql` コマンドがありません。

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    次の例では、`hdbsql` コマンドがユーザーの `$PATH` に一時的に追加されています。しかし、実行時に、`hdbuserstore Set` コマンドで接続キーが正しく設定されていないことが示されています (詳細については、ファースト ステップ ガイドを参照してください)。

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (AZACSNAP)
    ```

    > [!NOTE]
    > ユーザーの `$PATH` に永続的に追加するには、ユーザーの `$HOME/.profile` ファイルを更新します。

### <a name="insufficient-privilege"></a>十分な権限がない

`azacsnap` の実行時に `* 258: insufficient privilege` などのエラーが発生する場合は、適切な権限が "AZACSNAP" データベース ユーザーに割り当てられていることを確認します (このユーザーは[インストール ガイド](azacsnap-installation.md#enable-communication-with-database)に従って作成されたユーザーであることを前提としています)。  次のコマンドを使用して、ユーザーの現在の権限を確認します。

```bash
hdbsql -U AZACSNAP "select GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE from sys.granted_privileges "' | grep -i -e GRANTEE -e azacsnap
```

```output
GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE
"AZACSNAP","USER","BACKUP ADMIN","TRUE","FALSE"
"AZACSNAP","USER","CATALOG READ","TRUE","FALSE"
"AZACSNAP","USER","CREATE ANY","TRUE","TRUE"
```

エラーによって、必要な SAP HANA 権限を判断するのに役立つ詳細情報が提供される場合もあります (`Detailed info for this error can be found with guid '99X9999X99X9999X99X99XX999XXX999' SQLSTATE: HY000` の出力など)。  この場合は、[SAP ヘルプ ポータルの GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/9a73c4c017744288b8d6f3b9bc0db043.html) に関するページの SAP の手順に従ってください。ここでは、次の SQL クエリを使用して、必要な権限の詳細を確認することが推奨されています。

```sql
CALL SYS.GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS ('99X9999X99X9999X99X99XX999XXX999', ?)
```

```output
GUID,CREATE_TIME,CONNECTION_ID,SESSION_USER_NAME,CHECKED_USER_NAME,PRIVILEGE,IS_MISSING_ANALYTIC_PRIVILEGE,IS_MISSING_GRANT_OPTION,DATABASE_NAME,SCHEMA_NAME,OBJECT_NAME,OBJECT_TYPE
"99X9999X99X9999X99X99XX999XXX999","2021-01-01 01:00:00.180000000",120212,"AZACSNAP","AZACSNAP","DATABASE ADMIN or DATABASE BACKUP ADMIN","FALSE","FALSE","","","",""
```

上の例では、"DATABASE BACKUP ADMIN" 権限を SYSTEMDB の AZACSNAP ユーザーに追加することで、権限の不足エラーが解決されます。

### <a name="the-hdbuserstore-location"></a>`hdbuserstore` の場所

SAP HANA との通信を設定するときは、セキュリティで保護された通信設定を作成するために、`hdbuserstore` プログラムが使用されます。  `hdbuserstore` プログラムは、通常、`/usr/sap/<SID>/SYS/exe/hdb/` または `/usr/sap/hdbclient` の下にあります。  通常は、インストーラーによって `azacsnap` ユーザーの `$PATH` に正しい場所が追加されます。

## <a name="failed-test-with-storage"></a>ストレージに対するテストが失敗しました

コマンド `azacsnap -c test --test storage` が正常に完了しません。

### <a name="azure-large-instance"></a>Azure Large Instances

次の例は、Azure Large Instance 上の SAP HANA で `azacsnap` を実行したものです。

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**解決方法:** 上記のエラーは、通常、Azure Large Instance ストレージのユーザーが基になるストレージへのアクセス権を持っていない場合に表示されます。 指定されたストレージ ユーザーに関してストレージへのアクセス権を検証するには、`ssh` コマンドを実行してストレージ プラットフォームとの通信を検証します。

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

予想される出力の例を次に示します。

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>ホスト '172.18.18.11 (172.18.18.11)' の信頼性を確立できません

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**解決方法:** [はい] は選択しないでください。 ストレージ IP アドレスが確実に正しくなるようにしてください。 問題が解決しない場合は、Microsoft 運用チームと協力してストレージ IP アドレスをご確認ください。

### <a name="azure-netapp-files"></a>Azure NetApp Files

次の例は、Azure NetApp Files を使用して VM 上で `azacsnap` を実行したものです。

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**解決方法:**

1. `azacsnap.json` 構成ファイルに設定されているようなサービス プリンシパル ファイル `azureauth.json` が存在するかどうかを確認します。
1. ログ ファイル (`logs/azacsnap-test-azacsnap.log` など) を調べて、サービス プリンシパル (`azureauth.json`) のコンテンツが正しいものであるかどうかを確認します。  ログからの例を次に示します。

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. ログ ファイル (`logs/azacsnap-test-azacsnap.log` など) を調べて、サービス プリンシパル (`azureauth.json`) の有効期限が切れているかどうかを確認します。 ログからの例を次に示します。

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>次のステップ

- [ヒント](azacsnap-tips.md)