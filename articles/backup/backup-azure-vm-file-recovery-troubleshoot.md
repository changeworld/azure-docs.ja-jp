---
title: Azure VM ファイルの回復に関するトラブルシューティングを行う
description: Azure VM バックアップからのファイルとフォルダーの回復に固有の問題のトラブルシューティングを行います。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97512085"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Azure VM バックアップのファイル回復に関する問題のトラブルシューティング

この記事では、Azure VM バックアップからのファイルとフォルダーの回復に固有の問題に関連する Azure Backup エラーの解決に役立つトラブルシューティングの手順について説明します。 

## <a name="exception-caught-while-connecting-to-target"></a>ターゲットへの接続中に例外がキャッチされました

考えられる原因:スクリプトで回復ポイントにアクセスできません。推奨される操作: コンピューターですべての[アクセス要件](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)が満たされているかどうかを確認します。

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>ターゲットは iSCSI セッション経由で既にログインされています

考えられる原因:スクリプトは同じコンピューターで既に実行されており、ドライブがアタッチされています。
推奨される操作:回復ポイントのボリュームは既にアタッチされています。 ボリュームは、元の VM と同じドライブ文字を使用するとマウントされないことがあります。 エクスプローラーで使用可能なすべてのボリュームを参照します。

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>ディスクがポータルからマウント解除されているか、12 時間制限を超過しているため、 このスクリプトは無効です。 ポータルから新しいスクリプトをダウンロードしてください

考えられる原因:ディスクがポータルからマウント解除されているか、12 時間制限を超過しています。
推奨される操作:スクリプトはダウンロードされてから 12 時間後に無効になり、実行できません。 ファイルの回復を続行するには、ポータルにアクセスし、新しいスクリプトをダウンロードします。

## <a name="troubleshooting-common-issues"></a>一般的な問題のトラブルシューティング

このセクションでは、ファイル回復用のスクリプトのダウンロードと実行中に発生した問題をトラブルシューティングする手順について説明します。

## <a name="cannot-download-the-script"></a>スクリプトをダウンロードできません

推奨される操作:

1. [スクリプトをダウンロードするために必要なすべてのアクセス許可](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script)があることを確かめます。
2. Azure ターゲット IP への接続があることを確かめます。
接続を確認するには、管理者特権でのコマンド プロンプトから次のコマンドを実行します。 
    - *nslookup download.microsoft.com* または
    - *ping download.microsoft.com*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>スクリプトは正常にダウンロードされますが、実行に失敗します

### <a name="for-sles-12-sp4-os-linux"></a>SLES 12 SP4 OS (Linux) の場合

エラー: iscsi_tcp_module が見つかりません

考えられる原因:SUSE Linux OS バージョン 12sp4 で項目レベルの回復用の Python スクリプトを実行しているときに、***iscsi_tcp モジュールを読み込むことができません** _ というエラーで失敗します。 ILR モジュールでは、'iscsi_tcp' を使用してバックアップ サービスへの tcp 接続を確立します。SUSE では、12SP4 リリースの一部として、open-iscsi パッケージから iscsi_tcp が削除されたため、ILR 操作は失敗します。

推奨される操作:SUSE 12SP4 VM でのファイルの回復スクリプトの実行はサポートされていません。以前のバージョンの SUSE 12SP4 で復元操作を試してください。

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>スクリプトは実行されますが、iSCSI ターゲットへの接続は失敗しました

エラー:ターゲットへの接続中に例外がキャッチされました

1. スクリプトが実行されているコンピューターですべての[アクセス要件](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)が満たされていることを確かめます。
2. Azure ターゲット IP への接続を確認します。
接続を確認するには、管理者特権でのコマンド プロンプトから次のコマンドを実行します。 
    - _nslookup download.microsoft.com* または<br>
    - *ping download.microsoft.com*
3. iSCSI 送信ポート 3260 にアクセスできることを確かめます。
4. Azure ターゲット IP または復旧サービスの URL へのトラフィックをブロックしているファイアウォールや NSG がないことを確かめます。
5. ウイルス対策により、スクリプトの実行がブロックされているかどうかを確認します。

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>回復ポイントに接続しましたが、ディスクがアタッチされませんでした

[スクリプトを実行するための適切なコンピューター](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)があることを確かめます

### <a name="on-windows-vm"></a>Windows VM の場合

**VM 上の記憶域プールが読み取り専用モードでアタッチされています** Windows 2012 R2 および Windows 2016 (記憶域プールを含む) の場合、スクリプトを初めて実行するときは、VM にアタッチされている記憶域プールが読み取り専用状態になることがあります。

