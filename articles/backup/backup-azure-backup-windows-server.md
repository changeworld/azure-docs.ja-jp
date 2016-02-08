<properties
   pageTitle="Windows Server または Windows クライアントのファイルとフォルダーを Azure にバックアップ | Microsoft Azure"
   description="この簡単な手順で Windows Server または Windows クライアントを Azure にバックアップします。数回の簡単なステップで Windows のファイルとフォルダーをクラウドにバックアップできます。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="Windows Server バックアップ、Windows Server をバックアップする"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="01/22/2016"
	 ms.author="jimpark;"/>

# Windows Server または Windows クライアントのファイルとフォルダーを Azure にバックアップする
この簡単な手順で Windows のファイルとフォルダーを Azure にバックアップできます。まだ実行していない場合は、[前提条件](backup-configure-vault.md#before-you-start)を完了して、Windows コンピューターをバックアップする環境を準備してから、操作を続行します。

## ファイルとフォルダーのバックアップ
1. コンピューターが登録されたら、Microsoft Azure Backup mmc スナップインを開きます。

    ![Search result](./media/backup-azure-backup-windows-server/result.png)

2. [**バックアップのスケジュール**] をクリックします。

    ![Windows Server のバックアップ スケジュールを設定します](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. バックアップする項目を選択します。Windows サーバーまたは Windows クライアントで Azure Backup を使用して (つまり System Center Data Protection Manager を使用しない)、ファイルとフォルダーを保護することができます。

    ![Windows Server のバックアップ項目](./media/backup-azure-backup-windows-server/items.png)

4. バックアップ スケジュールと保持ポリシーを指定します。保持ポリシーについては以降の[記事](backup-azure-backup-cloud-as-tape.md)で詳しく説明します。

5. 最初のバックアップを送信する方法を選択します。最初のシード処理を実施する方法は、バックアップするデータの量と、インターネットのアップロード リンク速度によって決まります。GB または TB 単位のデータを大きな遅延が発生する低帯域幅接続でバックアップする場合は、最寄りの Azure データ センターにディスクを送付して、最初のバックアップを完了することをお勧めします。これは "オフライン バックアップ" と呼ばれ、この[記事](backup-azure-backup-import-export.md)で詳しく説明されています。十分な帯域幅接続が確保できる場合は、ネットワーク経由で最初のバックアップを完了することをお勧めします。

    ![Windows Server の初回バックアップ](./media/backup-azure-backup-windows-server/initialbackup.png)

6. スケジュール バックアップ プロセスが完了したら、mmc スナップインに戻り、[**今すぐバックアップ**] をクリックして、ネットワーク経由での最初のシード処理を完了します。

    ![Windows Server を今すぐバックアップする](./media/backup-azure-backup-windows-server/backupnow.png)

7. 最初のシード処理が完了すると、Azure Backup コンソールの [**ジョブ**] ビューに状態が表示されます。

    ![IR complete](./media/backup-azure-backup-windows-server/ircomplete.png)

## 次のステップ
- [Windows Server または Windows クライアントの管理](backup-azure-manage-windows-server.md)
- [Azure からの Windows Server または Windows クライアントの復元](backup-azure-restore-windows-server.md)
- [Azure Backup FAQ](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0128_2016-->