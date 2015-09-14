<properties
	pageTitle="Azure Backup サービスを Windows Server のバックアップ用に構成する | Microsoft Azure"
	description="このチュートリアルを使用し、Microsoft の Azure クラウド ソリューションの Backup サービスを使って、Windows Server をクラウドにバックアップする方法について学習します。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="08/21/2015" ms.author="jimpark"; "aashishr"/>

# Azure Backup を Windows Server のバックアップ用に構成する

この記事では、Azure Backup 機能を有効にする手順について説明します。Windows Server または Windows クライアントを Azure にバックアップするには、Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
>[AZURE.NOTE]以前は、バックアップ サーバーを登録するために、X.509 v3 証明書の作成や取得が必要でした。証明書は現在でもサポートされていますが、サーバーへの Azure 資格情報コンテナーの登録を容易にするために、[クイック スタート] ページから資格情報コンテナーの資格情報を生成できます。

## 開始する前に
ファイルやデータを Windows Server から Azure にバックアップするには、まず次のことを行う必要があります。

- **バックアップ コンテナーの作成** — Azure Backup コンソールでコンテナーを作成します。
- **コンテナー資格情報のダウンロード** — Azure Backup で、コンテナーに対して作成した管理証明書をダウンロードします。
- **Azure Backup エージェントのインストールおよびサーバーの登録** — Azure Backup からエージェントをインストールし、バックアップ コンテナーにサーバーを登録します。

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## 次のステップ
- [Windows Server または Windows クライアントのバックアップ](backup-azure-backup-windows-server.md)
- [Windows Server または Windows クライアントの管理](backup-azure-manage-windows-server.md)
- [Azure からの Windows Server または Windows クライアントの復元](backup-azure-restore-windows-server.md)
- [Azure Backup FAQ](backup-azure-backup-faq.md)
- [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=September15_HO1-->