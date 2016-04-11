<properties
	pageTitle="Azure での Linux とオープン ソース コンピューティング | Microsoft Azure"
	description="Linux の基本的な使用法、Azure での Linux イメージの実行やアップロードに関するいくつかの基本的な概念、特定のテクノロジと最適化に関するその他のコンテンツなど、Azure での Linux およびオープン ソース コンピューティングに関する記事を一覧します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/26/2016"
	ms.author="rasquill"/>



# Azure での Linux とオープン ソース コンピューティング

このドキュメントは、Microsoft とそのパートナーによって書かれた、Microsoft Azure での Linux ベースの Virtual Machines の実行およびその他のオープン ソースのコンピューティング環境とアプリケーションに関するすべてのクラシック デプロイ モデルのトピックをまとめて一覧にしたものです。

Azure やオープン ソース コンピューティングの環境はめまぐるしく変化しています。最新情報を追加し、古くなった情報を削除するよう常に最善を尽くしていますが、*それでも*このドキュメントの情報が最新ではない可能性があります。不足などあればコメント欄よりお知らせください。または、[GitHub リポジトリ](https://github.com/Azure/azure-content/)でプル要求を送信してください。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## 一般的な注意事項
このページのセクションは、右に示すように分けられています。(トピックが複数のコンセプト、ディストリビューション、テクノロジに関連しているため、複数のセクションにリンクが出現します)。 さらに、さまざまな Linux オプション、イメージ リポジトリ、ケース スタディに関するトピック、および独自のカスタム イメージをアップロードする操作方法に関するトピックがあります。

- [Azure Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [イベントおよびデモンストレーション: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [方法: 独自のディストリビューション イメージのアップロード](virtual-machines-linux-classic-create-upload-vhd.md) (と [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distros.md)の使用手順)
- [注: Azure で実行するための Linux の一般的な要件](virtual-machines-linux-create-upload-generic.md)
- [注: Azure 上の Linux の概要](virtual-machines-linux-intro-on-azure.md)

## ディストリビューション

大量の Linux ディストリビューションがあり、通常はパッケージ管理システムによって分類されています。Debian、Ubuntu などの dpkg ベースのものや、CentOS、SUSE、RedHat などの rpm ベースのものがあります。一部の企業は Microsoft の正式なパートナーとしてディストリビューション イメージを提供しており、そのイメージは動作保証されています。その他は、コミュニティによって提供されます。このセクションのディストリビューションには、それが他のテクノロジの例としてのみ使用されている場合でも、公式な記事があります。

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu は、dpkg および apt-get パッケージ管理に基づく、人気の高い、Azure での動作保証済み Linux ディストリビューションです。

3. [方法: MySQL クラスター](virtual-machines-linux-classic-mysql-cluster.md)
4. [方法: Node.js と Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
6. [詳細: Docker コンテナーを使用して Linux で ASP.NET 5 を実行する](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### CentOS

CentOS Linux ディストリビューションは、Red Hat Enterprise Linux (RHEL) のソースから派生した、安定した、予測可能で管理しやすい再現可能なプラットフォームです。

4. [ブログ: OpenLogic から CentOS VM イメージをデプロイする方法](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [方法: AMQP や Service Bus 用の Apache Qpid Proton-C のインストール](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server および openSUSE

11. [方法: MySQL のインストールと実行](virtual-machines-linux-classic-mysql-on-opensuse.md)
13. [[SUSE フォーラム] 方法: 新しいパッチ サーバーへの移動](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [イメージ: SAP Cloud Appliance Library の SUSE Linux Enterprise Server](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS は、カスタマイズを細かく制御できる純粋なコンピューティングのスケーリングのための軽量で最適化されたディストリビューションです。

10. [イメージ ギャラリー](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [方法: Azure での CoreOS の使用](virtual-machines-linux-classic-coreos-howto.md)
12. [方法: Azure 上の CoreOS での fleet と Docker の使用](virtual-machines-linux-classic-coreos-fleet-get-started.md)


### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [ブログ: Azure での FreeBSD の実行](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [ブログ: FreeBSD の簡単なデプロイ](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [ブログ: カスタマイズされた FreeBSD イメージのデプロイ](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
18. [Marketplace: Linux ファイル サーバーの Kaspersky AV](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## 基本

1. [基本: Azure コマンド ライン インターフェイス (Azure CLI)](../xplat-cli-install.md)
5. [基本: Linux ユーザー名の選択](virtual-machines-linux-usernames.md)
6. [基本: Azure クラシック ポータルを使用した Linux VM へのログオン](virtual-machines-linux-classic-log-on.md)
7. [基本: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [基本: Linux 用のパスワードまたは SSH プロパティをリセットする方法](virtual-machines-linux-classic-reset-access.md)
9. [基本: ルートの使用](virtual-machines-linux-use-root-privileges.md)
10. [基本: Linux VM へのデータ ディスクの接続](virtual-machines-linux-classic-attach-disk.md)
11. [基本: Linux VM からのデータ ディスクの切断](virtual-machines-linux-classic-detach-disk.md)
12. [基本に関するブログ: Linux と Azure での記憶域、ディスク、パフォーマンスの最適化](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [基本: RAID](virtual-machines-linux-configure-raid.md)
14. [基本: テンプレートを作成するための Linux VM のキャプチャ](virtual-machines-linux-classic-capture-image.md)
15. [基本: Azure Linux エージェント](virtual-machines-linux-agent-user-guide.md)
16. [基本: Azure VM 拡張機能とその機能](virtual-machines-windows-extensions-features.md)
17. [基本: Cloud-init で使用するカスタム データの VM への挿入](virtual-machines-windows-classic-inject-custom-data.md)
18. [基本に関するブログ: 12 の手順から成る Azure 上での高可用性 Linux の構築](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [基本に関するブログ: Azure CLI、node.js、jhawk を使用した、Azure 上での Linux のプロビジョニングの自動化](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
23. [Azure サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) のリファレンス
24. [Azure での GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## コミュニティのイメージとリポジトリ
4. [GitHub](https://github.com/Azure/) &mdash; Azure CLI やその他のさまざまなツールとプロジェクト用
5. [Docker ハブ レジストリ](https://registry.hub.docker.com/) &mdash; Docker コンテナー イメージ用レジストリ

## データ

このセクションには、NoSQL、リレーショナル、ビッグ データなどの異なる複数の記憶域のアプローチとテクノロジに関する情報が含まれています。

### NoSQL

1. [ブログ: Azure の 8 つのオープン ソースの NoSql データベース](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech): Azure 上の CouchDb とエクスペリエンス](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [ブログ: node.js、CORS、Grunt を使用して CouchDB をサービスとして実行する](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
4. Cassandra
    - [方法: Azure 上の Linux で Cassandra を実行して Node.js からアクセスする](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [ブログ: Azure Redis Cache Service での Redis on Windows](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [ブログ: Redis のプレビュー リリースの ASP.NET セッション状態プロバイダーの通知](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [ブログ: Azure Marketplace での RavenHQ の提供開始](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### ビッグ データ
2. Hadoop/Cloudera  
	- [ブログ: Azure Linux VM 上での Hadoop のインストール](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) -- Azure 上で Hadoop サービスを完全に管理する

### リレーショナル データベース
2. MySQL
    - [方法: MySQL のインストールと実行](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [方法: Azure での MySQL パフォーマンスの最適化](virtual-machines-linux-classic-optimize-mysql.md)
    - [方法: MySQL クラスター](virtual-machines-linux-classic-mysql-cluster.md)
    - [Microsoft Azure での MySQL の高可用性アーキテクチャ](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [方法: MariaDbs の複数のマスター クラスターを作成する](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [ILB を使用して Corosync、pg\_bouncer と Postgres をインストールする](https://github.com/chgeuer/postgres-azure)


## 認証と暗号化

認証と暗号化は、ソフトウェア開発において、重要なトピックで、Web 上には、適切なセキュリティ手法の学習と使用方法に関して膨大な数のトピックが存在します。ここでは、Linux とオープンソース ワークロードを使用して起動し、すばやく実行するための基本的な用法、および Azure でのリモート セキュリティ機能のリセットや削除に使用するツールについていくつか説明します。これらは基本的な手順ですが、より複雑なシナリオをすぐに追加していきます。

4. [基本: 証明書の使用と管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [基本: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [基本: Linux 用のパスワードまたは SSH プロパティをリセットする方法](virtual-machines-linux-classic-reset-access.md)
9. [基本: ルートの使用](virtual-machines-linux-use-root-privileges.md)

## Linux ハイ パフォーマンス コンピューティング (HPC)

オープン ソースのツールまたは Microsoft HPC Pack により構築された Linux VM クラスターで、HPC ワークロードを実行します。

1.	[クイックスタート テンプレート： SLURM クラスターの起動](https://azure.microsoft.com/documentation/templates/slurm/) (および[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
3.	[クイックスタート テンプレート: Linux コンピューティング ノードがある HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[チュートリアル: Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[チュートリアル： MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md)


## 開発、管理、最適化

このセクションでは、まずビデオ シリーズを含むブログ エントリを紹介します。[ビデオ: Azure Virtual Machines: Chef、Puppet、および Docker を使用した Linux VM の管理](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)。ただし、開発、管理、そして最適化は、非常に幅広く、かつ変化の激しい世界です。そのため、次に挙げる事項を最初に考慮する必要があります。

1. Docker
	- [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用](virtual-machines-linux-classic-cli-use-docker.md)
	- [Azure ポータルでの Docker VM 拡張機能の使用](virtual-machines-linux-classic-portal-use-docker.md)
    - [Azure Marketplace で Docker をすばやく開始する](virtual-machines-linux-classic-docker-quickstart.md)
	- [Azure で docker マシンを使用する方法](virtual-machines-linux-classic-docker-machine.md)

2. [CoreOS での Fleet](virtual-machines-linux-classic-coreos-howto.md)
4. Kubernetes
	- [CoreOS と Weave を使用した Kubernetes クラスターのデプロイの自動化に関する包括的なガイド](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins と Hudson
	- [ブログ: Azure 用 Jenkins スレーブ プラグイン](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub のリポジトリ: Azure 用 Jenkins ストレージ プラグイン](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [サード パーティ: Azure 用 Hudson スレーブ プラグイン](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [サード パーティ: Azure 用 Hudson ストレージ プラグイン](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [ビデオ: Chef の概要とそのしくみ](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure Automation
	- [ビデオ: Linux VM で Azure Automation を使用する方法](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Linux 用の Powershell DSC
    - [ビデオ: Linux 用 Powershell DSC を実行する方法](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: Docker クライアント DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Azure 用のプラグインの packer](https://github.com/msopentech/packer-azure)

## サポート、トラブルシューティング、および "うまくいかない場合"

1. Microsoft サポート ドキュメント
	- [サポート: Microsoft Azure での Linux イメージのサポート](http://support2.microsoft.com/kb/2941892)

<!---HONumber=AcomDC_0330_2016-->