 <properties
   pageTitle="Azure と Linux |Microsoft Azure"
   description="Linux 仮想マシンでの Azure Compute、Storage、Network サービスについて説明します。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# Azure と Linux

Microsoft Azure は、分析、仮想マシン、データベース、モバイル、ネットワーク、ストレージ、Web など、多様なパブリック クラウド サービスを一元化した集合体として成長し続け、さまざまなソリューションのホストとして理想的な形態となっています。Microsoft Azure のスケーラブルなコンピューティング プラットフォームによって、必要なときに使用した分だけを支払う料金体系が実現され、オンプレミスのハードウェアに投資する必要がありません。貴社の顧客のニーズに応えるうえで必要なレベルにまでソリューションをいつでもスケールアップまたはスケールアウトできる柔軟性が Azure にはあります。

Amazon の AWS のさまざまな機能を使い慣れている場合は、Azure と AWS の[定義マッピング ドキュメント](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)を調べることができます。


## 地域
Microsoft Azure のリソースは、世界各国複数の地理的リージョンに分散されます。"リージョン" とは、1 つの地域に存在する複数のデータ センターを表します。2016 年 1 月 1 日時点で米国に 8 つ、ヨーロッパに 2 つ、アジア太平洋地域に 6 つ、中国大陸に 2 つ、インドに 3 つのリージョンが存在します。すべての Azure リージョンを知りたい場合は、既存のリージョンと新たに発表されたリージョンのリストを公開していますのでご覧ください。

- [Azure リージョン](https://azure.microsoft.com/regions/)

## 可用性
Microsoft が VM に設けている 99.95% というサービス レベル アグリーメントの要件をカスタム デプロイで満たすためには、複数の VM をデプロイし、可用性セット内でワークロードを実行する必要があります。そうすることで、Microsoft のデータ センターにある複数の障害ドメインに VM を分散すると共に、メンテナンス期間の異なるホストにデプロイすることができます。完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) では、全体としての Azure の可用性の確保について説明します。

## Azure Virtual Machines とインスタンス
Microsoft Azure は、現在普及しているさまざまな Linux ディストリビューションに対応します。Microsoft の多数のパートナーが、それらのディストリビューションを提供、管理しています。Azure Marketplace からは、Red Hat Enterprise、CentOS、Debian、Ubuntu、CoreOS、RancherOS、FreeBSD などのディストリビューションが提供されています。Microsoft はさまざまな Linux コミュニティと積極的に連携し、[Azure 動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md) リストを拡充しています。

必要な Linux ディストリビューションが現時点でギャラリーに存在しない場合は、[Azure で Linux VHD を作成およびアップロード](virtual-machines-linux-create-upload-generic.md)することで、必要とする Linux の VM を導入できます。

Azure Virtual Machines を使用すると、さまざまなコンピューティング ソリューションを俊敏にデプロイできます。事実上、すべてのワークロードをすべての言語でほぼすべてのオペレーティング システム上 (Windows、Linux、または増え続けるパートナー各社でカスタム作成されたもの) にデプロイできます。たとえ目的に合った仮想マシンが見つからなくても、 ご安心ください。オンプレミスからカスタム イメージを取り込むことができます。

## VM サイズ
Azure に VM をデプロイするとき、あらかじめ用意された一連のサイズから、目的のワークロードに適した VM サイズを選択します。また、サイズは仮想マシンの処理能力、メモリ、記憶容量にも影響します。課金は、VM の実行時間とその割り当てリソースの消費量に基づいて行われます。[仮想マシンのサイズ](virtual-machines-linux-sizes.md)の完全な一覧。

ここでは、Microsoft が提供している一連の VM サイズ (A、D、DS、G、GS) からの選び方について基本的なガイドラインを紹介しています。

* A シリーズの VM は、軽量のワークロードと開発/テストを想定した低価格、入門レベルの VM です。すべてのリージョンで広く利用できます。定番となっているさまざまなリソースをつないで、仮想マシンから使用することが可能です。
* A シリーズ サイズ (A8 ～ A11) は、ハイパフォーマンス コンピューティング クラスター アプリケーションに適した特殊なコンピューティング集中型の構成です。
* D シリーズ VM は、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。
* Dv2 シリーズは D シリーズの最新版で、強力な CPU を特徴としています。Dv2 シリーズの CPU は D シリーズの CPU よりも、およそ 35% 高速です。これは最新世代の 2.4 GHz Intel Xeon® E5-2673 v3 (Haskell) プロセッサに基づいており、Intel Turbo Boost Technology 2.0 を使用することで、最大 3.2 GHz を実現できます。Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。
* G シリーズ VM は、最も多くのメモリを提供し、Intel Xeon E5 V3 ファミリのプロセッサが搭載されたホスト上で実行されます。

注: DS シリーズと GS シリーズの VM では、Premium Storage を使用できます。SSD をベースとした高性能かつ低レイテンシのストレージによって、I/O 負荷の高いワークロードに対応します。Premium Storage は特定のリージョンで使用できます。詳細については、次のリンクを参照してください。

- [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md)

## Automation
適切な DevOps カルチャを実現するには、すべてのインフラストラクチャがコードである必要があります。すべてのインフラストラクチャがコードである場合、簡単に再作成できます (Phoenix サーバー)。Azure は、Ansible、Chef、SaltStack、Puppet などのすべての主要なオートメーション ツールと連携します。Azure には、自動化のための独自のツールもあります。

