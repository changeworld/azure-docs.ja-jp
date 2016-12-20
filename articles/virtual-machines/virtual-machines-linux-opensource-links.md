---
title: "Azure での Linux とオープン ソース コンピューティング | Microsoft Docs"
description: "Linux の基本的な使用法、Azure での Linux イメージの実行やアップロードに関するいくつかの基本的な概念、特定のテクノロジと最適化に関するその他のコンテンツなど、Azure での Linux およびオープン ソース コンピューティングに関する記事を一覧します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: a7e608b5-26ea-41e0-b46b-1a483a257754
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/27/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 680bb7dfb401decc554637c0094a06a0a6260288


---
# <a name="linux-and-open-source-computing-on-azure"></a>Azure での Linux とオープン ソース コンピューティング
ここでは、クラシック デプロイメント モデルで Linux ベースの仮想マシンを作成および管理するために必要なすべてのドキュメントを紹介します。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>作業の開始
* [Azure での Linux 入門](virtual-machines-linux-intro-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシック デプロイ モデルで作成された Azure Virtual Machines についてよく寄せられる質問](virtual-machines-linux-classic-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [仮想マシンのイメージについて](virtual-machines-linux-classic-about-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [独自のディストリビューション イメージのアップロード](virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) (および [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)の使用手順)
* [Azure クラシック ポータルを使用した Linux VM へのログオン](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="set-up"></a>セットアップ
* [Azure コマンド ライン インターフェイス (Azure CLI) のインストール](../xplat-cli-install.md)

## <a name="tutorials"></a>Tutorials (チュートリアル)
* [Azure 上の Linux 仮想マシンへの LAMP スタックのインストール](virtual-machines-linux-create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VM での Ruby on Rails Web アプリケーション](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
* [方法: AMQP や Service Bus 用の Apache Qpid Proton-C のインストール](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>データベース
* [Azure での MySQL パフォーマンスの最適化](virtual-machines-linux-classic-optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MySQL クラスター](virtual-machines-linux-classic-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure 上の Linux で Cassandra を実行して Node.js からアクセス](virtual-machines-linux-classic-cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MariaDbs の複数のマスター クラスターを作成する](virtual-machines-linux-classic-mariadb-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="hpc"></a>HPC
* [Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="docker"></a>Docker
* [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用](virtual-machines-linux-classic-cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure ポータルでの Docker VM 拡張機能の使用](virtual-machines-linux-classic-portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure で docker マシンを使用する方法](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="ubuntu"></a>Ubuntu
* [方法: MySQL クラスター](virtual-machines-linux-classic-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [方法: Node.js と Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="opensuse"></a>OpenSUSE
* [方法: MySQL のインストールと実行](virtual-machines-linux-classic-mysql-on-opensuse.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="coreos"></a>CoreOS
* [方法: Azure での CoreOS の使用](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="planning"></a>計画
* [Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux ユーザー名の選択](virtual-machines-linux-usernames.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシック デプロイ モデルに仮想マシンの可用性セットを構成する方法](virtual-machines-linux-classic-configure-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure VM の計画的メンテナンスのスケジュールを設定する方法](virtual-machines-linux-planned-maintenance-schedule.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [仮想マシンの可用性管理](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure での Linux 仮想マシンに対する計画的なメンテナンス](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="deployment"></a>デプロイ
* [Linux を実行するカスタム仮想マシンの作成](virtual-machines-linux-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [基本: テンプレートを作成するための Linux VM のキャプチャ](virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="management"></a>管理
* [SSH](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux 用のパスワードまたは SSH プロパティをリセットする方法](virtual-machines-linux-classic-reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [ルートの使用](virtual-machines-linux-use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-resources"></a>Azure リソース
* [Azure Linux エージェント](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VM 拡張機能とその機能](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Cloud-init で使用するカスタム データの VM への挿入](virtual-machines-windows-classic-inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>Storage
* [Linux VM へのデータ ディスクの接続](virtual-machines-linux-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Linux VM からのデータ ディスクの切断](virtual-machines-linux-classic-detach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>ネットワーク
* [Azure 上でクラシック仮想マシンにエンドポイントをセットアップする方法](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="troubleshooting"></a>トラブルシューティング
* [Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Troubleshoot classic deployment issues with creating a new Linux virtual machine in Azure (Azure での新しい Linux 仮想マシンの作成に関するクラシック デプロイの問題のトラブルシューティング)](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)  
* [Troubleshoot classic deployment issues with restarting or resizing an existing Linux Virtual Machine in Azure (Azure での既存の Linux 仮想マシンの再起動またはサイズ変更に関するクラシック デプロイの問題のトラブルシューティング)](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 

## <a name="reference"></a>リファレンス
* [Azure サービス管理 (asm) モードでの Azure CLI コマンド](../virtual-machines-command-line-tools.md)
* [サービス管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/ee460799.aspx)

## <a name="general-links"></a>全般的なリンク
以下のリンクは、上記の Azure.com のドキュメントではなく、Microsoft ブログ、Technet ページ、および外部サイトを参照するものです。 Azure とオープン ソース コンピューティング環境はどちらもめまぐるしく変化しています。このため、新しいトピックを追加して古くなったトピックを削除するよう常に最善を尽くしてはいます "*が*"、以下のリンクは古くなっている可能性があります。 不足などあればコメント欄よりお知らせください。または、[GitHub リポジトリ](https://github.com/Azure/azure-content/)でプル要求を送信してください。

* [Docker コンテナーを使用して Linux で ASP.NET 5 を実行する](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
* [OpenLogic から CentOS 仮想マシン イメージをデプロイする方法](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
* [SUSE 更新インフラストラクチャ](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
* [SAP Cloud Appliance Library の SUSE Linux Enterprise Server](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
* [12 の手順から成る Azure 上での高可用性 Linux の構築](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
* [Azure CLI、node.js、jhawk を使用した Azure での Linux のプロビジョニングの自動化](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
* [Azure での GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
* [Azure での FreeBSD の実行](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
* [FreeBSD の簡単なデプロイ](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
* [カスタマイズされた FreeBSD イメージのデプロイ](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
* [Linux ファイル サーバーの Kaspersky AV](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL
* [Azure の 8 つのオープン ソースの NoSql データベース](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
* [Slideshare (MSOpenTech): Azure 上の CouchDb とエクスペリエンス](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
* [node.js、CORS、Grunt を使用して CouchDB をサービスとして実行する](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
* [Azure Redis Cache Service での Redis on Windows](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
* [Redis のプレビュー リリースの ASP.NET セッション状態プロバイダーの通知](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
* [ブログ: Azure Marketplace での RavenHQ の提供開始](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>ビッグ データ
* [Azure Linux VM 上での Hadoop のインストール](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
* [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>リレーショナル データベース
* [Microsoft Azure での MySQL の高可用性アーキテクチャ](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
* [ILB を使用して Corosync、pg_bouncer と Postgres をインストールする](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux ハイ パフォーマンス コンピューティング (HPC)
* [クイック スタート テンプレート: SLURM クラスターの起動](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (および[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
* [クイックスタート テンプレート: Linux コンピューティング ノードがある HPC クラスターを作成する](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>開発、管理、最適化
開発、管理、最適化は、非常に幅広く、かつ変化の激しい世界です。そのため、次に挙げる事項を最初に考慮する必要があります。

* [ビデオ: Azure Virtual Machines : Using Chef, Puppet and Docker for managing Linux VMs (Azure Virtual Machines: Chef、Puppet、Docker を使用した Linux VM の管理)](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)
* [CoreOS と Weave を使用した Kubernetes クラスターのデプロイの自動化に関する包括的なガイド](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
* [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Azure 用 Jenkins スレーブ プラグイン](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
* [GitHub のリポジトリ: Azure 用 Jenkins ストレージ プラグイン](https://github.com/jenkinsci/windows-azure-storage-plugin)
* [サード パーティ: Azure 用 Hudson スレーブ プラグイン](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
* [サード パーティ: Azure 用 Hudson ストレージ プラグイン](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [ビデオ: Chef の概要とそのしくみ](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)
* [ビデオ: Linux VM で Azure Automation を使用する方法](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* [ビデオ: Linux 用 Powershell DSC を実行する方法](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
* [GitHub: Docker クライアント DSC](https://github.com/anweiss/DockerClientDSC)
* [Azure 用のプラグインの packer](https://github.com/msopentech/packer-azure)




<!--HONumber=Nov16_HO3-->


