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
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 7ba5a05515284612e17d5aba4cc673c7e78f7ba1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869924"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールのトラブルシューティング (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの使い方に関するトラブルシューティング情報を提供します。

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

## <a name="failed-communication-with-sap-hana"></a>SAP HANA との通信に失敗しました

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
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
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
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > ユーザーの `$PATH` に永続的に追加するには、ユーザーの `$HOME/.profile` ファイルを更新します。

## <a name="the-hdbuserstore-location"></a>`hdbuserstore` の場所

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
