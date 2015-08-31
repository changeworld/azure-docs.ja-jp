<properties
   pageTitle="Windows Server または Windows クライアントのファイルとフォルダーを Azure にバックアップ | Microsoft Azure"
   description="Windows サーバーまたは Windows クライアントから Azure にバックアップする方法について説明します。"
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/13/2015" ms.author="jimpark"; "aashishr"/>

# Windows Server または Windows クライアントのファイルとフォルダーを Azure にバックアップする
この記事では、Windows Server または Windows クライアントのファイルおよびフォルダーを、Azure にバックアップする手順について説明します。

## 開始する前に
続行する前に、Microsoft Azure Backup を使用する場合の、Windows Server および Windows クライアントを保護するための[前提条件](backup-configure-vault.md#before-you-start)が満たされていることを確認します。前提条件は、バックアップ コンテナーの作成、コンテナー資格情報のダウンロード、Azure Backup エージェントのインストール、コンテナーへのサーバーの登録などのタスクに関するものです。

## ファイルのバックアップ
1. コンピューターが登録されたら、Microsoft Azure Backup mmc スナップインを開きます。

    ![Search result](./media/backup-azure-backup-windows-server/result.png)

2. [**バックアップのスケジュール**] をクリックします。

    ![Schedule Backup](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. バックアップする項目を選択します。Windows サーバーまたは Windows クライアントで Azure Buckup を使用して (つまり System Center Data Protection Manager を使用しない)、ファイルとフォルダーを保護することができます。

    ![Items to Backup](./media/backup-azure-backup-windows-server/items.png)

4. バックアップ スケジュールと保持ポリシーを指定します。保持ポリシーについては以降の[記事](backup-azure-backup-cloud-as-tape.md)で詳しく説明します。

5. 最初のバックアップを送信する方法を選択します。最初のシード処理を実施する方法は、バックアップするデータの量と、インターネットのアップロード リンク速度によって決まります。GB または TB 単位のデータを大きな遅延が発生する低帯域幅接続でバックアップする場合は、最寄りの Azure データ センターにディスクを送付して、最初のバックアップを完了することをお勧めします。これは "オフライン バックアップ" と呼ばれ、この[記事](backup-azure-backup-import-export.md)で詳しく説明されています。十分な帯域幅接続が確保できる場合は、ネットワーク経由で最初のバックアップを完了することをお勧めします。

    ![Initial Backup](./media/backup-azure-backup-windows-server/initialbackup.png)

6. スケジュール バックアップ プロセスが完了したら、mmc スナップインに戻り、[**今すぐバックアップ**] をクリックして、ネットワーク経由での最初のシード処理を完了します。

    ![Backup now](./media/backup-azure-backup-windows-server/backupnow.png)

7. 最初のシード処理が完了すると、Azure Backup コンソールの [**ジョブ**] ビューに状態が表示されます。

    ![IR complete](./media/backup-azure-backup-windows-server/ircomplete.png)

## 次のステップ
- [Windows Server または Windows クライアントの管理](backup-azure-manage-windows-server.md)
- [Azure からの Windows Server または Windows クライアントの復元](backup-azure-restore-windows-server.md)
- [Azure Backup FAQ](backup-azure-backup-faq.md)

<!---HONumber=August15_HO8-->