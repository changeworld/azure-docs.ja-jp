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
ms.openlocfilehash: c477eee662d20c66eaa3be3927e47c2c6bf379a6
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633266"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントをトラブルシューティングする

以下に、構成、登録、バックアップ、および復元中に発生する可能性があるエラーの解決方法を示します。

## <a name="invalid-vault-credentials-provided"></a>無効なコンテナーの資格情報が指定されました
| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **Error** </br> *無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。(ID: 34513)* | <ul><li> コンテナーの資格情報が有効ではありません (つまり、登録の時刻より 48 時間以上前にダウンロードされました)。<li>MARS エージェントが Windows の Temp ディレクトリにファイルをダウンロードできません。 <li>コンテナーの資格情報がネットワークの場所にあります。 <li>TLS 1.0 が無効です<li> 構成されたプロキシ サーバーが接続をブロックしています。 <br> |  <ul><li>新しいコンテナー資格情報をダウンロードします (**注**: 既に複数のコンテナー資格情報ファイルをダウンロードされている場合は、48 時間以内にダウンロードされた最新のファイルのみが有効になります)。 <li>**[インターネット オプション]** > **[セキュリティ]** > **[インターネット]** に移動します。 次に、**[レベルのカスタマイズ]** を選択し、ファイル ダウンロード セクションが表示されるまでスクロールします。 その後、**[有効化]** を選択します。<li>IE の[信頼済みサイト](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)へのサイトの追加が必要な場合もあります。<li>プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。 <li> 日付と時刻をコンピューターと一致させます。<li>ファイルのダウンロードが許可されていないことを示すエラーが発生する場合は、C:/Windows/Temp ディレクトリ内に多数のファイルが存在している可能性があります。<li>C:/Windows/Temp に移動し、拡張子が .tmp のファイルが 60,000 または 65,000 個より多くあるかどうかを確認します。 ある場合は、それらのファイルを削除します。<li>.NET Framework 4.6.2 がインストールされていることを確認します。 <li>PCI のコンプライアンスのために TLS 1.0 を無効にしてある場合は、こちらの[トラブルシューティング ページ](https://support.microsoft.com/help/4022913)をご覧ください。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ul><li>CBengine.exe<li>CSC.exe (.NET Framework に関連するもの)。 CSC.exe は、サーバーにインストールされているすべての .NET のバージョンに対して存在します。 影響を受けるサーバー上の .NET Framework の各バージョンに関連付けられている CSC.exe ファイルを除外してください。 *たとえば、v2.0.50727 の場合は、.NET Framework パスの C:\Windows\Microsoft.NET\Framework\v2.0.50727 を除外します。同様に、残りの .NET Framework のバージョン パスを除外します。* <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。<br><li>bin フォルダー C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="the-mars-agent-was-unable-to-connect-to-azure-backup"></a>MARS エージェントが Azure Backup に接続できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **Error** </br><ol><li>*Microsoft Azure Recovery Services Agent は Microsoft Azure Backup に接続できませんでした。(ID: 100050) ネットワーク設定を調べて、インターネットに接続できることを確認してください*<li>*(407) プロキシの認証が必要です* |プロキシが接続をブロックしています。 |  <ul><li>**[インターネット オプション]** > **[セキュリティ]** > **[インターネット]** に移動します。 次に、**[レベルのカスタマイズ]** を選択し、ファイル ダウンロード セクションが表示されるまでスクロールします。 **[有効化]** を選択します。<li>IE の[信頼済みサイト](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)へのサイトの追加が必要な場合もあります。<li>プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ul><li>CBEngine.exe (dpmra.exe ではありません)。<li>CSC.exe (.NET Framework に関連するもの)。 CSC.exe は、サーバーにインストールされているすべての .NET のバージョンに対して存在します。 影響を受けるサーバー上の .NET Framework のすべてのバージョンに関連付けられている CSC.exe ファイルを除外してください。 *たとえば、v2.0.50727 の場合は、.NET Framework パスの C:\Windows\Microsoft.NET\Framework\v2.0.50727 を除外します。同様に、残りの .NET Framework のバージョン パスを除外します。* <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。<li>bin フォルダー C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |      
| **Error** </br>*セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした。ライセンス認証は完全には成功しませんでしたが、暗号化パスフレーズが次のファイルに保存されました*。 |<li>サーバーは既に別のコンテナーに登録されています。<li>構成時に、パスフレーズが破損しました。| コンテナーからサーバーの登録を解除した後、新しいパスフレーズを使ってもう一度登録します。

## <a name="the-activation-did-not-complete-successfully"></a>ライセンス認証が正常に完了しませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
|---------|---------|---------|
|**Error** </br><ol>*ライセンス認証は正常に完了しませんでした。サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした。しばらくしてから操作を再試行してください。問題が解決しない場合は、Microsoft サポートにお問い合わせください*     | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります。 <li> スクラッチ フォルダーが誤って別の場所に移動されました。 <li> OnlineBackup.KEK ファイルが見つかりません。         | <li>[最新バージョン](https://aka.ms/azurebackup_agent)の MARS エージェントにアップグレードしてください。<li>バックアップ データの合計サイズの 5 ～ 10% に相当する空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 キャッシュの場所を正しく移動する方法については、「[Azure Backup エージェントに関する質問](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)」の手順を参照してください。<li> OnlineBackup.KEK ファイルが存在することを確認します。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>暗号化のパスフレーズが正しく構成されていません

| エラーの詳細 | 考えられる原因 | 推奨アクション |
|---------|---------|---------|
|**Error** </br><ol>*エラー 34506。このコンピューター用に保存されている暗号化のパスフレーズは、正しく構成されていません*。    | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります。 <li> スクラッチ フォルダーが誤って別の場所に移動されました。 <li> OnlineBackup.KEK ファイルが見つかりません。        | <li>[最新バージョン](https://aka.ms/azurebackup_agent)の MARS エージェントにアップグレードしてください。<li>バックアップ データの合計サイズの 5 ～ 10% に相当する空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 キャッシュの場所を正しく移動する方法については、「[Azure Backup エージェントに関する質問](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)」の手順を参照してください。<li> OnlineBackup.KEK ファイルが存在することを確認します。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。         |


## <a name="backups-dont-run-according-to-the-schedule"></a>バックアップがスケジュールに従って実行されません
手動によるバックアップが問題なく動作しているのに、スケジュールされたバックアップが自動的にトリガーされないときは、以下の操作を試してください。

- PowerShell 3.0 以降がサーバーにインストールされているかどうかを確認します。 PowerShell のバージョンを確認するために次のコマンドを実行し、"*メジャー*" バージョン番号が 3 以上であることを確認します。

  `$PSVersionTable.PSVersion`

- 次のパスが *PSMODULEPATH* 環境変数に含まれているかどうかを確認します。

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- *LocalMachine* の PowerShell 実行ポリシーが restricted に設定されている場合は、バックアップ タスクをトリガーする PowerShell コマンドレットが失敗することがあります。 次のコマンドを管理者特権モードで実行して、実行ポリシーを確認します。次に、実行ポリシーを *Unrestricted* または *RemoteSigned* に設定します。

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- **[コントロール パネル]** > **[管理ツール]** > **[タスク スケジューラ]** の順に移動します。 **[Microsoft]** を展開し、**[オンライン バックアップ]** を選択します。 **[Microsoft-OnlineBackup]\(Microsoft オンライン バックアップ\)** をダブルクリックし、**[トリガー]** タブに移動します。状態が **[有効]** に設定されていることを確認します。 設定されていない場合は、**[編集]** を選択し、**[有効]** チェック ボックスをオンにします。 **[全般]** タブで **[セキュリティ オプション]** に移動します。 タスク実行のために選択したユーザー アカウントが **SYSTEM**、またはサーバー上の**ローカル管理者のグループ**であることを確認します。


> [!TIP]
> 変更を確実に適用するために、上記の手順を実行した後で、サーバーを再起動します。


## <a name="troubleshoot-restore-issues"></a>復元の問題のトラブルシューティング

Azure Backup が、数分たっても回復ボリュームに正常にマウントできないことがあります。 処理中にエラー メッセージを受け取ることもあります。 正常に回復を開始するには、次の手順に従います。

1.  進行中のマウント プロセスが数分間実行されている場合は取り消します。

2.  Backup エージェントが最新バージョンであるかどうかを確認します。 バージョンを調べるには、MARS コンソールの **[アクション]** ウィンドウで、**[About Microsoft Azure Recovery Services Agent]\(Microsoft Azure Recovery Services エージェントについて\)** を選択します。 **バージョン**番号が、[この記事](https://go.microsoft.com/fwlink/?linkid=229525)に記載されているバージョン以上であることを確認します。 最新バージョンは、[こちら](https://go.microsoft.com/fwLink/?LinkID=288905)からダウンロードできます。

3.  **[デバイス マネージャー]** > **[ストレージ コントローラー]** の順に移動し、**Microsoft iSCSI イニシエーター**を探します。 見つかった場合は、手順 7. に進みます。

4.  Microsoft iSCSI イニシエーター サービスが見つからない場合は、**[デバイス マネージャー]** > **[記憶域コントローラー]** の下に、**[不明なデバイス]** という名前の、ハードウェア ID が **ROOT\ISCSIPRT** のエントリを探します。

5.  **[不明なデバイス]** を右クリックし、**[ドライバー ソフトウェアの更新]** を選択します。

6.  **[自動的に更新されたドライバ ソフトウェアを検索します]** を選択して、ドライバーを更新します。 更新が完了すると、下に示すように、**[不明なデバイス]** が **[Microsoft iSCSI イニシエーター]** に変わります。

    ![[記憶域コントローラー] が強調表示されている Azure Backup デバイス マネージャーのスクリーンショット](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  **[タスク マネージャー]** > **[サービス (ローカル)]** > **[Microsoft iSCSI イニシエーター サービス]** に移動します。

    ![[サービス (ローカル)] が強調表示されている Azure Backup タスク マネージャーのスクリーンショット](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Microsoft iSCSI イニシエーター サービスを再開します。 そうするには、サービスを右クリックして **[停止]** を選択し、もう一度右クリックしてから **[開始]** を選択します。

9.  **インスタント リストア**を使用して回復を再試行します。

それでも回復が失敗する場合は、サーバーまたはクライアントを再起動します。 再起動したくない場合、またはサーバーを再起動しても回復が失敗する場合は、別のマシンから回復を試行してください。 手順については、[この記事](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)を参照してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順
* [Azure Backup エージェントでの Windows Server のバックアップ方法](tutorial-backup-windows-server-to-azure.md)についての詳しい情報を見ます。
* バックアップを復元する必要がある場合は、 [Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。
