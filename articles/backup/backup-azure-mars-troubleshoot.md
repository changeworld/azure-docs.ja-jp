---
title: Azure Backup エージェントのトラブルシューティング
description: Azure Backup エージェントのインストールと登録のトラブルシューティングを行います
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: e7a63167285c06fdfe632e7d45d9fddd3cca7842
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248524"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>Microsoft Azure Recovery Services (MARS) エージェントの問題をトラブルシューティングする
## <a name="recommended-steps"></a>推奨される手順
この記事をご覧になると、MARS エージェントを使用して構成、登録、バックアップ、および復元を行うときに発生したエラーを解決できます。

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>無効なコンテナーの資格情報が指定されました。 ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。
| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **エラー** </br> *無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。(ID: 34513)* | <ul><li> コンテナーの資格情報が有効ではありません (つまり、登録の時刻より 48 時間以上前にダウンロードされました)。<li>MARS エージェントが Windows の Temp ディレクトリにファイルをダウンロードできません。 <li>コンテナーの資格情報がネットワークの場所にあります。 <li>TLS 1.0 が無効です<li> 構成されたプロキシ サーバーが接続をブロックしています。 <br> |  <ul><li>新しいコンテナー資格情報をダウンロードします。<li>**[インターネット オプション]** > **[セキュリティ]** > **[インターネット]** に移動します。 次に、**[レベルのカスタマイズ]** を選択し、ファイル ダウンロード セクションが表示されるまでスクロールします。 その後、**[有効化]** を選択します。<li>[信頼済みサイト](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)にサイトを追加することも必要な場合があります。<li>プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。 <li> 日付と時刻をコンピューターと一致させます。<li>C:/Windows/Temp に移動し、拡張子が .tmp のファイルが 60,000 または 65,000 個より多くあるかどうかを確認します。 ある場合は、それらのファイルを削除します。<li>これは、サーバー上で SDP パッケージを実行して確認できます。 ファイルのダウンロードが許可されていないことを示すエラーが発生する場合は、C:/Windows/Temp ディレクトリ内に多数のファイルが存在している可能性があります。<li>.NET Framework 4.6.2 がインストールされていることを確認します。 <li>PCI のコンプライアンスのために TLS 1.0 を無効にしてある場合は、こちらの[トラブルシューティング ページ](https://support.microsoft.com/help/4022913)をご覧ください。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ul><li>CBengine.exe<li>CSC.exe (.NET Framework に関連するもの)。 CSC.exe は、サーバーにインストールされているすべての .NET のバージョンに対して存在します。 影響を受けるサーバー上の .NET Framework のすべてのバージョンに関連付けられている CSC.exe ファイルを除外してください。 <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services エージェントは Microsoft Azure Backup に接続できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **エラー** </br><ol><li>*Microsoft Azure Recovery Services Agent は Microsoft Azure Backup に接続できませんでした。(ID: 100050) ネットワーク設定を調べて、インターネットに接続できることを確認してください*<li>*(407) プロキシの認証が必要です* |プロキシが接続をブロックしています。 |  <ul><li>**[インターネット オプション]** > **[セキュリティ]** > **[インターネット]** に移動します。 次に、**[レベルのカスタマイズ]** を選択し、ファイル ダウンロード セクションが表示されるまでスクロールします。 **[有効化]** を選択します。<li>[信頼済みサイト](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)にサイトを追加することも必要な場合があります。<li>プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ul><li>CBEngine.exe (dpmra.exe ではありません)。<li>CSC.exe (.NET Framework に関連するもの)。 CSC.exe は、サーバーにインストールされているすべての .NET のバージョンに対して存在します。 影響を受けるサーバー上の .NET Framework のすべてのバージョンに関連付けられている CSC.exe ファイルを除外してください。 <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |      
| **エラー** </br>*セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした。ライセンス認証は完全には成功しませんでしたが、暗号化パスフレーズが次のファイルに保存されました*。 |<li>サーバーは既に別のコンテナーに登録されています。<li>構成時に、パスフレーズが破損しました| コンテナーからサーバーの登録を解除した後、新しいパスフレーズを使ってもう一度登録します。

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>ライセンス認証は正常に完了しませんでした。 サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
|---------|---------|---------|
|**エラー** </br><ol>*ライセンス認証は正常に完了しませんでした。サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした。しばらくしてから操作を再試行してください。問題が解決しない場合は、Microsoft サポートにお問い合わせください*     | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります。 <li> スクラッチ フォルダーが誤って別の場所に移動されました。 <li> OnlineBackup.KEK ファイルが見つかりません。         | <li>[最新バージョン](http://aka.ms/azurebackup_agent)の MARS エージェントにアップグレードしてください。<li>バックアップ データの合計サイズの 5 ～ 10% に相当する空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 キャッシュの場所を正しく移動する方法については、「[Azure Backup エージェントに関する質問](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)」の手順を参照してください。<li> OnlineBackup.KEK ファイルが存在することを確認します。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>エラー 34506。 このコンピューター用に保存されている暗号化のパスフレーズは、正しく構成されていません

| エラーの詳細 | 考えられる原因 | 推奨アクション |
|---------|---------|---------|
|**エラー** </br><ol>*エラー 34506。このコンピューター用に保存されている暗号化のパスフレーズは、正しく構成されていません*。    | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります。 <li> スクラッチ フォルダーが誤って別の場所に移動されました。 <li> OnlineBackup.KEK ファイルが見つかりません。        | <li>[最新バージョン](http://aka.ms/azurebackup_agent)の MARS エージェントにアップグレードしてください。<li>バックアップ データの合計サイズの 5 ～ 10% に相当する空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 キャッシュの場所を正しく移動する方法については、「[Azure Backup エージェントに関する質問](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)」の手順を参照してください。<li> OnlineBackup.KEK ファイルが存在することを確認します。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。         |

## <a name="backups-do-not-run-as-per-schedule"></a>バックアップがスケジュールに従って実行されない
手動によるバックアップが問題なく動作しているのに、スケジュールされたバックアップが自動的にトリガーされないときは、以下の手順を実行します。 
 
<li>PowerShell 3.0 以降がサーバー上にインストールされていることを確認します。 PowerShell のバージョンを確認するには、次のコマンドを実行し、"*メジャー*" バージョン番号が 3 以上であることを確認します。

`$PSVersionTable.PSVersion`
<li>次のパスが *PSMODULEPATH* 環境変数に含まれていることを確認します。

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>*LocalMachine* の PowerShell 実行ポリシーが restricted に設定されている場合は、バックアップ タスクをトリガーする PowerShell コマンドレットが失敗することがあります。 次のコマンドを管理者特権モードで実行して、実行ポリシーを確認します。次に、実行ポリシーを *Unrestricted* または *RemoteSigned* のいずれかに設定します。

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>コントロール パネルに移動して、[管理者ツール] -> [タスク スケジューラ] を選択し、[Microsoft] を展開して、[オンライン バックアップ] を選択します。
<li>[Microsoft-OnlineBackup]\(Microsoft オンライン バックアップ\) タスクをダブルクリックし、[トリガー] タブに移動します。
<li>タスクの [Status]\(状態\) が [有効] に設定されていることを確認します。 設定されていない場合は、[編集] をクリックし、[有効] チェック ボックスをオンにします。
<li>*[General]\(全般\)* タブの *[Security options]\(セキュリティ オプション\)* セクションに移動します。
<li>タスク実行のために選択したユーザー アカウントが *SYSTEM*、またはサーバー上のローカル管理者のグループであることを確認します。> [!TIP]上記の手順を実行した後は、変更を確実に適用するために、サーバーを再起動することをお勧めします。


## <a name="troubleshooting-restore-issues"></a>復元に関する問題のトラブルシューティング

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>ファイルとフォルダーの回復中に、回復ボリュームのマウントが失敗する
Azure Backup が、**[マウント]** のクリック後数分経過しても回復ボリュームを正常にマウントしない場合、または 1 つ以上のエラーによって回復ボリュームをマウントできない場合、回復を正常に開始するには、以下の手順に従ってください。

1.  進行中のマウント プロセスが数分間実行されている場合はキャンセルします。

2.  最新バージョンの Azure Backup エージェントを使用していることを確認します。 Azure Backup エージェントのバージョン情報を調べるには、Microsoft Azure Backup コンソールの **[アクション]** ウィンドウにある **[About Microsoft Azure Recovery Services Agent]\(Microsoft Azure Recovery Services エージェントについて\)** をクリックし、**バージョン**番号が[この記事](https://go.microsoft.com/fwlink/?linkid=229525)に示されているバージョン以上であることを確認します。 最新バージョンは [こちら](https://go.microsoft.com/fwLink/?LinkID=288905)からダウンロードできます。

3.  **[デバイス マネージャー]** -> **[ストレージ コントローラー]** に移動し、**Microsoft iSCSI イニシエーター**が見つかることを確認します。 見つかった場合は、下の手順 7. に進みます。 

4.  手順 3. で説明したように Microsoft iSCSI イニシエーター サービスが見つからない場合は、**[デバイス マネージャー]** -> **[ストレージ コントローラー]** の下に、**[不明なデバイス]** という名前の、ハードウェア ID **ROOT\ISCSIPRT** を持つエントリがあるかどうかを確認します。

5.  **[不明なデバイス]** を右クリックし、**[ドライバー ソフトウェアの更新]** を選択します。

6.  **[自動的に更新されたドライバ ソフトウェアを検索します]** を選択して、ドライバーを更新します。 更新が完了すると、下に示すように、**[不明なデバイス]** が **[Microsoft iSCSI イニシエーター]** に変わります。 

    ![暗号化](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  **[タスク マネージャー]** -> **[サービス (ローカル)]** -> **[Microsoft iSCSI イニシエーター サービス]** に移動します。 

    ![暗号化](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Microsoft iSCSI イニシエーター サービスを再起動するには、サービスを右クリックして [停止] をクリックし、もう一度右クリックして **[開始]** をクリックします。

9.  インスタント リストアを使用して回復を再試行します。 

それでも回復が失敗する場合は、サーバーまたはクライアントを再起動します。 再起動が望ましくない場合、またはサーバーを再起動しても回復が失敗する場合は、[こちらの記事](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)に記載されている手順を実行して、代わりのマシンから回復を試みてください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順
* [Azure Backup エージェントでの Windows Server のバックアップ方法](tutorial-backup-windows-server-to-azure.md)についての詳しい情報を見ます。
* バックアップを復元する必要がある場合は、 [Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。
