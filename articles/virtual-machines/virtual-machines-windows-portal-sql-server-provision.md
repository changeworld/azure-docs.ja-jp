<properties
	pageTitle="SQL Server 仮想マシンのプロビジョニング | Microsoft Azure"
	description="ポータルを使用して Azure で SQL Server 仮想マシンを作成し、接続します。このチュートリアルでは、Resource Manager モードを使用します。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	editor=""
	manager="jhubbard"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/24/2016"
	ms.author="jroth" />

# Azure ポータルでの SQL Server 仮想マシンのプロビジョニング

> [AZURE.SELECTOR]
- [ポータル](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

このエンド ツー エンドのチュートリアルでは、Azure ポータルを使用して、SQL Server を実行する仮想マシンをプロビジョニングする方法について説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

Azure 仮想マシン (VM) ギャラリーには、Microsoft SQL Server を含むイメージがいくつか用意されています。わずか数クリックで、ギャラリーからいずれかの SQL VM イメージを選択し、Azure 環境に VM をプロビジョニングできます。

このチュートリアルでは、次のことについて説明します。

- [ギャラリーから SQL VM イメージを選択する](#select-a-sql-vm-image-from-the-gallery)
- [VM を構成して作成する](#configure-the-vm)
- [リモート デスクトップを使用して VM を開く](#open-the-vm-with-remote-desktop)
- [SQL Server にリモート接続する](#connect-to-sql-server-remotely)

## ギャラリーから SQL VM イメージを選択する

1. アカウントを使用して [Azure ポータル](https://portal.azure.com)にログインします。

	>[AZURE.NOTE] Azure アカウントを持っていない場合は、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)にアクセスしてください。

1. Azure ポータルで、**[新規]** をクリックします。**[新規]** ブレードが開きます。SQL Server VM のリソースが、Marketplace の **[Virtual Machines]** グループに表示されます。

1. **[新規]** ブレードで、**[Virtual Machines]** をクリックします。

1. 利用可能なイメージをすべて表示するには、**[Virtual Machines]** ブレードの **[すべて表示]** をクリックします。

	![Azure Virtual Machines Blade](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. **[データベース サーバー]** の下にある **[SQL Server]** をクリックします。**[データベース サーバー]** を見つけるために下へスクロールする必要がある場合があります。使用可能な SQL Server テンプレートを確認します。

	![Virtual Machine Gallery SQL Images](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. 各テンプレートでは、SQL Server のバージョンとオペレーティング システムが示されています。一覧からこれらのイメージのいずれかを選択します。次に、仮想マシン イメージの説明を提供する詳細ブレードを確認します。

1. **[デプロイ モデルの選択]** で **[リソース マネージャー]** を選択していることを確認し、**[作成]** をクリックします。

	![Create SQL VM with Resource Manager](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## VM を構成する
SQL Server 仮想マシンを構成するための 5 つのブレードがあります。

| 手順 | 説明 |
|---------------------|-------------------------------|
| **基本** | [基本設定を構成する](#1-configure-basic-settings) |
| **サイズ** | [仮想マシンのサイズを選択する](#2-choose-virtual-machine-size) |
| **設定** | [オプション機能を構成する](#3-configure-optional-features) |
| **SQL Server の設定** | [SQL Server の設定を構成する](#4-configure-sql-server-settings) |
| **まとめ** | [概要を確認する](#5-review-the-summary) |

## 1\.基本設定を構成する
**[基本]** ブレードで次の情報を指定します。

* 仮想マシンの一意の**名前**を入力します。
* VM のローカル管理者アカウントの**ユーザー名**を指定します。このアカウントは、SQL Server の **sysadmin** 固定サーバー ロールにも追加されます。
* 強力な**パスワード**を指定します。
* 複数のサブスクリプションがある場合は、新しい VM に対してサブスクリプションが正しいことを確認します。
* **[リソース グループ]** ボックスに、新しいリソース グループの名前を入力します。または、既存のリソース グループを使用する場合は、**[既存のものを選択]** をクリックします。リソース グループとは、Azure の関連リソース (仮想マシン、ストレージ アカウント、仮想ネットワークなど) のコレクションです。

	>[AZURE.NOTE] Azure における SQL Server のデプロイについてテストまたは調査のみを実施する場合は、新しいリソース グループを使用すると便利です。テストが完了したら、リソース グループを削除します。そうすると、そのリソース グループに関連付けられている VM とすべてのリソースが自動的に削除されます。リソース グループの詳細については、「[Azure Resource Manager の概要](../resource-group-overview.md)」を参照してください。

* このデプロイの**場所**を選択します。
* **[OK]** をクリックして設定を保存します。

	![SQL Basics Blade](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## 2\.仮想マシンのサイズを選択する
この**サイズ**設定の手順では、**[サイズの選択]** ブレードで仮想マシンのサイズを選択します。ブレードには、選択したテンプレートに基づいて推奨されるマシン サイズが最初に表示されます。VM の実行にかかる毎月のコストも見積もられます。

![SQL VM Size Options](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

運用環境のワークロードには、[Premium Storage](../storage/storage-premium-storage.md) をサポートする仮想マシンのサイズを選択することをお勧めします。そのレベルのパフォーマンスが必要でない場合は、**[すべて表示]** ボタンを使用して、マシン サイズのオプションをすべて表示します。たとえば、開発またはテスト環境用には、小さいマシン サイズを使用することができます。

>[AZURE.NOTE] 仮想マシン サイズの詳細については、[仮想マシンのサイズ](virtual-machines-windows-sizes.md)に関するページを参照してください。SQL Server VM のサイズに関する考慮事項については、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」を参照してください。

マシン サイズを選択し、**[選択]** をクリックします。

## 3\.オプション機能を構成する
**[設定]** ブレードで、仮想マシン用に Azure Storage、ネットワーク、監視を構成します。

- **[Storage]** で、**ディスクの種類**として Standard または Premium (SSD) を指定します。運用環境のワークロードには Premium Storage をお勧めします。

>[AZURE.NOTE] Premium Storage をサポートしていないマシン サイズで Premium (SSD) を選択した場合は、マシン サイズが自動的に変更されます。

- **[ストレージ アカウント]** で、自動的にプロビジョニングされたストレージ アカウント名をそのまま使用できます。または、**[ストレージ アカウント]** をクリックして、既存のアカウントを選択し、ストレージ アカウントの種類を構成することもできます。既定では、ローカル冗長ストレージで新しいストレージ アカウントが作成されます。ストレージ オプションの詳細については、「[Azure Storage のレプリケーション](../storage/storage-redundancy.md)」を参照してください。

- **[ネットワーク]** で、自動的に設定された値をそのまま使用できます。または、各機能をクリックして、**仮想ネットワーク**、**サブネット**、**パブリック IP アドレス**、**ネットワーク セキュリティ グループ**を手動で構成することもできます。このチュートリアルでは、既定値をそのまま使用します。

- **[監視]** は、既定では VM に指定されているものと同じストレージ アカウントで有効になります。これらの設定はここで変更できます。

- **[可用性セット]** で、可用性セットを指定します。このチュートリアルでは、**[なし]** を選択します。SQL AlwaysOn 可用性グループを設定する場合は、仮想マシンを再作成しないように可用性を構成します。詳細については、「[Virtual Machines の可用性管理](virtual-machines-windows-manage-availability.md)」を参照してください。

これらの設定の構成が済んだら、**[OK]** をクリックします。

## 4\.SQL Server の設定を構成する
**[SQL Server の設定]** ブレードで、SQL Server の個々の設定と最適化を構成します。SQL Server について構成できる設定は次のとおりです。

| 設定 |
|---------------------|
| [接続](#connectivity) |
| [認証](#authentication) |
| [ストレージの構成](#storage-configuration) |
| [自動修正](#automated-patching) |
| [自動化されたバックアップ](#automated-backup) |
| [Azure Key Vault の統合](#azure-key-vault-integration) |

### 接続
**[SQL connectivity]** (SQL 接続) で、この VM 上の SQL Server インスタンスに必要なアクセスの種類を指定します。このチュートリアルでは、**[パブリック (インターネット)]** を選択して、インターネット上のマシンまたはサービスから SQL Server に接続できるようにします。このオプションを選択すると、ポート 1433 のトラフィックを許可するように、ファイアウォールとネットワーク セキュリティ グループが自動的に構成されます。

![SQL Connectivity Options](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

インターネット経由で SQL Server に接続するには、次のセクションで説明する SQL Server 認証を有効にする必要もあります。

>[AZURE.NOTE] SQL Server VM にネットワーク通信の制限をさらに追加することができます。これは、VM を作成した後に、ネットワーク セキュリティ グループを編集することで実行できます。詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)」を参照してください。

インターネット経由によるデータベース エンジンへの接続を有効にしない場合は、次のいずれかのオプションを選択します。

- **ローカル (VM 内のみ)**: VM 内から SQL Server への接続のみを許可します。
- **プライベート (Virtual Network 内)**: 同じ仮想ネットワーク内のマシンまたはサービスから SQL Server への接続を許可します。

一般的に、シナリオで許容される最も制限の厳しい接続を選択すると、セキュリティが向上します。ただし、ネットワーク セキュリティ グループの規則と SQL 認証または Windows 認証を使用すると、すべてのオプションをセキュリティで保護できます。

**[ポート]** の既定値は 1433 です。別のポート番号を指定できます。詳細については、「[SQL Server 仮想マシンへの接続 (リソース マネージャー) | Microsoft Azure](virtual-machines-windows-sql-connect.md)」を参照してください。

### 認証
SQL Server 認証が必要な場合は、**[SQL 認証]** の **[有効]** をクリックします。

![SQL Server 認証](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] インターネット経由で SQL Server にアクセスする場合 (つまりパブリック接続オプション)、ここで SQL 認証を有効にする必要があります。SQL Server へのパブリック アクセスでは、SQL 認証を使う必要があります。

SQL Server 認証を有効にする場合は、**[ログイン名]** と **[パスワード]** を指定します。このユーザー名が、SQL Server 認証ログインおよび **sysadmin** 固定サーバー ロールのメンバーとして構成されます。認証モードの詳細については、「[認証モードの選択](http://msdn.microsoft.com/library/ms144284.aspx)」を参照してください。

SQL Server 認証を有効にしない場合は、VM のローカル管理者アカウントを使用して SQL Server インスタンスに接続できます。

### ストレージの構成
ストレージの要件を指定するには、**[ストレージ構成]** をクリックします。

![SQL Storage Configuration](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Standard Storage を選択した場合は、このオプションは使用できません。ストレージの自動最適化は、Premium Storage でのみ使用できます。

1 秒間の入力/出力操作数 (IOP)、スループット (MB/秒)、およびストレージの合計サイズで、要件を指定できます。スライド スケールを使用してこれらを構成します。これらの要件に基づいて、ディスクの数が自動的に計算されます。

既定では、Azure はストレージを 5000 IOP、200 MB、1 TB の記憶域容量に最適化します。ワークロードに基づいて、これらのストレージ設定を変更できます。**[Storage optimized for]** (ストレージ最適化対象) で、次のいずれかのオプションを選択します。

- **[General]** (全般) は、既定の設定であり、ほとんどのワークロードをサポートします。
- **[Transactional]** (トランザクション) は、従来のデータベース OLTP ワークロード用にストレージを最適化します。
- **[Data warehousing]** (データ ウェアハウジング) は、分析とレポートのワークロード用にストレージを最適化します。

>[AZURE.NOTE] スライダーの上限値は、選択した仮想マシンのサイズによって異なります。

### 自動修正
**[自動修正]** は、既定で有効になります。自動修正を有効にすると、Azure は SQL Server とオペレーティング システムに修正プログラムを自動的に適用します。メンテナンス ウィンドウの曜日、時刻、および期間を指定します。Azure は、修正プログラムの適用をこのメンテナンス ウィンドウで実行します。メンテナンス ウィンドウのスケジュールでは、VM のロケールが時刻に使用されます。SQL Server とオペレーティング システムに修正プログラムを自動的に適用しない場合は、**[無効]** をクリックします。

![SQL Automated Patching](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

詳細については、[Azure Virtual Machines での SQL Server の自動修正](virtual-machines-windows-classic-sql-automated-patching.md)に関するページを参照してください。

### 自動バックアップ
**[自動バックアップ]** では、すべてのデータベースの自動データベース バックアップを有効にすることができます。既定では、自動バックアップは無効です。

SQL の自動バックアップを有効にするときは、以下の構成を行います。

- バックアップのリテンション期間 (日数)
- バックアップに使用するストレージ アカウント
- バックアップの暗号化オプションとパスワード

バックアップを暗号化するには、**[有効]** をクリックします。**パスワード**を指定します。Azure は、バックアップを暗号化するための証明書を作成し、指定されたパスワードを使用してその証明書を保護します。

![SQL Automated Backup](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 詳細については、「[Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-windows-classic-sql-automated-backup.md)」をご覧ください。

### Azure Key Vault の統合
暗号化のためのセキュリティ シークレットを Azure に格納するには、**[Azure key vault integration]** (Azure Key Vault の統合)、**[有効]** の順にクリックします。

![SQL Azure Key Vault Integration](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

次の表では、Azure Key Vault の統合の構成に必要なパラメーターを示します。

|パラメーター|説明|例|
|----------|----------|-------|
|**Key Vault の URL** |Key Vault の場所。|https://contosokeyvault.vault.azure.net/ |
|**プリンシパル名** |Azure Active Directory サービスのプリンシパル名。クライアント ID とも呼ばれます。 |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **プリンシパル シークレット**|Azure Active Directory サービスのプリンシパル シークレット。クライアント シークレットとも呼ばれます。 | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**資格情報名**|**資格情報名**: AKV 統合により SQL Server 内に資格情報が作成されます。VM に Key Vault にアクセスする許可が与えられます。この資格情報の名前を選択します。| mycred1|

詳細については、「[Azure VM で SQL Server 用に Azure Key Vault 統合を構成する](virtual-machines-windows-classic-ps-sql-keyvault.md)」を参照してください。

SQL Server の設定の構成が完了したら、**[OK]** をクリックします。

## 5\.概要を確認する
**[概要]** ブレードで概要を確認し、**[OK]** をクリックして、この VM に対して指定した SQL Server、リソース グループ、リソースを作成します。

Azure ポータルでデプロイメントを監視できます。画面の上部にある **[通知]** ボタンをクリックすると、デプロイの基本的な状態が表示されます。

>[AZURE.NOTE] デプロイの時間について参考になるように、既定の設定で SQL VM を米国東部リージョンにデプロイしました。このテスト デプロイには完了までに合計 26 分間かかりました。ただし、リージョンや選択した設定によっては、デプロイに必要な時間が変わる可能性があります。

## リモート デスクトップを使用して VM を開く

リモート デスクトップを使用して仮想マシンに接続するには、次の手順に従います。

1. Azure VM を作成すると、Azure ダッシュボードに VM のアイコンが表示されます。既存の仮想マシンでもアイコンを見つけることができます。新しい SQL 仮想マシンをクリックします。**[仮想マシン]** ブレードに、仮想マシンの詳細が表示されます。
1. **[仮想マシン]** ブレードの上部にある **[接続]** をクリックします。
1. ブラウザーで VM の RDP ファイルがダウンロードされます。RDP ファイルを開きます。![Remote Desktop to SQL VM](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. リモート デスクトップ接続で、このリモート接続の発行元が特定できないことが通知されます。**[接続]** をクリックして続行します。
1. **[Windows セキュリティ]** ダイアログで、**[別のアカウントを使用]** をクリックします。
1. **[ユーザー名]** に、**<user name>** を入力します。この <user name> は、VM の構成時に指定したユーザー名です。名前の先頭にバックスラッシュを追加する必要があります。
1. この VM 用に構成した**パスワード**を入力し、**[OK]** をクリックして接続します。
1. 別の **[リモート デスクトップ接続]** ダイアログ ボックスが表示され、接続するかどうかを確認するメッセージが表示されたら、**[はい]** をクリックします。

SQL Server 仮想マシンに接続した後は、SQL Server Management Studio を起動し、ローカル管理者の資格情報を使用して Windows 認証で接続できます。SQL Server 認証を有効にした場合は、プロビジョニングの間に構成した SQL のログインとパスワードを使用して SQL 認証で接続することもできます。

マシンにアクセスすると、要件に基づいてマシンと SQL Server の設定を直接変更することができます。たとえば、ファイアウォールの設定を構成したり、SQL Server の構成設定を変更したりできます。

## SQL Server にリモート接続する

このチュートリアルでは、仮想マシンと **SQL Server 認証**に**パブリック** アクセスを選択しています。これらの設定により、インターネット経由による任意のクライアントから SQL Server への接続を許可するように仮想マシンが自動的に構成されています (適切な SQL ログインを持っている場合)。

>[AZURE.NOTE] プロビジョニング時に [パブリック] を選択しなかった場合、インターネット経由で SQL Server インスタンスにアクセスするには、追加の手順が必要です。詳細については、[SQL Server 仮想マシンへの接続](virtual-machines-windows-sql-connect.md)に関するページを参照してください。

次のセクションでは、インターネット経由で別のコンピューターから VM の SQL Server インスタンスに接続する方法を示します。

> [AZURE.INCLUDE [VM リソース マネージャーで SQL Server に接続する](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## 次のステップ
Azure での SQL Server の使用に関するその他の情報については、[Azure Virtual Machines における SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) に関するページと[よく寄せられる質問](virtual-machines-windows-sql-server-iaas-faq.md)に関するページを参照してください。

Azure Virtual Machines 上の SQL Server のビデオの概要については、「[Azure VM is the best platform for SQL Server 2016 (Azure VM は SQL Server 2016 に最適なプラットフォーム)](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)」をご覧ください。

<!---HONumber=AcomDC_0525_2016-->