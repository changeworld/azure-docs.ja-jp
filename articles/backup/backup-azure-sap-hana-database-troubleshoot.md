---
title: SAP HANA データベースのバックアップ エラーのトラブルシューティング
description: Azure Backup を使用して SAP HANA データベースをバックアップするときに発生する可能性のある一般的なエラーをトラブルシューティングする方法について説明します。
ms.topic: troubleshooting
ms.date: 05/31/2021
ms.openlocfilehash: 1af52116a08344d36a9364ac9e3b4f468e0abb83
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451963"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure での SAP HANA データベースのバックアップをトラブルシューティングする

この記事では、Azure 仮想マシン上で SAP HANA データベースをバックアップするためのトラブルシューティング情報を提供します。 現在サポートされている SAP HANA バックアップ シナリオの詳細については、「[シナリオのサポート](sap-hana-backup-support-matrix.md#scenario-support)」を参照してください。

## <a name="prerequisites-and-permissions"></a>前提条件とアクセス許可

バックアップを構成する前に、「[前提条件](tutorial-backup-sap-hana-db.md#prerequisites)」セクションと[事前登録スクリプトの内容](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)に関するセクションを参照してください。

## <a name="common-user-errors"></a>一般的なユーザー エラー

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **エラー メッセージ**      | `Azure Backup does not have required role  privileges to carry out Backup and Restore operations`    |
| ---------------------- | ------------------------------------------------------------ |
| **考えられる原因**    | バックアップ ユーザー (AZUREWLBACKUPHANAUSER) に **SAP_INTERNAL_HANA_SUPPORT** ロールが割り当てられていないか、ロールが上書きされている場合は、すべての操作が失敗し、このエラーが表示されます。                          |
| **推奨される操作** | [事前登録スクリプト](https://aka.ms/scriptforpermsonhana)をダウンロードして SAP HANA インスタンスで実行するか、**SAP_INTERNAL_HANA_SUPPORT** ロールをバックアップ ユーザー (AZUREWLBACKUPHANAUSER) に手動で割り当てます。<br><br>**注:**<br><br>HANA 2.0 SPS04 Rev 46 以降を使用している場合、これらの HANA バージョンでは **SAP_INTERNAL_HANA_SUPPORT** ロールの使用が非推奨であるため、このエラーは発生しません。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| **エラー メッセージ**      | `Failed to connect to HANA system`                        |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | <ul><li>HANA インスタンスへの接続に失敗しました</li><li>システム DB がオフラインです</li><li>テナント DB がオフラインです</li><li>バックアップ ユーザー (AZUREWLBACKUPHANAUSER) に十分なアクセス許可または特権がありません。</li></ul> |
| **推奨される操作** | システムが稼働しているかどうかを確認します。 データベースが稼働している場合は、SAP HANA インスタンスで[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)をダウンロードして実行することにより、必要な権限が設定されていることを確認します。 |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| **エラー メッセージ**      | `The specified SAP HANA instance is either invalid or can't be found`  |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | <ul><li>指定した SAP HANA インスタンスは無効であるか、見つかりません。</li><li>単一の Azure VM 上の複数の SAP HANA インスタンスをバックアップすることはできません。</li></ul> |
| **推奨される操作** | <ul><li>Azure VM 上で実行されている HANA インスタンスが 1 つだけであることを確認します。</li><li>正しい SAP HANA インスタンスを指定して、[Discover DB]\(DB の検出\) ウィンドウのスクリプト ([こちらから](https://aka.ms/scriptforpermsonhana)見つけることもできます) を実行して問題を解決します。</li></ul> |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| **エラー メッセージ**      | `Backup log chain is broken`                                    |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | HANA LSN ログ チェーンの中断は、以下を含むさまざまな理由でトリガーされる可能性があります。<ul><li>バックアップをコミットする Azure Storage 呼び出しに失敗しました。</li><li>テナント DB がオフラインです。</li><li>拡張機能のアップグレードにより、進行中のバックアップ ジョブが終了しました。</li><li>バックアップ中に Azure Storage に接続できません。</li><li>SAP HANA によってバックアップ プロセスでトランザクションがロールバックされました。</li><li>バックアップは完了しましたが、HANA システムでカタログがまだ正常に更新されていません。</li><li>Azure Backup の観点でバックアップに失敗しましたが、HANA の観点からは成功しました。ログ バックアップまたはカタログの配置先が backint からファイル システムに更新されたか、backint 実行可能ファイルが変更された可能性があります。</li></ul> |
| **推奨される操作** | この問題を解決するために、Azure Backup では自動修復の完全バックアップがトリガーされます。 この自動修復のバックアップが進行中の間、HANA によってトリガーされたすべてのログ バックアップが **OperationCancelledBecauseConflictingAutohealOperationRunningUserError** で失敗します。 自動修復の完全バックアップが完了すると、ログとその他のすべてのバックアップが正常に動作し始めます。<br>24 時間以内に自動修復完全バックアップがトリガーされない、または成功したバックアップ (完全、差分、増分) が確認できない場合は、Microsoft サポートにお問い合わせください。</br> |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| **エラー メッセージ**      | `SDC to MDC upgrade detected.`                                   |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | SDC システムが MDC にアップグレードされると、バックアップは失敗し、このエラーが発生します。 |
| **推奨される操作** | この問題をトラブルシューティングして解決するには、[SDC から MDC へのアップグレード](#sdc-to-mdc-upgrade-with-a-change-in-sid)に関する記事を参照してください。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| **エラー メッセージ**      | `Backups will fail with this error when the Backint Configuration is incorrectly updated.`                       |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | Azure Backup による保護の構成フロー中に更新された backint 構成が、顧客によって変更または更新されています。 |
| **推奨される操作** | 次の (backint) パラメーターが設定されているかどうかを確認します。<br><ul><li>[catalog_backup_using_backint:true]</li><li>[enable_accumulated_catalog_backup:false]</li><li>[parallel_data_backup_backint_channels:1]</li><li>[log_backup_timeout_s:900)]</li><li>[backint_response_timeout:7200]</li></ul>backint ベースのパラメーターが HOST レベルに存在する場合は、それらを削除します。 ただし、パラメーターが HOST レベルに存在しないが、データベース レベルで手動で変更されている場合は、データベース レベルの値が上記で設定されていることを確認してください。 または、Azure portal から[バックアップ データを保持した保護の停止](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)を実行してから、[バックアップの再開] を選択します。 |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|**エラー メッセージ**  | `The source and target systems for restore are incompatible.`  |
|---------|---------|
|**考えられる原因**   | ソースとターゲットの HANA データベース、およびシステムに互換性がない場合、復元フローは失敗し、このエラーが発生します。 |
|推奨される操作   |   復元シナリオが、次のような互換性のない復元の一覧に含まれていないことを確認します。<br> **ケース 1:** 復元中に SYSTEMDB の名前を変更することはできません。<br>**ケース 2:** ソースは SDC、ターゲットは MDC: ソース データベースをターゲット上の SYSTEMDB またはテナント DB として復元することはできません。 <br> **ケース 3:** ソースは MDC、ターゲットは SDC: ソース データベース (SYSTEMDB またはテナント DB) をターゲットに復元することはできません。<br>詳細については、[SAP サポート スタート パッド](https://launchpad.support.sap.com)のノート **1642148** を参照してください。 |

### <a name="usererrorhanapodoesnotexist"></a>UserErrorHANAPODoesNotExist

**エラー メッセージ** | `Database configured for backup does not exist.`
--------- | --------------------------------
**考えられる原因** | バックアップ用に構成されているデータベースが削除されると、このデータベースのすべてのスケジュールされたバックアップとアドホック バックアップが失敗します。
**推奨される操作** | データベースが削除されているか確認します。 データベースを作成し直すか、データベースの[保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します (データを保持して、または保持しないで)。

### <a name="usererrorinsufficientprivilegeofdatabaseuser"></a>UserErrorInsufficientPrivilegeOfDatabaseUser

**エラー メッセージ** |    `Azure Backup does not have enough privileges to carry out Backup and Restore operations.`
---------- | ---------
**考えられる原因** | 事前登録スクリプトによって作成されたバックアップ ユーザー (AZUREWLBACKUPHANAUSER) に、次のロールが 1 つ以上割り当てられていません。<ul><li>MDC の場合、復元中に新しいデータベースを作成するための DATABASE ADMIN および BACKUP ADMIN (HANA 2.0 SPS05 以降の場合)。</li><li>SDC の場合、復元中に新しいデータベースを作成するための BACKUP ADMIN。</li><li>バックアップ カタログを読み取るための CATALOG READ。</li><li>いくつかのプライベート テーブルにアクセスするための SAP_INTERNAL_HANA_SUPPORT。 HANA 2.0 SPS04 Rev 46 より前の SDC および MDC バージョンでのみ必要となります。 HANA 2.0 SPS04 Rev 46 以降では必要ありません。必要な情報は、HANA チームからの修正プログラムと共に、パブリック テーブルから入手することになります。</li></ul>
**推奨される操作** | 問題を解決するには、必要なロールとアクセス許可を手動でバックアップ ユーザー (AZUREWLBACKUPHANAUSER) に追加するか、SAP HANA インスタンスで[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)をダウンロードして実行します。

### <a name="usererrordatabaseuserpasswordexpired"></a>UserErrorDatabaseUserPasswordExpired

**エラー メッセージ** | `Database/Backup user's password expired.`
----------- | -----------
**考えられる原因** | 事前登録スクリプトによって作成されたデータベースまたはバックアップ ユーザーに、パスワードの有効期限が設定されていません。 ただし、変更された場合は、このエラーが表示される場合があります。
**推奨される操作** | この問題を解決するには、SAP HANA インスタンス上で[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)をダウンロードして実行します。

### <a name="usererrorinconsistentssfs"></a>UserErrorInconsistentSSFS

**エラー メッセージ** | `SAP HANA error`
------------ | ----------
**考えられる原因** | SAP HANA エンジンから受信した、セキュリティ保護ストレージ ファイル システム (SSFS) の不整合エラー。
**推奨される操作** | SAP HANA チームと協力してこの問題を修正してください。 詳細については、SAP ノート **0002097613** を参照してください。

### <a name="usererrorcannotconnecttoazureactivedirectoryservice"></a>UserErrorCannotConnectToAzureActiveDirectoryService

**エラー メッセージ** | `Unable to connect to the AAD service from the HANA system.`
--------- | --------
**考えられる原因** | バックアップ拡張機能のプラグイン サービス アカウントとしてのファイアウォールまたはプロキシの設定で、AAD への送信接続が許可されていません。
**推奨される操作** | AAD への送信接続が成功するようにファイアウォールまたはプロキシの設定を修正してください。

### <a name="usererrormisconfiguredsslcastore"></a>UserErrorMisConfiguredSslCaStore

**エラー メッセージ** | `Misconfigured CA store`
-------- | -------
**考えられる原因** | バックアップ拡張機能のプラグイン ホスト プロセスがルート CA ストア (SLES の場合は _/var/lib/ca-certificates/ca-bundle.pem_) にアクセスできません。
**推奨される操作** | `chmod o+r` を使用して元のアクセス許可を復元することで、CA ストアの問題を修正します。  次に、バックアップと復元が成功するようにプラグイン ホスト サービスを再起動します。

### <a name="usererrorbackupfailedasremedialbackupinprogress"></a>UserErrorBackupFailedAsRemedialBackupInProgress

**エラー メッセージ** | `Remedial Backup in progress.`
---------- | -------
**考えられる原因** | Azure Backup により、LSN ログ チェーンの切断に対処するための修復完全バックアップがトリガーされます。 この修復完全バックアップの進行中には、ポータルまたは CLI を介してトリガーされたバックアップ (完全/差分/増分) が失敗し、このエラーが発生します。
**推奨される操作** | 修復完全バックアップが正常に完了するまで待ってから、別のバックアップをトリガーします。

### <a name="operationcancelledbecauseconflictingoperationrunningusererror"></a>OperationCancelledBecauseConflictingOperationRunningUserError

**エラー メッセージ** | `Conflicting operation in progress.`
----------- | -------------
**考えられる原因** | 別の完全、差分、増分バックアップが既に進行中であるときに、ポータル、CLI、ネイティブ HANA クライアントで完全、差分、増分バックアップがトリガーされています。
**推奨される操作** | アクティブなバックアップ ジョブが完了するのを待ってから、新しい完全または差分バックアップをトリガーします。

### <a name="operationcancelledbecauseconflictingautohealoperationrunning-usererror"></a>OperationCancelledBecauseConflictingAutohealOperationRunning UserError

**エラー メッセージ** | `Auto-heal Full backup in progress.`
------- | -------
**考えられる原因** | Azure Backup により、**UserErrorHANALSNValidationFailure** を解決するために自動修復の完全バックアップがトリガーされます。 この自動修復のバックアップが進行中の間、HANA によってトリガーされたすべてのログ バックアップが **OperationCancelledBecauseConflictingAutohealOperationRunningUserError** で失敗します。<br>自動修復の完全バックアップが完了すると、ログとその他のすべてのバックアップが正常に動作し始めます。</br>
**推奨される操作** | 自動修復の完全バックアップが完了するのを待ってから、新しい完全または差分バックアップをトリガーします。

### <a name="usererrorhanaprescriptnotrun"></a>UserErrorHanaPreScriptNotRun

**エラー メッセージ** | `Pre-registration script not run.`
--------- | --------
**考えられる原因** | 環境を設定するための SAP HANA 事前登録スクリプトがまだ実行されていません。
**推奨される操作** | [事前登録スクリプト](https://aka.ms/scriptforpermsonhana)をダウンロードし、SAP HANA インスタンスで実行します。


### <a name="usererrortargetpoexistsoverwritenotspecified"></a>UserErrorTargetPOExistsOverwriteNotSpecified

**エラー メッセージ** | `Target database cannot be overwritten for Restore.`
------- | -------
**考えられる原因** | ターゲット データベースは存在しますが、上書きすることはできません。 ポータル または CLI の復元フローでは、強制的な上書きは設定されません。
**推奨される操作** | [強制的に上書きします] オプションを選択してデータベースを復元するか、別のターゲット データベースに復元します。

### <a name="usererrorrecoverysysscriptfailedtotriggerrestore"></a>UserErrorRecoverySysScriptFailedToTriggerRestore

**エラー メッセージ** | `RecoverySys.py could not be run successfully to restore System DB.`
-------- | ---------
**考えられる原因** | システム DB の復元が失敗する原因として考えられるものは次のとおりです。<ul><li>Azure Backup が HANA マシン上の **Recoverysys.py** を検出できません。 これは、HANA 環境が適切に設定されていない場合に発生します。</li><li>**Recoverysys.py** は存在しますが、HANA を呼び出して復元を実行するためのこのスクリプトのトリガーに失敗しました。</li><li>Recoverysys.py による、復元を実行するための HANA の呼び出しは成功しましたが、HANA による復元が失敗しました。</li></ul>
**推奨される操作** | <ul><li>問題 1 の場合は、SAP HANA チームと協力して問題を修正してください。</li><li>2 と 3 については、sid-adm プロンプトで HDSetting.sh コマンドを実行してログ トレースを参照してください。 たとえば、 _/usr/sap/SID/HDB00/HDBSetting.sh_ と入力します。</li></ul>これらの結果を SAP HANA チームと共有して問題を修正してください。

### <a name="usererrordbnamenotincorrectformat"></a>UserErrorDBNameNotInCorrectFormat

**エラー メッセージ** | `Restored database name not in correct format.`
--------- | --------
**考えられる原因** | ユーザーが指定した復元されたデータベース名が、許容または予期される形式ではありません。
**推奨される操作** | 復元されるデータベース名が文字で始まり、数字またはアンダースコア以外の記号が含まれていないことを確認します。<br>最大文字数は 127 文字で、先頭を "\_SYS_\" にすることはできません。

### <a name="usererrordefaultsidadmdirectorychanged"></a>UserErrorDefaultSidAdmDirectoryChanged

**エラー メッセージ** | `Default sid-adm directory changed.`
------- | -------
**考えられる原因** | 既定の **sid-adm** ディレクトリが変更され、使用できる **HDBSetting.sh** がこの既定のディレクトリにありません。
**推奨される操作** | HXE が SID である場合、環境変数 HOME が **sid-adm** ユーザーとして _/usr/sap/HXE/home_ に設定されていることを確認してください。

### <a name="usererrorhdbsettingsscriptnotfound"></a>UserErrorHDBsettingsScriptNotFound

**エラー メッセージ** | `HDBSetting.sh file cannot be found.`
--------- | -------
**考えられる原因** | 復元をトリガーするための **HDBsettings.sh** ファイルが **&lt;sid&gt;adm** ユーザー環境で見つからなかったため、システム データベースの復元が失敗しました。
**推奨される操作** | SAP HANA チームと協力してこの問題を修正してください。<br><br>HXE が SID である場合、環境変数 HOME が **sid-adm** ユーザーとして _/usr/sap/HXE/home_ に設定されていることを確認してください。

## <a name="restore-checks"></a>復元の確認

### <a name="single-container-database-sdc-restore"></a>単一コンテナー データベース (SDC) の復元

HANA 用の単一コンテナー データベース (SDC) を別の SDC マシンに復元するときは、入力に注意してください。 データベース名は小文字で指定し、かっこで囲んだ "sdc" を後に付ける必要があります。 HANA インスタンスは大文字で表示されます。

SDC HANA インスタンス "H21" がバックアップされているとします。 バックアップ項目のページに、バックアップ項目名が **"h21(sdc)"** と表示されます。 このデータベースを別のターゲット SDC (たとえば H11) に復元する場合は、次の入力を指定する必要があります。

![復元する SDC データベース名](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

以下の点に注意してください。

- 既定では、復元されるデータベース名に、バックアップ項目名が設定されます。 この場合は h21(sdc) です。
- ターゲットを H11 として選択しても、復元されるデータベース名は自動的に変更されません。 **h11(sdc) となるように編集する必要があります**。 SDC に関しては、復元されるデータベース名は、小文字のターゲット インスタンス ID の後に sdc をかっこで囲んで付けたものです。
- SDC はデータベースを 1 つしか含むことができないため、復旧ポイント データによる既存のデータベース データのオーバーライドを許可するチェックボックスをオンにすることも必要です。
- Linux では大文字と小文字が区別されます。 そのため、大文字と小文字を維持するように注意してください。

### <a name="multiple-container-database-mdc-restore"></a>複数コンテナー データベース (MDC) の復元

HANA 用の複数コンテナー データベースの場合、標準構成は SYSTEMDB と 1 つ以上のテナント DB です。 SAP HANA インスタンス全体の復元は、SYSTEMDB とテナント DB の両方を復元することを意味します。 最初に SYSTEMDB を復元してから、テナント DB の処理に進みます。 基本的にシステム DB では、選択したターゲットのシステム情報がオーバーライドされます。 この復元では、ターゲット インスタンス内の BackInt 関連情報もオーバーライドされます。 そのため、システム DB がターゲット インスタンスに復元された後に、事前登録スクリプトを再実行します。 その後でのみ、後続のテナント DB の復元が成功します。

## <a name="back-up-a-replicated-vm"></a>レプリケートされた VM のバックアップ

### <a name="scenario-1"></a>シナリオ 1

元の VM は Azure Site Recovery または Azure VM バックアップを使用してレプリケートされています。 新しい VM は古い VM をシミュレートするように構築されています。 つまり、設定はまったく同じです (これは、元の VM が削除され、VM バックアップまたは Azure Site Recovery から復元が行われたためです)。

このシナリオでは、2 つのケースが考えられます。 両方の場合について、レプリケートされた VM をバックアップする方法を説明します。

1. 新しく作成された VM は同じ名前で、削除された VM と同じリソース グループとサブスクリプションに存在する場合。

    - 拡張機能は VM に既に存在していますが、どのサービスにも表示されていません
    - 事前登録スクリプトを実行します
    - Azure portal で同じコンピューターに対して拡張機能を再登録します ( **[バックアップ]**  ->  **[詳細の表示]** -> [関連する Azure VM の選択] -> [再登録])
    - (削除された VM からの) 既存のバックアップ データベースが正常にバックアップされるようになります

2. 新しく作成された VM が次のいずれかの場合。

    - 削除された VM とは名前が異なる
    - 削除された VM と同じ名前だが、(削除された VM とは) 別のリソース グループまたはサブスクリプションに存在する

    この場合は、次の手順を実行します。

    - 拡張機能は VM に既に存在していますが、どのサービスにも表示されていません
    - 事前登録スクリプトを実行します
    - 新しいデータベースを検出して保護すると、ポータルに重複するアクティブなデータベースが表示されます。 これを回避するには、古いデータベースの[データを保持して保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します。 その後、残りの手順に進みます。
    - バックアップを有効にするデータベースを検出します
    - これらのデータベースでバックアップを有効にします
    - (削除された VM からの) 既存のバックアップ データベースは、引き続きコンテナーに格納されています (バックアップはポリシーに従って保持されます)

### <a name="scenario-2"></a>シナリオ 2

元の VM は Azure Site Recovery または Azure VM バックアップを使用してレプリケートされています。 新しい VM は、テンプレートとして使用するために、コンテンツから構築されています。 これは新しい SID を持つ新しい VM です。

新しい VM でバックアップを有効にするには、次の手順に従います。

- 拡張機能は VM に既に存在していますが、どのサービスにも表示されていません
- 事前登録スクリプトを実行します。 新しい VM の SID に基づいて、次の 2 つのシナリオが考えられます
  - 元の VM と新しい VM に同じ SID が割り当てられている。 事前登録スクリプトは正常に実行されます。
  - 元の VM と新しい VM に異なる SID が割り当てられている。 事前登録スクリプトは失敗します。 このシナリオについては、サポートにお問い合わせください。
- バックアップするデータベースを検出する
- これらのデータベースでバックアップを有効にします

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>同じ VM 上の SDC バージョン アップグレードまたは MDC バージョン アップグレード

SID が変更されない OS のアップグレード、SDC バージョンの変更、または MDC バージョンの変更は、次のように処理できます。

- 新しい OS バージョン、SDC、または MDC バージョンが現在 [Azure Backup でサポートされている](sap-hana-backup-support-matrix.md#scenario-support)ことを確認してください
- そのデータベースの[データを保持して保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します
- アップグレードまたは更新を実行します
- 事前登録スクリプトを再実行します。 多くの場合、アップグレード プロセスで[必要なロール](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)が削除されます。 事前登録スクリプトを実行すると、必要なすべてのロールを確認できます。
- データベースの保護を再開します

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SID の変更がない SDC から MDC へのアップグレード

SID が変更されない SDC から MDC へのアップグレードは、次のように処理できます。

- 新しい MDC バージョンが現在 [Azure Backup でサポートされている](sap-hana-backup-support-matrix.md#scenario-support)ことを確認してください
- 古い SDC データベースの[データを保持して保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します
- アップグレードを実行します。 完了後、HANA システムはシステム DB とテナント DB を備えた MDC になります
- [事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を再実行します
- Azure portal で同じコンピューターに対して拡張機能を再登録します ( **[バックアップ]**  ->  **[詳細の表示]** -> [関連する Azure VM の選択] -> [再登録])
- 同じ VM に対して **[DB の再検出]** を選択します。 このアクションにより、手順 3 の新しい DB が表示されます (SDC ではなく SYSTEMDB とテナント DB)
- 古い SDC データベースは引き続きコンテナーに存在し、ポリシーに従って古いバックアップ データが保持されます
- これらのデータベースのバックアップを構成します

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SID が変更される SDC から MDC へのアップグレード

SID が変更される SDC から MDC へのアップグレードは、次のように処理できます。

- 新しい MDC バージョンが現在 [Azure Backup でサポートされている](sap-hana-backup-support-matrix.md#scenario-support)ことを確認してください
- 古い SDC データベースの **データを保持して保護を停止** します
- アップグレードを実行します。 完了後、HANA システムはシステム DB とテナント DB を備えた MDC になります
- 適切な詳細 (新しい SID と MDC) を指定して、[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を再実行します。 SID の変更により、スクリプトを正常に実行する際に問題が発生する可能性があります。 問題が発生した場合は、Azure Backup サポートにお問い合わせください。
- Azure portal で同じコンピューターに対して拡張機能を再登録します ( **[バックアップ]**  ->  **[詳細の表示]** -> [関連する Azure VM の選択] -> [再登録])
- 同じ VM に対して **[DB の再検出]** を選択します。 このアクションにより、手順 3 の新しい DB が表示されます (SDC ではなく SYSTEMDB とテナント DB)
- 古い SDC データベースは引き続きコンテナーに存在し、ポリシーに従って古いバックアップ データが保持されます
- これらのデータベースのバックアップを構成します

## <a name="re-registration-failures"></a>再登録エラー

再登録操作をトリガーする前に、次の兆候が 1 つ以上ないか確認してください。

- VM ですべての操作 (バックアップ、復元、バックアップの構成など) が次のいずれかのエラー コードで失敗している。**WorkloadExtensionNotReachable、UserErrorWorkloadExtensionNotInstalled、WorkloadExtensionNotPresent、WorkloadExtensionDidntDequeueMsg**。
- バックアップ項目の **[バックアップ状態]** 領域に **[到達できません]** が表示されている場合は、同じ状態になる可能性のある他のすべての原因を除外します。

  - VM でバックアップ関連の操作を実行する権限がない
  - VM がシャットダウンされているため、バックアップが実行できない
  - ネットワークの問題

これらの兆候は、次の 1 つ以上の理由によって発生する可能性があります。

- 拡張機能がポータルから削除またはアンインストールされた。
- VM がインプレース ディスク復元を使用して時間内に復元された。
- VM が長期間シャットダウンされたため、その拡張機能構成の期限が切れた。
- VM が削除され、削除された VM と同じ名前で同じリソース グループに別の VM が作成された。

前のシナリオにおいて、VM で再登録操作をトリガーすることをお勧めします。

## <a name="next-steps"></a>次のステップ

- Azure VM 上の SAP HANA データベースのバックアップに関する[よく寄せられる質問](./sap-hana-faq-backup-azure-vm.yml)を確認する。
