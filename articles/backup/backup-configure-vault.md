<properties
	pageTitle="Windows Server または Windows クライアントをバックアップするための環境の準備 | Microsoft Azure"
	description="バックアップ資格情報コンテナーを作成し、資格情報をダウンロードして、バックアップ エージェントをインストールすることにより、Windows をバックアップするための環境を準備します。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="バックアップ資格情報コンテナー; バックアップ エージェント; Windows のバックアップ;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Windows コンピューターをバックアップする環境の準備
この記事では、Windows Server または Windows クライアントを Azure にバックアップするための準備について説明します。そのためには、Azure アカウントが必要です。アカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

前提条件を満たしている場合は、[Windows コンピューターのバックアップ](backup-azure-backup-windows-server.md)を開始できます。満たしていない場合は、次の手順で環境を準備してください。

>[AZURE.NOTE] 以前は、バックアップ サーバーを登録するために、X.509 v3 証明書の作成や取得が必要でした。証明書は現在でもサポートされていますが、サーバーへの Azure 資格情報コンテナーの登録を容易にするために、[クイック スタート] ページから資格情報コンテナーの資格情報を作成できます。

## 開始する前に
ファイルやデータを Windows Server から Azure にバックアップするには、まず次のことを行う必要があります。

- **バックアップ コンテナーの作成** — [Azure Backup 管理ポータル](http://manage.windowsazure.com)でコンテナーを作成します。
- **コンテナー資格情報のダウンロード** — Azure Backup コンテナーの [ダッシュボード] ページから、バックアップ コンテナーに Windows コンピューターを登録する際に使用するコンテナー資格情報をダウンロードします。
- **Azure Backup エージェントのインストールとサーバーの登録** — [ダッシュボード] ページで、[Azure Backup エージェント](http://aka.ms/azurebackup_agent)をダウンロードするためのリンクをクリックします。エージェントをインストールし、コンテナー資格情報を使用してサーバーをバックアップ コンテナーに登録します。

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## 次のステップ
- [Windows Server または Windows クライアントのバックアップ](backup-azure-backup-windows-server.md)
- [Windows Server または Windows クライアントの管理](backup-azure-manage-windows-server.md)
- [Azure からの Windows Server または Windows クライアントの復元](backup-azure-restore-windows-server.md)
- [Azure Backup FAQ](backup-azure-backup-faq.md)
- [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0128_2016-->