<properties
	pageTitle="VM の SQL Server への SQL Server データベースの移行 | Microsoft Azure"
	description="オンプレミスのユーザー データベースを Azure 仮想マシンの SQL Server に移行する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="carlrab"/>


# Azure VM の SQL Server への SQL Server データベースの移行

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


オンプレミスの SQL Server ユーザー データベースを Azure VM の SQL Server に移行する方法は多数あります。この記事では、さまざまな方法について簡単に説明したうえで、さまざまなシナリオに最適な方法を紹介します。また、**Microsoft Azure VM への SQL Server データベースのデプロイ** ウィザードを使用する方法について順を追って説明する[チュートリアル](#azure-vm-deployment-wizard-tutorial)も含まれています。

## 主な移行方法

主な移行方法は次のとおりです。

- Microsoft Azure VM への SQL Server Database のデプロイ ウィザードを使用する
- 圧縮機能を使用してオンプレミスのバックアップの実行し、そのバックアップ ファイルを Azure 仮想マシンに手動でコピーする
- URL へのバックアップを実行し、その URL から Azure 仮想マシンに復元する
- データとログ ファイルをデタッチしてから、Azure BLOB ストレージにコピーし、その後、URL から Azure VM の SQL Server にアタッチする
- オンプレミスの物理マシンを HYPER-V VHD に変換して Azure BLOB ストレージにアップロードし、アップロードしたその VHD を使用して、新しい VM としてデプロイする
- Windows の Import/Export サービスを使用して、ハード ドライブを発送する
- オンプレミスの AlwaysOn デプロイがある場合は、[Azure のレプリカ追加ウィザード](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)を使って Azure でレプリカを作成した後、フェールオーバーして、ユーザーに Azure データベース インスタンスを参照させる
- SQL Server の[トランザクション レプリケーション](https://msdn.microsoft.com/library/ms151176.aspx)を使用して Azure SQL Server インスタンスをサブスクライバーとして構成した後、レプリケーションを無効にして、ユーザーに Azure データベース インスタンスを参照させる



## 移行方法の選択

最適なデータ転送パフォーマンスを実現するために、Azure VM へのデータベース ファイルの移行は、一般的には圧縮されたバックアップ ファイルを使って行うのがベストです。この方法は、[Microsoft Azure VM への SQL Server データベースのデプロイ ウィザード](#azure-vm-deployment-wizard-tutorial)で使用されます。このウィザードは、圧縮されたデータベースのバックアップ ファイルが 1 TB より小さいとき、SQL Server 2005 以降で実行されているオンプレミスのユーザー データベースを、SQL Server 2014 以降に移行する際に使用することをお勧めします。

データベース移行プロセス中のダウンタイムを最小限に抑えるには、AlwaysOn の方法またはトランザクション レプリケーションの方法を使います。

上記の方法を使用できない場合は、手動でデータベースを移行します。この方法を使うときは、一般に、まずデータベースをバックアップしてから、データベースのバックアップを Azure にコピーした後、データベースの復元を実行します。データベース ファイル自体を Azure にコピーしてからアタッチしてもかまいません。この手動による Azure VM へのデータベースの移行プロセスは、複数の方法で実行できます。

> [AZURE.NOTE] 以前のバージョンの SQL Server から SQL Server 2014 または SQL Server 2016 にアップグレードする場合は、変更が必要かどうかを検討する必要があります。移行プロジェクトの一環として、新しいバージョンの SQL Server でサポートされない機能への依存関係すべてを解決することをお勧めします。サポートされるエディションとシナリオの詳細については、[SQL Server へのアップグレード](https://msdn.microsoft.com/library/bb677622.aspx)に関するページを参照してください。

次の表に、主な移行方法の一覧を示します。ここでは、それぞれの方法を使用するタイミングとして最適な状況についても説明します。

| メソッド | 移行元データベースのバージョン | 移行先データベースのバージョン | 移行元データベースのバックアップ サイズ制限 | メモ |
|---|---|---|---|---|
| [Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードを使用する](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 以降 | SQL Server 2014 以降 | > 1 TB | 最も高速で簡単な方法。Azure 仮想マシンの新規または既存の SQL Server インスタンスに移行するときにいつでも使用できます。 | 
| [Azure レプリカの追加ウィザードを使用する](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md) | SQL Server 2012 以降 | SQL Server 2012 以降 | [Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | ダウンタイムが最小限になります。AlwaysOn のオンプレミスのデプロイがあるときに使います。 |
| [SQL Server のトランザクション レプリケーションを使用する](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 以降 | SQL Server 2005 以降 | [Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | ダウンタイムを最小限にする必要があり、AlwaysOn のオンプレミスのデプロイがないときに使います。 |
| [圧縮機能を使用してオンプレミスのバックアップの実行し、そのバックアップ ファイルを Azure 仮想マシンに手動でコピーする](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 以降 | SQL Server 2005 以降 | [Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | ウィザードを使用できない場合にのみ使用。たとえば、移行先データベースのバージョンが SQL Server 2012 SP1 CU2 より以前の場合や、データベースのバックアップ サイズが 1 TB (SQL Server 2016 の場合は 12.8 TB) を超えている場合は、ウィザードを使用できません。 |
| [URL へのバックアップを実行し、その URL から Azure 仮想マシンに復元する](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 以上 | SQL Server 2012 SP1 CU2 以上 | > 1 TB (SQL Server 2016 の場合は < 12.8 TB) | 一般的に、[URL へのバックアップ](https://msdn.microsoft.com/library/dn435916.aspx)は、パフォーマンスについてはウィザードを使用する場合と同等で、それほど簡単ではありません。 |
| [データとログ ファイルをデタッチしてから、Azure BLOB ストレージにコピーし、その後、URL から Azure 仮想マシンの SQL Server にアタッチする](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 以降 | SQL Server 2014 以降 | [Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | この方法は、特にデータベースのサイズが非常に大きい場合に、[Azure BLOB ストレージ サービスを使用してこれらのファイルを格納](https://msdn.microsoft.com/library/dn385720.aspx)し、Azure VM で実行されている SQL Server にファイルをアタッチするときに使用します。 |
| [オンプレミスのマシンを HYPER-V VHD に変換して Azure BLOB ストレージにアップロードし、アップロードしたその VHD を使用して、新しい仮想マシンをデプロイする](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 以降 | SQL Server 2005 以降 | [Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | [ご自身の SQL Server ライセンスを使用する](../data-management-azure-sql-database-and-sql-server-iaas/)場合、古いバージョンの SQL Server で実行するデータベースを移行する場合、または他のユーザー データベースやシステム データベースに応じて、データベースの移行の一環として、データベース システムとユーザー データベースを一緒に移行する場合に使用します。 |
| [Windows の Import/Export サービスを使用して、ハード ドライブを発送する](#ship-hard-drive) | SQL Server 2005 以降 | SQL Server 2005 以降 | [Azure VM ストレージの制限](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | [Windows の Import/Export サービス](../storage-import-export-service/)は、データベースのサイズが非常に大きい場合など、手動でのコピーが遅すぎるときに使用します。 |

## Azure VM のデプロイ ウィザードのチュートリアル

Microsoft SQL Server Management Studio の "**Microsoft Azure VM への SQL Server データベースのデプロイ** ウィザード" を使用して、SQL Server 2005、SQL Server 2008、SQL Server 2008 R2、SQL Server 2012、SQL Server 2014、または SQL Server 2016 のオンプレミスのユーザー データベース (最大 1 TB) を、Azure 仮想マシンの SQL Server 2014 または SQL Server 2016 に移行します。このウィザードを使用すると、ユーザー データベースが、既存の Azure 仮想マシン、または移行プロセス中にウィザードによって作成された SQL Server が含まれる Azure VM のいずれかに移行されます。データベースを新しいバージョンの SQL Server に移行すると、そのデータベースは、プロセス中に自動的にアップグレードされます。

### 最新の Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードを入手する

最新の **Microsoft Azure VM への SQL Server データベースのデプロイ** ウィザードを確実に入手するには、最新の Microsoft SQL Server Management Studio for SQL Server を使用します。このウィザードの最新バージョンには、Azure クラシック ポータルの最新の更新プログラムが組み込まれており、ギャラリーの最新の Azure VM イメージがサポートされます (以前のバージョンのウィザードは機能しないことがあります)。最新の Microsoft SQL Server Management Studio for SQL Server を入手するには、[これをダウンロード](http://go.microsoft.com/fwlink/?LinkId=616025)して、移行するデータベースとインターネットに接続されているクライアント コンピューターにインストールします。

### 既存の Azure 仮想マシンと SQL Server インスタンスを構成する (該当する場合)

既存の Azure VM に移行するには、次の構成手順を実行します。

- 「[Azure での SQL Server 仮想マシンのプロビジョニング](../virtual-machines-provision-sql-server/#SSMS)」の「別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する」の手順に従って、Azure VM と SQL Server インスタンスを構成して、別のコンピューターからの接続を有効にします。ウィザードを使用して移行する場合、サポートされるのは、ギャラリーの SQL Server 2014 および SQL Server 2016 のイメージのみです。
- Microsoft Azure ゲートウェイの SQL Server クラウド アダプター サービスのオープン エンドポイントを、プライベート ポート 11435 で構成します。このポートは、Microsoft Azure VM における SQL Server 2014 または SQL Server 2016 のプロビジョニングの一環として作成されます。また、クラウド アダプターでは、既定のポート 11435 での着信 TCP 接続を許可する Windows ファイアウォール ルールも作成されます。このエンドポイントにより、ウィザードがクラウド アダプター サービスを利用して、バックアップ ファイルをオンプレミスのインスタンスから Azure VMにコピーできます。詳細については、「[SQL Server のクラウド アダプター](https://msdn.microsoft.com/library/dn169301.aspx)」をご覧ください。

	![クラウド アダプターのエンドポイントの作成](./media/virtual-machines-migrate-onpremises-database/cloud-adapter-endpoint.png)

### Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードを実行する

1. Microsoft SQL Server Management Studio for Microsoft SQL Server 2016 を開き、Azure VMに移行するユーザー データベースを含む SQL Server のインスタンスに接続します。
2. 移行するデータベースを右クリックし、[タスク] をポイントして、[Microsoft Azure VM にデプロイする] をクリックします。

	![ウィザードの開始](./media/virtual-machines-migrate-onpremises-database/start-wizard.png)

3. [説明] ページで [次へ] をクリックします。
4. [ソース設定] ページで、Azure VM に移行するデータベースを含む SQL Server インスタンスに接続します。
5. バックアップ ファイルの一時的な場所を指定します。リモート サーバーに接続する場合は、ネットワーク ドライブを指定する必要があります。

	![移行元の設定](./media/virtual-machines-migrate-onpremises-database/source-settings.png)

6. [次へ] をクリックします。
7. [Microsoft Azure ログイン] ページで、[ログイン] をクリックし、Azure アカウントにログインします。
8. 使用するサブスクリプションを選択し、[次へ] をクリックします。

	![Azure のサインイン](./media/virtual-machines-migrate-onpremises-database/azure-signin.png)

9. [デプロイの設定] ページでは、新規または既存のクラウド サービス名と仮想マシン名を指定できます。

 - 新しいクラウド サービス名と仮想マシン名を指定し、SQL Server 2014 ギャラリーまたは SQL Server 2016 ギャラリーのイメージを使用して、新しい Azure 仮想マシンで新しいクラウド サービスを作成します。

     - 新しいクラウド サービス名を指定する場合は、使用するストレージ アカウントを指定します。

     - 既存のクラウド サービス名を指定する場合は、ストレージ アカウントが取得され、入力されます。

 - 既存のクラウド サービス名と新しい仮想マシン名を指定して、既存のクラウド サービスで新しい Azure 仮想マシンを作成します。SQL Server 2014 ギャラリーまたは SQL Server 2016 ギャラリーのイメージのみを指定します。
 - 既存のクラウド サービス名と仮想マシン名を指定して、既存の Azure 仮想マシンを使用します。これには、SQL Server 2014 ギャラリーまたは SQL Server 2016 ギャラリーのイメージを使用して作成されたイメージが必要です。

		![Deploymnent Settings](./media/virtual-machines-migrate-onpremises-database/deployment-settings.png)

10. [設定] のクリック
  - 既存のクラウド サービス名と仮想マシン名を指定した場合は、ユーザー名とパスワードを入力するように求められます。

		![Azure machine settings](./media/virtual-machines-migrate-onpremises-database/azure-machine-settings.png)

	- 新しい仮想マシン名を指定した場合は、ギャラリーのイメージの一覧からイメージを選択し、次の情報を入力するように求められます。
	  - イメージ - SQL Server 2014 または SQL Server 2016 のみを選択
		- ユーザー名
		- 新しいパスワード
		- パスワードの確認
		- 場所
		- サイズ
 	- また、[この新しい Microsoft Azure 仮想マシンに対して自動生成された証明書を使用する] チェック ボックスをオンにして、[OK] をクリックします。

	![Azure の新しいコンピューターの設定](./media/virtual-machines-migrate-onpremises-database/azure-new-machine-settings.png)

11. 移行先データベース名を指定します (移行元データベース名と異なる場合)。移行先データベースが既に存在する場合、既存のデータベースは上書きされず、データベース名が自動的にインクリメントされます。
12. [次へ] をクリックし、[完了] をクリックします。

	![結果](./media/virtual-machines-migrate-onpremises-database/results.png)

13. ウィザードが完了したら、仮想マシンに接続し、データベースが移行されていることを確認します。
14. 新しい仮想マシンを作成した場合は、「[Azure での SQL Server 仮想マシンのプロビジョニング](../virtual-machines-provision-sql-server/#SSMS)」の「別のコンピューターにある SQL Server Management Studio を使用して仮想マシンに接続するための構成手順を完了する」の手順に従って、Azure 仮想マシンと SQL Server インスタンスを構成します。

## ファイルへのバックアップと VM へのコピーおよび復元

SQL Server 2014 より前の SQL Server に移行する場合、またはバックアップ ファイルが 1 TB を超える場合、Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードは使用できません。この方法は、このような場合に使用します。VM ディスクの最大サイズは 1 TB であるため、バックアップ ファイルが 1 TB を超える場合、そのファイルはストライプする必要があります。この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1.	オンプレミスの場所へのデータベースの完全バックアップを実行します。
2.	必要な SQL Server バージョンを使用して、仮想マシンを作成またはアップロードします。
3.	「[Azure での SQL Server 仮想マシンのプロビジョニング](../virtual-machines-provision-sql-server/#SSMS)」の手順を使用して、仮想マシンをプロビジョニングします。
4.	リモート デスクトップ、Windows エクスプローラー、またはコマンド プロンプトのコピー コマンドを使用して、バックアップ ファイルを VM にコピーします。

## URL へのバックアップと復元

バックアップ ファイルが 1 TB を超える場合、SQL Server 2016 から移行する場合、および SQL Server 2016 に移行する場合、Microsoft Azure VM への SQL Server データベースのデプロイ ウィザードは使用できません。[URL へのバックアップ](https://msdn.microsoft.com/library/dn435916.aspx)方法は、このような場合に使用します。データベースが 1 TB 未満の場合、または SQL Server 2016 より前のバージョンの SQL Server を実行している場合は、ウィザードを使用することをお勧めします。SQL Server 2016 では、ストライプ バックアップ セットがサポートされています。このバックアップ セットは、パフォーマンスを確保するために使用することが推奨されます。また、BLOB ごとのサイズ制限を超える場合は必須です。データベースのサイズが非常に大きい場合は、[Windows の Import/Export サービス](../storage-import-export-service/)をお勧めします。

## デタッチして URL をコピーし、URL からアタッチ

この方法は、特にデータベースのサイズが非常に大きい場合に、[Azure BLOB ストレージ サービスを使用してこれらのファイルを格納](https://msdn.microsoft.com/library/dn385720.aspx)し、Azure VM で実行されている SQL Server にファイルをアタッチするときに使用します。この手動による方法を使用してユーザー データベースを移行するには、次の一般的な手順を実行します。

1.	オンプレミスのデータベース インスタンスからデータベース ファイルをデタッチします。
2.	デタッチされたデータベース ファイルを、[AZCopy コマンド ライン ユーティリティ](../storage-use-azcopy/)を使用して Azure BLOB ストレージにコピーします。
3.	データベース ファイルを、Azure URL から Azure VM の SQL Server のインスタンスにアタッチします。

## VM に変換して URL にアップロードし、新しい VM としてデプロイ

この方法は、オンプレミスの SQL Server インスタンスのすべてのシステムとユーザー データベースを、Azure 仮想マシンに移行するときに使用します。この手動による方法を使用して SQL Server インスタンス全体を移行するには、次の一般的な手順を実行します。

1.	[Microsoft Virtual Machine Converter](http://technet.microsoft.com/library/dn873998.aspx) を使用して、物理マシンまたは仮想マシンを HYPER-V VHD に変換します。
2.	[Add-AzureVHD コマンドレット](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)を使用して、VHD ファイルを Azure Storage にアップロードします。
3.	アップロードした VHD を使用して、新しい仮想マシンをデプロイします。

> [AZURE.NOTE] アプリケーション全体を移行するには、[Azure Site Recovery](../services/site-recovery/) を使用することを検討します。

## ハード ドライブの発送

ネットワーク経由のアップロードが実現不可能であるか、非常にコストがかかる場合には、[Windows の Import/Export サービス方法](../storage-import-export-service/)を使用して、ファイルの大量のデータを Azure BLOB ストレージに転送します。このサービスでは、そのデータを含む 1 台以上のハード ドライブを Azure データ センターに発送します。このデータ センターでデータがストレージ アカウントにアップロードされます。

## 次のステップ

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-sql-server-infrastructure-services.md)」を参照してください。

<!---HONumber=AcomDC_0128_2016-->