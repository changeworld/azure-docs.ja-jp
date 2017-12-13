---
title: "Azure Backup エージェント (MARS エージェント) のトラブルシューティング | Microsoft Docs"
description: "Azure Backup エージェントのインストールと登録のトラブルシューティングを行います"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: da297177ca56822f7d50dbe06ec022640bf5d0cb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2017
---
# <a name="troubleshooting-azure-backup-agent-configurationregistration-issues"></a>Azure Backup エージェントの構成/登録の問題のトラブルシューティング
## <a name="recommended-steps"></a>推奨される手順
Azure Backup エージェントの構成または登録中に発生するエラーを解決するには、以下で指定されている対応する推奨アクションを参照してください。

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>エラー: 無効なコンテナーの資格情報が指定されました。 ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。
| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **エラー** </br> *無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。(ID: 34513)* | <ol><li> コンテナーの資格情報が有効ではありません (つまり、 登録の時刻より 48 時間以上前にダウンロードされました)。<li>   Azure Backup エージェントは、Windows の Temp フォルダーに一時ファイルをダウンロードできません。 <li>コンテナーの資格情報がネットワークの場所にあります。 <li>TLS 1.0 が無効です<li> 構成されたプロキシ サーバーが接続をブロックしています <br> |  <ol><li>新しいコンテナー資格情報をダウンロードします<li>[インターネット オプション] > [セキュリティ] > [インターネット] > [レベルのカスタマイズ] の順にクリックし、[ファイルのダウンロード] セクションまでスクロールして、有効にします。<li>[信頼済みサイト](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)にサイトを追加することも必要な場合があります。<li>プロキシを使うように設定を変更し、プロキシの詳細を指定します <li> 日付と時刻 (UTC) をコンピューターと一致させます<li>C:/Windows/Temp に移動し、60,000 または 65,000 個より多くのファイル (拡張子が .tmp のファイル) を確認して、これらのファイルを削除します。<li>これは、サーバーで SDP パッケージを実行することによってテストできます。 ファイルのダウンロードが許可されていないことを示すエラーが発生する場合は、C:/Windows/Temp ディレクトリに多数のファイルがあると考えてまず間違いありません。<li>.NET Framework 4.6.2 がインストールされていることを確認します <li>PCI のコンプライアンスのために TLS 1.0 を無効にしてある場合は、こちらの[トラブルシューティングに関するリンク](https://support.microsoft.com/help/4022913)をご覧ください。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ol><li>CBengine.exe<li>CSC.exe (.NET Framework に関連があります。 サーバーにインストールされている .NET のバージョンごとに CSC.exe があります。 影響を受けるサーバー上の .NET Framework のすべてのバージョンに関連付けられているすべての CSC.exe ファイルを除外します。) <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です。*

## <a name="error-the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>エラー: Microsoft Azure Recovery Services Agent は Microsoft Azure Backup に接続できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| **エラー** </br><ol><li>*Microsoft Azure Recovery Services Agent は Microsoft Azure Backup に接続できませんでした。(ID: 100050) ネットワーク設定を調べて、インターネットに接続できることを確認してください*<li>*(407) プロキシの認証が必要です* |プロキシが接続をブロックしています* |  <ol><li>[インターネット オプション] > [セキュリティ] > [インターネット] > [レベルのカスタマイズ] の順にクリックし、[ファイルのダウンロード] セクションまでスクロールして、有効にします。<li>[信頼済みサイト](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)にサイトを追加することも必要な場合があります。<li>プロキシを使うように設定を変更し、プロキシの詳細を指定します <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、次のファイルをウイルス対策スキャンから除外します。 <ol><li>CBengine.exe<li>(dpmra.exe ではありません)<li>CSC.exe (.NET Framework に関連があります。 サーバーにインストールされている .NET のバージョンごとに CSC.exe があります。 影響を受けるサーバー上の .NET Framework のすべてのバージョンに関連付けられているすべての CSC.exe ファイルを除外します。) <li>スクラッチ フォルダーまたはキャッシュの場所 <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です。*

## <a name="error-failed-to-set-the-encryption-key-for-secure-backups"></a>エラー: セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |      
| **エラー** </br>*セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした。現在の操作は、内部エラー "無効な入力エラー" が原因で失敗しました。しばらくしてから、操作を再試行してください。問題が解決しない場合は、Microsoft サポートにお問い合わせください* |サーバーは既に別のコンテナーに登録されています| コンテナーからサーバーを登録解除してから、再び登録します。

## <a name="error-the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>エラー: ライセンス認証は正常に完了しませんでした。 サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした

| エラーの詳細 | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |          
| **エラー** </br><ol><li>*ライセンス認証は正常に完了しませんでした。サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした。しばらくしてから、操作を再試行してください。問題が解決しない場合は、Microsoft サポートにお問い合わせください* <li>*エラー 34506。このコンピューター用に保存されている暗号化のパスフレーズは、正しく構成されていません* | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります <li> スクラッチ フォルダーが誤って別の場所に移動されました <li> OnlineBackup.KEK ファイルが見つかりません | <li>バックアップ データの合計サイズの 5 ～ 10% に相当する空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 [こちらの記事](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)で説明されている手順を参考にして、キャッシュの場所を正しく移動します。<li> OnlineBackup.KEK ファイルが存在することを確認します <br>*スクラッチ フォルダーまたはキャッシュの場所のパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です。*

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ
* [Azure Backup エージェントでの Windows Server のバックアップ](tutorial-backup-windows-server-to-azure.md)についての詳しい情報を見ます。
* バックアップを復元する必要がある場合は、 [Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。
