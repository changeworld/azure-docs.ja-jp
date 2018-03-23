---
title: Azure Backup エージェントのトラブルシューティング | Microsoft Docs
description: Azure Backup エージェントのインストールと登録のトラブルシューティングを行います
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shreeshd
editor: ''
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: f7f4ac328c4e35f52bcc9708faf96d06189dd9ac
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>Azure Backup エージェントの構成および登録の問題のトラブルシューティング
## <a name="recommended-steps"></a>推奨される手順
Azure Backup エージェントの構成または登録中に発生する可能性があるエラーの解決方法については、次の表に示す推奨アクションを参照してください。

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>無効なコンテナーの資格情報が指定されました。 ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。
| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **エラー** </br> *無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。(ID: 34513)* | <ul><li> コンテナーの資格情報が有効ではありません (つまり、登録の時刻より 48 時間以上前にダウンロードされました)。<li>Azure Backup エージェントが、Windows Temp フォルダーに一時ファイルをダウンロードできません。 <li>コンテナーの資格情報がネットワークの場所にあります。 <li>TLS 1.0 が無効です<li> 構成されたプロキシ サーバーが接続をブロックしています。 <br> |  <ul><li>新しいコンテナー資格情報をダウンロードします。<li>**[インターネット オプション]** > **[セキュリティ]** > **[インターネット]** に移動します。 次に、**[レベルのカスタマイズ]** を選択し、ファイル ダウンロード セクションが表示されるまでスクロールします。 その後、**[有効化]** を選択します。<li>[信頼済みサイト](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)にサイトを追加することも必要な場合があります。<li>プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。 <li> 日付と時刻をコンピューターと一致させます。<li>C:/Windows/Temp に移動し、拡張子が .tmp のファイルが 60,000 または 65,000 個より多くあるかどうかを確認します。 ある場合は、それらのファイルを削除します。<li>これは、サーバーで SDP パッケージを実行することによってテストできます。 ファイルのダウンロードが許可されていないことを示すエラーが発生する場合は、C:/Windows/Temp ディレクトリに多数のファイルがあると考えられます。<li>.NET Framework 4.6.2 がインストールされていることを確認します。 <li>PCI のコンプライアンスのために TLS 1.0 を無効にしてある場合は、こちらの[トラブルシューティング ページ](https://support.microsoft.com/help/4022913)をご覧ください。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ul><li>CBengine.exe<li>CSC.exe (.NET Framework に関連するもの)。 CSC.exe は、サーバーにインストールされているすべての .NET のバージョンに対して存在します。 影響を受けるサーバー上の .NET Framework のすべてのバージョンに関連付けられている CSC.exe ファイルを除外してください。 <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services エージェントは Microsoft Azure Backup に接続できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **エラー** </br><ol><li>*Microsoft Azure Recovery Services Agent は Microsoft Azure Backup に接続できませんでした。(ID: 100050) ネットワーク設定を調べて、インターネットに接続できることを確認してください*<li>*(407) プロキシの認証が必要です* |プロキシが接続をブロックしています。 |  <ul><li>**[インターネット オプション]** > **[セキュリティ]** > **[インターネット]** に移動します。 次に、**[レベルのカスタマイズ]** を選択し、ファイル ダウンロード セクションが表示されるまでスクロールします。 **[有効化]** を選択します。<li>[信頼済みサイト](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)にサイトを追加することも必要な場合があります。<li>プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ul><li>CBEngine.exe (dpmra.exe ではありません)。<li>CSC.exe (.NET Framework に関連するもの)。 CSC.exe は、サーバーにインストールされているすべての .NET のバージョンに対して存在します。 影響を受けるサーバー上の .NET Framework のすべてのバージョンに関連付けられている CSC.exe ファイルを除外してください。 <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |      
| **エラー** </br>*セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした。現在の操作は、内部エラー "無効な入力エラー" が原因で失敗しました。しばらくしてから、操作を再試行してください。問題が解決しない場合は、Microsoft サポートにお問い合わせください*。 |サーバーは既に別のコンテナーに登録されています。| コンテナーからサーバーを登録解除してから、再び登録します。

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>ライセンス認証は正常に完了しませんでした。 サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |          
| **エラー** </br><ol><li>*ライセンス認証は正常に完了しませんでした。サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした。しばらくしてから、操作を再試行してください。問題が解決しない場合は、Microsoft サポートにお問い合わせください* <li>*エラー 34506。このコンピューター用に保存されている暗号化のパスフレーズは、正しく構成されていません*。 | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります。 <li> スクラッチ フォルダーが誤って別の場所に移動されました。 <li> OnlineBackup.KEK ファイルが見つかりません。 | <li>バックアップ データの合計サイズの 5 ～ 10% に相当する空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 キャッシュの場所を正しく移動する方法については、「[Azure Backup エージェントに関する質問](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)」の手順を参照してください。<li> OnlineBackup.KEK ファイルが存在することを確認します。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順
* [Azure Backup エージェントでの Windows Server のバックアップ方法](tutorial-backup-windows-server-to-azure.md)についての詳しい情報を見ます。
* バックアップを復元する必要がある場合は、 [Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。