この問題を解決するには、最初に記憶域プールへの読み取り/書き込みアクセスを手動で設定し、仮想ディスクをアタッチする必要があります。以下の手順に従ってください。

1. 読み取り/書き込みアクセスを設定します。
サーバー マネージャー > [ファイル サービスおよび記憶域サービス] > [ボリューム] > [記憶域プール] の順に移動します。

   ![Windows ストレージ](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. **[記憶域プール]** ウィンドウで、使用可能な記憶域プールを右クリックし、 **[Set Read-Write Access]\(読み取り-書き込みアクセス権の設定\)** オプションを選択します。

   ![Windows ストレージの読み取りと書き込み](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. 記憶域プールに読み取り/書き込みアクセス権が割り当てられた後、仮想ディスクをアタッチする必要があります。
**サーバー マネージャーの UI** に移動し、[仮想ディスク] セクションで右クリックして **[仮想ディスクのアタッチ]** オプションを選択します。

   ![サーバー マネージャーの仮想ディスク](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Linux VM の場合

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>ディスクにボリュームが含まれていないため、ファイルの回復で自動マウントに失敗します

ファイルの回復を実行しているときに、バックアップ サービスによってボリュームと自動マウントが検出されます。 しかし、バックアップ ディスクに未加工のパーティションがある場合、それらのディスクは自動マウントされず、回復用のデータ ディスクを表示できません。

この問題を解決する場合は、こちらの[記事](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)に記載されている手順に従ってください。
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>OS でファイル システムを特定できなかったため、ディスクのマウント中に Linux ファイルの回復に失敗しました

ファイルの回復スクリプトを実行しているときに、以下のエラーが発生し、データ ディスクをアタッチできませんでした。"*OS でファイル システムを特定できなかったため、次のパーティションをマウントできませんでした*"

- この問題を解決するには、ボリュームがサードパーティ製アプリケーションで暗号化されているかどうかを確認します。 暗号化されている場合、ディスクまたは VM はポータルで暗号化されたものとして表示されません。
1. バックアップされた VM にログインし、次のコマンドを実行します: *lsblk-f*<br>

   ![ボリュームのないディスク](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. ファイル システムと暗号化を確認します。
3. ボリュームが暗号化されている場合、ファイルの回復はサポートされません。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)。

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>ディスクはアタッチされていますが、ボリュームをマウントできません

- [スクリプトを実行するための適切なコンピューター](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)があることを確かめます。

### <a name="on-windows-vm"></a>Windows VM の場合

Windows 用のファイルの回復スクリプトを実行しているときに、"***アタッチされているボリュームがない** _" ことを示すメッセージが表示されますが、ディスクはディスク管理コンソールで検出されます。 iSCSI を介してボリュームをアタッチするときに、検出されるいくつかのボリュームはオフライン状態に移行します。 iSCSI チャネルでは、VM とサービスの間で通信を行うときに、これらのボリュームが検出されてオンラインになりますが、マウントされません。

   ![ディスクがアタッチされていません](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

この問題を特定して解決するには、次の手順を行います。

1. コマンド ウィンドウで **diskmgmt.msc** コマンドを実行して、_ *[ディスクの管理]* * に移動します。
2. 追加のディスクが表示されているかどうかを確認します。 以下の例では、ディスク 2 が追加のディスクです。

   ![ディスクの管理 0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. **[ボリューム]** を右クリックし、 **[ドライブ文字とパスの変更]** を選択します。

   ![ディスクの管理 1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. **[ドライブ文字またはパスの追加]** ウィンドウで、 **[次のドライブ文字を割り当てる]** を選択し、使用可能なドライブを割り当てて **[OK]** をクリックします。 

   ![ディスクの管理 2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. エクスプローラーから、ドライブを表示できます。

   ![ディスクの管理 3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. 新しいボリュームがアタッチされるはずです。  

   ![ディスクがマウントされていません](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. エクスプローラーでは、スクリプトを再実行した後に新しいボリュームが表示されます。

   ![ディスクがマウントされていません 1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![ディスクがマウントされていません 2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Linux VM の場合 

- [スクリプトを実行するための適切なコンピューター](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)があることを確かめます。
- 保護された Linux VM で LVM や RAID アレイを使用している場合は、こちらの[記事](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)に一覧表示されている手順に従ってください。

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>マウントされたボリュームからファイルをコピーできません

コピーが次のエラー 0x80070780 で失敗する場合があります: システムからファイルにアクセスできません。 この場合は、ソース サーバーでディスクの重複除去が有効になっているかどうかを確認します。 その後、復元サーバーでも重複除去がドライブで有効になっていることを確かめます。 復元サーバーでドライブを重複除去しないように、重複除去の役割を未構成のままにすることができます。