- [Azure テンプレート](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

Azure は Linux ディストリビューションの多くで、[cloud-init](http://cloud-init.io/) のサポートを展開しています。現在、既定で有効になっている cloud-init で、Canonical の Ubuntu VM がデプロイされています。RedHats RHEL、CentOS、Fedora は cloud-init をサポートしますが、RedHat で保持される Azure のイメージに cloud-init はインストールされていません。RedHat family OS で cloud-init を使用するには、インストールされている cloud-init でカスタム イメージを作成する必要があります。

- [Azure Linux VM 上で cloud-init を使用する](virtual-machines-linux-using-cloud-init.md)

## Quotas (クォータ)
Azure サブスクリプションにはそれぞれ既定のクォータ制限が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。クォータ制限は、サポート チケットで制限の引き上げを要求することによって引き上げることができます。クォータ制限の詳細については、次を参照してください。

- [Azure サブスクリプション サービスの制限](../azure-subscription-service-limits.md)


## パートナー

Microsoft はパートナーと連携し、利用可能なイメージが Azure ランタイム用に更新、最適化されたことを確認します。パートナーの詳細については、次の Marketplace ページを確認してください。

- [Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)

Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)

Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - Azure Container Service with Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## Azure の設定
Azure の使用を開始するには、Azure アカウント、Azure CLI のインストール、および SSH 公開キーと秘密キーのペアが必要です。

## アカウントにサインアップする
Azure クラウドを使用する最初の手順は、Azure アカウントにサインアップすることです。開始するには、[Azure アカウントのサインアップ](https://azure.microsoft.com/pricing/free-trial/) ページに移動します。

## CLI をインストールする
新しい Azure アカウントを使用すると、Web ベースの管理パネルである Azure Portal の使用を直ちに開始することができます。コマンド ラインから Azure クラウドを管理するには、`azure-cli` をインストールします。Mac または Linux ワークステーションに [Azure CLI](../xplat-cli-install.md) をインストールします。

## SSH キー ペアの作成
これで Azure アカウント、Azure Web Portal 、Azure CLI の準備ができました。次の手順では、パスワードを使用せずに Linux で SSH を使用するための SSH キー ペアを作成します。[Linux および Mac で SSH キーを作成](virtual-machines-linux-mac-create-ssh-keys.md)し、パスワードのないログインとセキュリティの強化を実現します。

## Microsoft Azure における Linux の概要
Azure アカウントをセットアップすると Azure CLI がインストールされ、SSH キーが作成されます。これで Azure クラウドでインフラストラクチャの構築を開始する準備ができました。最初のタスクでは、いくつかの VM を作成します。

## CLI で VM を作成する
CLI で Linux VM を作成すると、作業中のターミナルを離れることなく、すばやく VM をデプロイできます。Web ポータルで指定できるものは、コマンド ライン フラグまたはスイッチからも使用できます。

- [CLI を使用して Linux VM を作成する](virtual-machines-linux-quick-create-cli.md)

## ポータルで VM を作成する
Azure Web Portal 上に Linux VM を作成すると、デプロイのためのさまざまなオプションから簡単にポイントおよびクリックすることができます。コマンド ライン フラグまたはスイッチを使用する代わりに、さまざまなオプションと設定の Web レイアウトを表示できます。コマンド ライン インターフェイスから使用できるものは、ポータルでも使用可能です。

- [ポータルを使用して Linux VM を作成する](virtual-machines-linux-quick-create-portal.md)

## パスワードを使わずに SSH を使用してログインする
これで VM が Azure 上で実行され、ログインする準備ができました。パスワードを使用して SSH からログインする方法は、安全ではなく、時間がかかります。ログインの手段として最も安全性が高く、かつすばやい方法は、SSH キーを使用することです。ポータルまたは CLI から Linux VM を作成する場合、認証の選択肢が 2 つあります。SSH のパスワードを選択した場合、Azure はパスワードによるログインを許可するように VM を構成します。SSH 公開キーを選択した場合、Azure は SSH キーを使用したログインのみを許可するように VM を構成し、パスワードのログインは無効になります。SSH キーのログインのみを許可することにより Linux VM をセキュリティで保護するには、ポータルまたは CLI で VM を作成する時に SSH 公開キーのオプションを使用します。

- [SSHD の構成により Linux VM で SSH パスワードを無効化する](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## 関連する Azure のコンポーネント

## Storage

- [Microsoft Azure Storage の概要](../storage/storage-introduction.md)

- [Azure CLI を使用して Linux VM にディスクを追加する](virtual-machines-linux-add-disk.md)

- [Azure ポータルで Linux VM にデータ ディスクを接続する方法](virtual-machines-linux-attach-disk-portal.md)

## ネットワーク

- [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)

- [Azure 内の IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Azure での Linux VM へのポートの開放](virtual-machines-linux-nsg-quickstart.md)

- [Azure ポータルでの完全修飾ドメイン名の作成](virtual-machines-linux-portal-create-fqdn.md)


## コンテナー

- [Virtual Machines とコンテナーが Azure にもたらすメリット](virtual-machines-linux-containers.md)

- [Azure Container Service の概要](../container-service/container-service-intro.md)

- [Azure コンテナー サービス クラスターのデプロイ](../container-service/container-service-deployment.md)

## 次のステップ

これで、Azure での Linux の概要の説明が終わりました。次の手順では、VM の作成について詳しく説明します。

- [ポータルを使用して Azure に Linux VM を作成する](virtual-machines-linux-quick-create-portal.md)

- [CLI を使用した Azure での Linux VM の作成](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0921_2016-->