<properties
	pageTitle="Azure での Linux とオープン ソース コンピューティング"
	description="このトピックには、Linux の基本的な使用法、Azure での Linux イメージの実行やアップロードに関するいくつかの基本的な概念、特定のテクノロジと最適化に関するその他のコンテンツなど、Azure での Linux およびオープン ソース コンピューティングの一覧が含まれています。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="08/07/2015"
	ms.author="rasquill"/>



# Azure での Linux とオープン ソース コンピューティング

このドキュメントは、Microsoft とそのパートナーによって書かれた、Microsoft Azure での Linux ベースの仮想マシンの実行およびその他のオープン ソースのコンピューティング環境とアプリケーションに関するすべてのトピックをまとめて一覧にしたものです。Azure やオープン ソース コンピューティングの環境はめまぐるしく変化しています。最新情報を追加し、古くなった情報を削除するよう常に最善を尽くしていますが、*それでも*このドキュメントの情報が最新ではない可能性があります。不足などあればコメント欄よりお知らせください。または、[GitHub リポジトリ](https://github.com/Azure/azure-content/)でプル要求を送信してください。

## 一般的な注意事項
このページのセクションは、右に示すように分けられています。(トピックが複数のコンセプト、ディストリビューション、テクノロジに関連しているため、複数のセクションにリンクが出現します)。 さらに、さまざまな Linux オプション、イメージ リポジトリ、ケース スタディに関するトピック、および独自のカスタム イメージをアップロードする操作方法に関するトピックがあります。

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [イベントおよびデモンストレーション: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [方法: 独自のディストリビューション イメージのアップロード](virtual-machines-linux-create-upload-vhd.md) (と [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distributions.md)の使用手順)
- [注: Azure で実行するための Linux の一般的な要件](virtual-machines-linux-create-upload-vhd-generic.md)
- [注: Azure 上の Linux の概要](virtual-machines-linux-introduction.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## ディストリビューション

大量の Linux ディストリビューションがあり、通常はパッケージ管理システムによって分類されています。Debian、Ubuntu などの dpkg ベースのものや、CentOS、SUSE、RedHat などの rpm ベースのものがあります。一部の企業は Microsoft の正式なパートナーとしてディストリビューション イメージを提供しており、そのイメージは動作保証されています。その他は、コミュニティによって提供されます。このセクションのディストリビューションには、それが他のテクノロジの例としてのみ使用されている場合でも、公式な記事があります。

### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu は、dpkg および apt-get パッケージ管理に基づく、人気の高い、Azure での動作保証済み Linux ディストリビューションです。

1. [方法: 独自の Ubuntu イメージのアップロード](virtual-machines-linux-create-upload-vhd-ubuntu.md)
2. [方法: Ubuntu LAMP スタック](virtual-machines-linux-install-lamp-stack.md)
2. [イメージ: LAPP スタック](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [方法: MySQL クラスター](virtual-machines-linux-mysql-cluster.md)
4. [方法: Node.js と Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)
5. [方法: IPython Notebook](virtual-machines-python-ipython-notebook.md)
6. [詳細: Docker コンテナーを使用して Linux で ASP.NET 5 を実行する](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [イメージ: Redis サーバー](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [イメージ: Minecraft サーバー](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [イメージ: Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [イメージ: Mono as a Service](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian は、dpkg および apt-get パッケージ管理に基づく、Linux およびオープン ソース環境における重要なディストリビューションです。MSOpenTech VM Depot では複数のイメージを使用します。.

### CentOS

CentOS Linux ディストリビューションは、Red Hat Enterprise Linux (RHEL) のソースから派生した、安定した、予測可能で管理しやすい再現可能なプラットフォームです。

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [イメージ ギャラリー](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [方法: Azure 用のカスタム CentOS ベースの VM の準備](virtual-machines-linux-create-upload-vhd-centos.md)
4. [ブログ: OpenLogic から CentOS VM イメージをデプロイする方法](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [方法: AMQP や Service Bus 用の Apache Qpid Proton-C のインストール](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [イメージ: OpenLogic CentOS 6.3 上の Apache 2.2.15](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [イメージ: OpenLogic CentOS 6.3 上の Drupal 7.2、LAMP サーバー](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

### SUSE Linux Enterprise Server および openSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [方法: MySQL のインストールと実行](virtual-machines-linux-mysql-use-opensuse.md)
12. [方法: カスタム SLES または openSUSE VM の準備](virtual-machines-linux-create-upload-vhd-suse.md)  
13. [[SUSE フォーラム] 方法: 新しいパッチ サーバーへの移動](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [イメージ: SAP Cloud Appliance Library の SUSE Linux Enterprise Server](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS は、カスタマイズを細かく制御できる純粋なコンピューティングのスケーリングのための軽量で最適化されたディストリビューションです。

10. [イメージ ギャラリー](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [方法: Azure での CoreOS の使用](virtual-machines-linux-coreos-how-to.md)
12. [方法: Azure 上の CoreOS での fleet と Docker の使用](virtual-machines-linux-coreos-fleet-get-started.md)
13. [ブログ: TechEd Europe -- Windows Docker クライアント と Linux コンテナー](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
14. [ブログ: ますます大規模化、高速化、オープン化する Azure](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
15. [GitHub: Azure で CoreOS をデプロイするためのクイック スタート](https://github.com/timfpark/coreos-azure)
16. [GitHub: Spring Boot、MongoDB、CoreOS での Java アプリのデプロイ](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Azure 用の Oracle Linux 仮想マシンの準備](virtual-machines-linux-create-upload-vhd-oracle.md)

### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [ブログ: Azure での FreeBSD の実行](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [ブログ: FreeBSD の簡単なデプロイ](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [ブログ: カスタマイズされた FreeBSD イメージのデプロイ](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [方法: Azure Linux エージェントのインストール](virtual-machines-linux-agent-user-guide.md)
18. [Marketplace: Linux ファイル サーバーの Kaspersky AV](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## 基本

1. [基本: Azure コマンド ライン インターフェイス (Azure CLI)](../xplat-cli.md)
4. [基本: 証明書の使用と管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [基本: Linux ユーザー名の選択](virtual-machines-linux-usernames.md)
6. [基本: Azure ポータルを使用した Linux VM へのログオン](virtual-machines-linux-how-to-log-on.md)
7. [基本: SSH](virtual-machines-linux-use-ssh-key.md)
8. [基本: Linux 用のパスワードまたは SSH プロパティをリセットする方法](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [基本: ルートの使用](virtual-machines-linux-use-root-privileges.md)
10. [基本: Linux VM へのデータ ディスクの接続](virtual-machines-linux-how-to-attach-disk.md)
11. [基本: Linux VM からのデータ ディスクの切断](virtual-machines-linux-how-to-detach-disk.md)
12. [基本に関するブログ: Linux と Azure での記憶域、ディスク、パフォーマンスの最適化](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [基本: RAID](virtual-machines-linux-configure-raid.md)
14. [基本: テンプレートを作成するための Linux VM のキャプチャ](virtual-machines-linux-capture-image.md)
15. [基本: Azure Linux エージェント](virtual-machines-linux-agent-user-guide.md)
16. [基本: Azure VM 拡張機能とその機能](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [基本: Cloud-init で使用するカスタム データの VM への挿入](virtual-machines-how-to-inject-custom-data.md)
18. [基本に関するブログ: 12 の手順から成る Azure 上での高可用性 Linux の構築](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [基本に関するブログ: Azure CLI、node.js、jhawk を使用した、Azure 上での Linux のプロビジョニングの自動化](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
19. [Azure CLI を使用して複数の VM のデプロイを作成する](virtual-machines-create-multi-vm-deployment-../xplat-cli.md)
20. [基本: Azure の Docker VM 拡張機能](virtual-machines-docker-vm-extension.md)
23. [Azure サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) のリファレンス
24. [Azure での GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## コミュニティのイメージとリポジトリ
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; 仮想マシン イメージを提供するコミュニティ用
4. [GitHub](https://github.com/Azure/) &mdash; Azure CLI やその他のさまざまなツールとプロジェクト用
5. [Docker ハブ レジストリ](https://registry.hub.docker.com/) &mdash; Docker コンテナー イメージ用レジストリ

## 言語とプラットフォーム
### [Azure Java デベロッパー センター](http://azure.microsoft.com/develop/java/)

1. [イメージ](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [方法: AMQP 1.0 を使用した Java からの Service Bus の使用](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [方法: Azure ポータルを使用した Linux 上での Tomcat7 の設定](virtual-machines-linux-setup-tomcat7-linux.md)
4. [ビデオ: サービス管理用の Azure Java SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [ブログ: Java 用 Azure 管理ライブライの概要](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [GitHub のリポジトリ: Azure Toolkit for Eclipse with Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [リファレンス: Azure Toolkit for Eclipse with Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [GitHub のリポジトリ: IntelliJ IDEA と Android Studio 用の MS Open Tech ツールのプラグイン](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [ブログ: OpenJDK での MSOpenTech の使用](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [イメージ: WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [イメージ: WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [イメージ: Windows 上の JDK6](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [イメージ: Windows 上の JDK7](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [イメージ: Windows 上の JDK8](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

### JVM 言語

1. [スカラ: Azure Cloud Services での Play Framework アプリケーションの実行](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### SDK の種類、インストール、アップグレード
4. [Azure サービス管理 SDK: Java](http://dl.windowsazure.com/javadoc/)
5. [Azure サービス管理 SDK: Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Azure サービス管理 SDK: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [方法: Ruby on Rails のインストール](virtual-machines-ruby-rails-web-app-linux.md)
6. [Azure サービス管理 SDK: Python](https://github.com/Azure/azure-sdk-for-python)
    - [方法: Django Hello World Web アプリケーション (Mac-Linux)](virtual-machines-python-django-web-app-linux.md)
7. [Azure サービス管理 SDK: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Azure サービス管理 SDK: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [方法: Azure VM での LAMP スタックのインストール](virtual-machines-linux-install-lamp-stack.md)
    - [ビデオ: Azure VM での LAMP スタックのインストール](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Azure サービス管理 SDK: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [ブログ: Mono、ASP.NET 5、Linux、Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## サンプルとスクリプト

このセクションで不足を埋めましょう。ご提案があれば PR をお送りください。または下記のコメントにご記載ください。

1. [Azure CLI を使用して複数の VM のデプロイを作成する](virtual-machines-create-multi-vm-deployment-../xplat-cli.md)
2. [Patrick Chanezon の Azure Linux GitHub リポジトリ](https://github.com/chanezon/azure-linux)
3. [ビデオ: **usbip** を使用して Linun のオンプレミス USB データを Azure に移動する方法](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [ビデオ: fernapp を使用してブラウザーで Azure の Linux ベースの GUI にアクセスする](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [ビデオ: Azure ファイル プレビューを使用した Linux 上の共有記憶域 - パート 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [ビデオ: Service Bus と Web Sites を使用して Azure 上に Linux デバイスを組み込む](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [ビデオ: ネイティブ Linux ベースの memcached アプリケーションを Azure に接続する](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [ビデオ: Azure 上の高可用性 Linux サービスの負荷分散: OpenLDAP と MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## データ

このセクションには、NoSQL、リレーショナル、ビッグ データなどの異なる複数の記憶域のアプローチとテクノロジに関する情報が含まれています。

### NoSQL

1. [ブログ: Azure の 8 つのオープン ソースの NoSql データベース](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech): Azure 上の CouchDb とエクスペリエンス](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [ブログ: node.js、CORS、Grunt を使用して CouchDB をサービスとして実行する](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [方法: MongoLab アドオンを使用して Azure で MongoDB 対応の Node.js アプリケーションを作成する](store-mongolab-web-sites-nodejs-store-data-mongodb.md)
4. Cassandra
    - [方法: Azure 上の Linux で Cassandra を実行して Node.js からアクセスする](virtual-machines-linux-nodejs-running-cassandra.md)
5. Redis
    - [ブログ: Azure Redis Cache Service での Redis on Windows](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [ブログ: Redis のプレビュー リリースの ASP.NET セッション状態プロバイダーの通知](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [ブログ: Azure Marketplace での RavenHQ の提供開始](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### ビッグ データ
2. Hadoop/Cloudera  
	- [ブログ: Azure Linux VM 上での Hadoop のインストール](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [方法: HDInsight を使用して Hadoop と Hive を使用する](hdinsight-get-started.md)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) -- Azure 上で Hadoop サービスを完全に管理する

### リレーショナル データベース
2. MySQL
    - [方法: MySQL のインストールと実行](virtual-machines-linux-mysql-use-opensuse.md)
    - [方法: Azure での MySQL パフォーマンスの最適化](virtual-machines-linux-optimize-mysql-perf.md)
    - [方法: MySQL クラスター](virtual-machines-linux-mysql-cluster.md)
    - [方法: Marketplace を使用して MySQL データベースを作成する](store-php-create-mysql-database.md)
    - [方法: Python と Visual Studio を使用した Azure Websites の Django と MySQL](web-sites-python-ptvs-django-mysql.md)
    - [方法: WebMatrix を使用した Azure Websites のPHP と MySQL](web-sites-php-mysql-use-webmatrix.md)
    - [Microsoft Azure での MySQL の高可用性アーキテクチャ](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [方法: MariaDbs の複数のマスター クラスターを作成する](virtual-machines-mariadb-cluster.md)
8. [ILB を使用して Corosync、pg\_bouncer と Postgres をインストールする](https://github.com/chgeuer/postgres-azure)


## 認証と暗号化

認証と暗号化は、ソフトウェア開発において、重要なトピックで、Web 上には、適切なセキュリティ手法の学習と使用方法に関して膨大な数のトピックが存在します。ここでは、Linux とオープンソース ワークロードを使用して起動し、すばやく実行するための基本的な用法、および Azure でのリモート セキュリティ機能のリセットや削除に使用するツールについていくつか説明します。これらは基本的な手順ですが、より複雑なシナリオをすぐに追加していきます。

4. [基本: 証明書の使用と管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [基本: SSH](virtual-machines-linux-use-ssh-key.md)
8. [基本: Linux 用のパスワードまたは SSH プロパティをリセットする方法](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [基本: ルートの使用](virtual-machines-linux-use-root-privileges.md)

## Linux ハイパフォーマンス コンピューティング (HPC)

オープン ソースのツールまたは Microsoft HPC Pack により構築された Linux VM クラスターで、HPC ワークロードを実行します。

1.	[クイックスタート テンプレート： SLURM クラスターの起動](http://azure.microsoft.com/documentation/templates/slurm/) (および[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
2.	[クイックスタート テンプレート： トルク クラスターの起動](http://azure.microsoft.com/documentation/templates/torque-cluster/)
3.	[チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-cluster-hpcpack.md)
4.	[チュートリアル： MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-cluster-rdma.md)

## 開発、管理、最適化

このセクションでは、まずビデオ シリーズを含むブログ エントリを紹介します。[ビデオ: Azure Virtual Machines: Chef、Puppet、および Docker を使用した Linux VM の管理](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)。ただし、開発、管理、そして最適化は、非常に幅広く、かつ変化の激しい世界です。そのため、次に挙げる事項を最初に考慮する必要があります。

1. Docker
	- [Azure での Linux 用 Docker VM 拡張機能](virtual-machines-docker-vm-extension.md)
	- [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用](virtual-machines-docker-with-../xplat-cli.md)
	- [Azure プレビュー ポータルでの Docker VM 拡張機能の使用](virtual-machines-docker-with-portal.md)
	- [Azure Marketplace で Docker をすばやく開始する](virtual-machines-docker-ubuntu-quickstart.md)
	- [Azure で docker マシンを使用する方法](virtual-machines-docker-machine.md)
	- [Azure 上の Swarm における Docker の使用方法](virtual-machines-docker-swarm.md)
	- [Azure での Docker および Compose の概要](virtual-machines-docker-compose-quickstart.md)

2. [CoreOS での Fleet](virtual-machines-linux-coreos-how-to.md)
3. Deis
	- [GitHub のリポジトリ: Azure 上の CoreOS クラスターへの Deis のインストール](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [CoreOS と Weave を使用した Kubernetes クラスターのデプロイの自動化に関する包括的なガイド](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins と Hudson
	- [ブログ: Azure 用 Jenkins スレーブ プラグイン](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub のリポジトリ: Azure 用 Jenkins ストレージ プラグイン](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [サード パーティ: Azure 用 Hudson スレーブ プラグイン](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [サード パーティ: Azure 用 Hudson ストレージ プラグイン](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef と仮想マシン](virtual-machines-windows-install-chef-client.md)
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

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md

<!---HONumber=August15_HO7-->