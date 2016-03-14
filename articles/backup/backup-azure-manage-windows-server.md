<properties
	pageTitle="Azure Backup コンテナーとサーバーの管理 | Microsoft Azure"
	description="このチュートリアルでは、Azure Backup コンテナーとサーバーを管理する方法について説明します。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="jimpark; markgal"/>


# Azure Backup コンテナーとサーバーの管理
この記事では、管理ポータルで使用できるバックアップ管理タスクの概要を説明します。

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。
2. **[復旧サービス]** をクリックし、バックアップ資格情報コンテナーの名前をクリックして、[クイック スタート] ページを表示します。

    [クイック スタート] ページの上部にあるオプションを選択すると、利用可能な管理タスクを確認できます。

    ![保護された項目](./media/backup-azure-manage-windows-server/RS_tabs.png)

## ダッシュボード
**[ダッシュボード]** を選択すると、サーバーの使用状況の概要が表示されます。[ダッシュボード] の下部で、次のタスクを実行できます。

- **証明書の管理**。証明書を使用してサーバーを登録した場合は、これを使用して証明書を更新します。資格情報コンテナーの資格情報を使用している場合は、**[証明書の管理]** を使用しないでください。
- **削除**。現在のバックアップ コンテナーを削除します。バックアップ コンテナーがもう使用されていない場合は、そのコンテナーを削除してストレージ領域を解放できます。**[削除]** は、登録されているサーバーすべてが資格情報コンテナーから削除された場合にのみ有効になります。
- **資格情報コンテナーの資格情報**。[概要] メニュー項目を使用して資格情報コンテナーの資格情報を構成します。

## 保護された項目
**[保護された項目]** を選択すると、サーバーからバックアップされた項目が表示されます。この一覧は、情報の提供のみを目的としています。

![保護された項目](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## 登録済みの項目
**[登録済みの項目]** を選択すると、このコンテナーに登録されたサーバーの名前が表示されます。

![削除されたサーバー](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

ここでは、次のタスクを実行できます。

- **再登録を許可** - サーバーに対してこのオプションが選択されている場合、エージェントで**登録ウィザード**を使用して、サーバーをバックアップ コンテナーにもう一度登録できます。証明書でエラーが発生したとき、またはサーバーを再構築する必要があった場合などに、再登録が必要になる可能性があります。
- **削除** - バックアップ コンテナーからサーバーを削除します。サーバーに関連付けられている保存されたデータすべてがすぐに削除されます。

## 次のステップ
- [Azure からの Windows Server または Windows クライアントの復元](backup-azure-restore-windows-server.md)
- Azure Backup の詳細については、「[Azure Backup の概要](backup-introduction-to-azure-backup.md)」を参照してください。
- [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。

<!---HONumber=AcomDC_0302_2016-->