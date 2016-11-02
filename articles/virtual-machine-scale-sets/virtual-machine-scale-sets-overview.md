<properties
	pageTitle="仮想マシン スケール セットの概要 | Microsoft Azure"
	description="仮想マシン スケール セットについて説明します。"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/>

# 仮想マシン スケール セットの概要

仮想マシン スケール セットは、同一の VM のセットをデプロイおよび管理するために使用できる Azure 計算リソースです。すべての VM が同じ構成になっており、VM を事前にプロビジョニングする必要がない、真の自動スケールをサポートするように設計されています。そのため、ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロードなどを対象にした大規模サービスを簡単に構築できます。

コンピューティング リソースをスケール アウトしたりスケール インしたりする必要のあるアプリケーションでは、複数の障害ドメインと更新ドメインに対してスケール操作が暗黙的にバランシングされます。VM スケール セットの概要については、[Azure ブログでの発表](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)を参照してください。

以下のビデオで、VM スケール セットが詳しく解説されています。

 - [Mark Russinovich が語る Azure スケール セット](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)

 - [仮想マシン スケール セットを Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## VM スケール セットの作成と管理

[Azure ポータル](https://portal.azure.com)で VM スケール セットを作成するには、_[新規]_ を選択して、検索バーに「スケール」と入力します。検索結果として、"仮想マシン スケール セット" が表示されます。そこから必要なフィールドを入力し、スケール セットをカスタマイズしてデプロイします。

VM スケール セットは、個々の Azure Resource Manager VM と同様に、JSON テンプレートと [REST API](https://msdn.microsoft.com/library/mt589023.aspx) を使用して、定義およびデプロイすることもできます。そのため、Azure リソース マネージャーの標準的なデプロイ方法を利用することができます。テンプレートの詳細については、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」をご覧ください。

VM スケール セットのサンプル テンプレートは、[こちら](https://github.com/Azure/azure-quickstart-templates)の Azure Quickstart templates GitHub リポジトリから入手できます (タイトルに _vmss_ が付いているテンプレートを探してください)

これらのテンプレートの詳細ページには、ポータルのデプロイ機能にリンクしているボタンが表示されます。VM スケール セットをデプロイするには、このボタンをクリックし、ポータルで必要なすべてのパラメーターを指定します。リソースで大文字がサポートされているかどうかが不明な場合は、常に小文字のパラメーター値を使用する方が安全です。以下のサイトで、VM スケール セット テンプレートについてやさしく解説したビデオをご覧いただけます。

[VM スケール セット テンプレートの解説](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## VM スケール セットのスケール アウトとスケール イン

VM スケール セット内の仮想マシンの数を増減するには、単に _capacity_ プロパティを変更し、テンプレートを再デプロイします。このように単純なので、Azure 自動スケールでサポートされていないカスタム スケール イベントを定義する場合に、簡単に独自のカスタム スケール層を記述できます。

容量を変更するためにテンプレートを再デプロイする場合は、SKU と更新された容量だけが含まれている、非常に小さいテンプレートを定義できます。その例は[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)にあります。

自動的にサイズを増減するスケール セットの作成手順については、「[仮想マシン スケール セットでのマシンの自動スケール](virtual-machine-scale-sets-windows-autoscale.md)」を参照してください。

## VM スケール セットの監視

[Azure ポータル](https://portal.azure.com)には、一連のスケール セットと基本的なプロパティが表示されるほか、スケール セットに含まれる VM が一覧表示されます。さらに詳しい情報については、[Azure リソース エクスプローラー](https://resources.azure.com)を使用して VM スケール セットを表示してください。VM スケール セットは、Microsoft.Compute の下のリソースなので、このサイトから以下のリンクを展開すると表示できます。

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## VM スケール セットのシナリオ

このセクションでは、標準的な VM スケール セットのシナリオをいくつか紹介します。一部の高レベルの Azure サービス (Batch、Service Fabric、Azure Container Service など) も、これらのシナリオを使用します。

 - **VM スケール セット インスタンスへの RDP/SSH** - VM スケール セットは VNET の中に作成され、スケール セット内の個々の VM にはパブリック IP アドレスが割り当てられていません。これは、望ましいことです。通常は、コンピューティング グリッド内のすべてのステートレス リソースに個別のパブリック IP アドレスを割り当てるための費用や管理オーバーヘッドを避けたいためです。また、これらの VM には VNET 内の他のリソース (ロード バランサー、スタンドアロン仮想マシンなど、パブリック IP アドレスを持つリソース) から簡単に接続できます。

 - **NAT 規則を使用した VM への接続** - パブリック IP アドレスを作成し、ロード バランサーに割り当て、IP アドレス上のポートを VM スケール セット内の VM 上のポートにマッピングする受信 NAT 規則を定義することができます。次に例を示します。
 
	から | 発信元ポート | 変換先 | 宛先ポート
	--- | --- | --- | ---
	パブリック IP | ポート 50000 | vmss\_0 | ポート 22
	パブリック IP | ポート 50001 | vmss\_1 | ポート 22
	パブリック IP | ポート 50002 | vmss\_2 | ポート 22

	1 つのパブリック IP を使用しているスケール セット内のすべての VM への SSH 接続を NAT 規則で有効にする VM スケール セットの作成例は、[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat) にあります。

	同じことを RDP と Windows で行う例は、[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) にあります。

 - **"Jumpbox" を使用して VM に接続** - VM スケール セットとスタンドアロン VM を同じ VNET 内に作成する場合、スタンドアロン VM と VM スケール セット VM は、VNET/サブネットによって定義された内部 IP アドレスを使用して、互いに接続することができます。パブリック IP アドレスを作成し、スタンドアロン VM に割り当てる場合は、スタンドアロン VM に RDP または SSH 接続して、そのコンピューターから VM スケール セット インスタンスに接続できます。これでお気付きかもしれませんが、単純な VM スケール セットは、本質的に、既定の構成のパブリック IP アドレスを持つ単純なスタンドアロン VM よりも安全です。

	[この方法の例として、次のテンプレートは、VM のスケール セット ベースのクラスターを管理するスタンドアロン マスター VM で構成される単純な Mesos クラスターを作成します。](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **VM スケール セット インスタンスに対する負荷分散** - "ラウンド ロビン" 方式を使用して VM のコンピューティング クラスターに作業を割り当てる場合は、それに応じた負荷分散規則で Azure Load Balancer を構成できます。指定されたプロトコル、間隔、および要求パスでポートを ping して、アプリケーションが実行されていることを確認するためのプローブを定義することもできます。また Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) はスケール セットをサポートし、より洗練された負荷分散シナリオに対応します。

	[以下に、IIS Web サーバーを実行している VM の VM スケール セットを作成し、各 VM が受け取る負荷を分散するロード バランサーを使用する例を示します。この例では、各 VM の特定の URL を ping するために、HTTP プロトコルも使用します。](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json)(Microsoft.Network/loadBalancers というリソースの種類と、virtualMachineScaleSet の networkProfile および extensionProfile を見てください)

 - **PaaS クラスター マネージャーでのコンピューティング クラスターとしての VM スケール セットのデプロイ** - VM スケール セットは、次世代の worker ロールとして説明されることがあります。有効な説明ですが、スケール セットの機能を PaaS v1 worker ロール機能と混同させる危険性があります。ある意味で、VM スケール セットは、真の "worker ロール" または worker リソースを提供します。これにより、プラットフォーム/ランタムに依存せず、カスタマイズ可能で、Azure リソース マネージャー IaaS に統合された、汎用コンピューティング リソースを利用できます。

	PaaS v1 worker ロールは、プラットフォーム/ランタイムのサポートの面では制限がありますが (Windows プラットフォーム イメージのみ)、VIP スワップ、構成可能なアップグレード設定、ランタイム/アプリ デプロイ固有の設定などのサービスも提供しています。これらのサービスは、VM スケール セットでは_まだ_利用できないか、Service Fabric などの他の高レベル PaaS サービスによって提供されます。このような点を踏まえておけば、VM スケール セットは PaaS をサポートするインフラストラクチャであると見なすことができます。つまり、Service Fabric のような PaaS ソリューションや Mesos のようなクラスター マネージャーを、スケーラブルなコンピューティング層として VM スケール セット上に構築できます。

	[この方法の例として、次のテンプレートは、VM のスケール セット ベースのクラスターを管理するスタンドアロン マスター VM で構成される単純な Mesos クラスターを作成します。](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) 将来のバージョンの [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) では、VM スケール セットに基づいて、このシナリオの、より複雑で強化されたバージョンがデプロイされます。

## VM スケール セットのパフォーマンスとスケールのガイダンス

- 複数の VM スケール セット内に 500 個を超える VM を同時に作成しないでください。
- ストレージ アカウントあたりの VM が 20 個を超えないように計画してください (_overprovision_ プロパティを "false" に設定する場合を除く。設定した場合は、最大で 40 個まで可能)。
- 各ストレージ アカウント名の最初の文字は、できるだけ重複しないようにします。[Azure のクイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/)のサンプル VMSS テンプレートには、その方法の例が紹介されています。
- カスタム VM を使用している場合は、1 つのストレージ アカウントで、VM スケール セットあたり 40 個以下の VM を計画します。VM スケール セットのデプロイを開始する前に、イメージをストレージ アカウントに事前コピーする必要があります。詳細については、FAQ を参照してください。
- VNET あたり 4,096 個以下の VM を計画します。
- 作成できる VM の数は、デプロイ先のリージョンのコア クォータによって制限されます。計算クォータの制限を緩和するには、現時点でクラウド サービスや IaaS v1 で使用するためのコアの上限が高い場合でも、カスタマー サポートへの連絡が必要である場合があります。クォータを照会するには、Azure CLI コマンドの場合は `azure vm list-usage`、PowerShell コマンドの場合は `Get-AzureRmVMUsage` (1.0 以前のバージョンの PowerShell を使用している場合は `Get-AzureVMUsage`) を実行します。

## VM スケール セットに関してよく寄せられる質問

**Q.** VM スケール セットには何個の VM を設定できますか?

**A.** 複数のストレージ アカウントに分散できるプラットフォーム イメージを使用する場合は、100 個です。カスタム イメージを使用する場合は、最大で 40 個です (_overprovision_ プロパティが "false" に設定される場合は、既定で 20 個)。現在は、カスタム イメージが 1 つのストレージ アカウントに制限されているためです。

**Q** VM スケール セットには、他にどのようなリソース制限がありますか?

**A.** 10 分の間に、リージョンごとに複数のスケール セットで 500 個を超える VM を作成できないという制限があります。既存の [Azure サブスクリプション サービス制限](../azure-subscription-service-limits.md)が適用されます。

**Q.** VM スケール セット内でデータ ディスクはサポートされていますか?

**A.** 最初のリリースではされていません。データを格納するためのオプションは、次のとおりです。

- Azure ファイル (SMB 共有ドライブ)

- OS ドライブ

- 一時ドライブ (ローカルで、Azure Storage にサポートされない)

- Azure データ サービス (Azure テーブル、Azure BLOB など)

- 外部データ サービス (リモート DB など)

**Q.** VM スケール セットは、どの Azure リージョンでサポートされていますか?

**A.** Azure リソース マネージャーがサポートされているすべてのリージョンで、VM スケール セットがサポートされています。

**Q.** カスタム イメージを使用して VM スケール セットを作成するにはどうすればよいですか?

**A.** vhdContainers プロパティを空白のままにします。たとえば、次のようにします。

	"storageProfile": {
		"osDisk": {
			"name": "vmssosdisk",
			"caching": "ReadOnly",
			"createOption": "FromImage",
			"image": {
				"uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
			},
			"osType": "Windows"
		}
	},


**Q.** VM スケール セット容量を 20 から 15 に減らすと、どの VM が削除されますか?

**A.** 可用性を最大限に高めるために仮想マシンは、すべてのアップグレード ドメインと障害ドメインのスケール セットから均等に削除されます。ID が最大の VM が最初に削除されます。

**Q.** その後、容量を 15 から 18 に増やす場合はどうなるでしょうか?

**A.** 容量を 18 に増やすと、 3 つの新しい VM が作成されます。作成されるたびに、VM インスタンス ID は前の最大値に増分された値となります (例: 20、21、22) 。VM は FD と UD 間に分散されます。

**Q.** VM スケール セットで複数の拡張機能を使用する場合、実行順序を強制できますか?

**A.** 直接的にではありませんが、customScript 拡張機能の場合、スクリプトで他の拡張機能が完了するまで待機できます ([たとえば、拡張機能ログを監視することによって](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh))。拡張機能の実行順序についての詳しいガイダンスについては、ブログ記事「[Extension Sequencing in Azure VM Scale Sets (Azure VM スケール セットにおける拡張機能の実行順序)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)」を参照してください。

**Q.** VM スケール セットは、Azure 可用性セットと連携できますか?

**A.** はい。VM スケール セットは、5 つの FD と 5 つの UD を持つ、暗黙的な可用性セットです。virtualMachineProfile の下のものは、何も構成する必要はありません。今後のリリースでは、VM スケール セットが複数のテナントにまたがる可能性がありますが、現時点では 1 つの可用性セットです。

<!---HONumber=AcomDC_0914_2016-->