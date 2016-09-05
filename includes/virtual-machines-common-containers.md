


Azure は、物理コンピューター ハードウェアをエミュレートした仮想マシンを利用して、ソフトウェア デプロイメントの移動を迅速化し、物理ハードウェアよりも格段に効率的なリソース統合を可能にするすばらしいクラウド ソリューションです。この数年間、[Docker](https://www.docker.com) のコンテナー アプローチと Docker エコシステムが業界に大きな影響を与えたこともあり、Linux のコンテナー テクノロジは分散型ソフトウェアの開発や管理のあり方を大きく変えています。コンテナー内のアプリケーション コードは、ホストの Azure VM からだけでなく、同じ VM 上にあるその他のコンテナーからも分離されるため、Azure VM がもたらす開発やデプロイメントのアジリティをアプリケーション レベルでさらに高めることにつながります。

**しかし、このことはもはやニュースではありません。** 注目すべきは、Docker がもたらす次のようなメリットを Azure で活用できるようになったということです。

- 状況に応じて、[さまざま](../articles/virtual-machines/virtual-machines-linux-docker-machine.md)[な](../articles/virtual-machines/virtual-machines-linux-dockerextension.md)方法で Docker ホストを作成できる
- [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) で **marathon** や **swarm** といったオーケストレーターを使用して、コンテナー ホストのクラスターを作成できる
- [Azure リソース マネージャー](../articles/resource-group-overview.md)と[リソース グループ テンプレート](../articles/resource-group-authoring-templates.md)を使用して、複雑な分散アプリケーションのデプロイや更新を簡略化できる
- 自社製かオープン ソースかを問わず、さまざまな構成管理ツールを統合できる

また、VM や Linux コンテナーを Azure 上でコーディングできるので、VM とコンテナーの*オーケストレーション* ツールを使用して Virtual Machines (VM) のグループを作成し、Linux コンテナーと [Windows コンテナー](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)の両方にアプリケーションをデプロイすることができます。

この記事では、これらの技術の概念について俯瞰的に説明とともに、 Azure でのコンテナーとクラスターの使用に関連する詳細情報、チュートリアル、および製品へのリンクを紹介します。技術については既にご存知で、リンクだけを確認したいという方は[コンテナーを操作するためのツールに関するページ](#tools-for-working-with-containers)でご確認ください。

## 仮想マシンとコンテナーの違い

仮想マシンは、[ハイパーバイザー](http://en.wikipedia.org/wiki/Hypervisor)によって提供される隔離されたハードウェア仮想化環境内で実行されます。Azure では、[Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) サービスによってすべてが管理されるため、ユーザーはオペレーティング システムを選択し、必要に応じて構成を変更したり、独自のカスタム VM をアップロードするだけで Virtual Machines を作成できます。Virtual Machines は市場で長年使用され、その価値を広く認められているテクノロジであり、オペレーティング システムの管理やアプリケーションの構成を行うためのツールも多数提供されています。仮想マシンで実行されるプログラムはホスト オペレーティング システムからは見えず、仮想マシン内で実行されているアプリケーションやユーザーの観点から見ると、仮想マシンは自立した物理コンピューターのように見えます。

[Linux コンテナー](http://en.wikipedia.org/wiki/LXC)は Docker などのツールを使用して作成、ホストされ、ハイパーバイザーによる分離機能を必要としません。代わりにコンテナー ホストによって、特定のカーネル機能と独自の分離型ファイル システムだけが (最小限の単位で) コンテナー (およびそのアプリケーション) に公開されます。コンテナーの内部で実行されるアプリケーションから見ると、コンテナーは独立したオペレーティング システム インスタンスのように見えます。内部のアプリケーションからは、コンテナーの外部にあるその他のプロセスやリソースは見えません。

この分離型の実行モデルでは、Docker ホスト コンピューターのカーネルが共有され、コンテナーのディスク要件にオペレーティング システム全体を含める必要がなくなるため、コンテナーの起動時間と、必要なディスク ストレージのオーバーヘッドがかなり縮小されます。

実に合理的です。

Windows コンテナーは、Linux コンテナーと同じメリットを Windows 上のアプリケーションに提供するためのものです。Windows コンテナーは、Docker イメージのフォーマットと Docker API をサポートしていますが、PowerShell を使用して管理することもできます。2 つのコンテナーのランタイムは、Windows コンテナー、Windows コンテナー、Hyper-V コンテナーで使用可能です。Hyper-V コンテナーは、非常に強力に最適化された仮想マシンで各コンテナーをホストすることで分離のレイヤーを追加します。Windows コンテナーの詳細については、[Windows コンテナーに関するページ](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)をご覧ください。Azure で Windows コンテナーを試すには、[Windows コンテナー Azure クイック スタート](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/azure_setup)を参照してください。

これも大きな魅力と言えます。

### 話がうますぎるとお思いですか。

確かに、メリットばかりを言い過ぎたかもしれません。コンテナーも他のテクノロジと同様、分散アプリケーションにまつわる面倒な作業をすべて魔法のように消してくれるわけではありません。しかし、次のようなメリットをもたらしてくれることは事実です。

- アプリケーション コードをすばやく開発し、広範囲に共有できる
- テストにかかる時間を短縮し、その信頼性を高めることができる
- デプロイにかかるを短縮し、その信頼性を高めることができる

ただし、コンテナーを実行するにはコンテナー ホスト (オペレーティング システム) が必要です。Azure でいうところの Azure 仮想マシンです。コンテナーのメリットを活用するには、コンテナーをホストする VM インフラストラクチャが必要なのです。しかし、コンテナーの利点は実行するための VM を選ばないという点にあります (コンテナーの実行環境として Linux と Windows のどちらが適しているか、などといったことは重要になりますが)。

## コンテナーのメリット

さまざまなメリットが期待できますが、[Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) や [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md) のように、単一サービスやマイクロサービス指向の分散アプリケーションを効果的に作成できるのが第一のメリットです。これにより、大規模で結合性の強いコンポーネントではなく、より小規模な構成パーツによってアプリケーションを設計することができます。

これは、必要な場所に必要なタイミングで VM をレンタルできる、Azure のようなパブリック クラウド環境においては特に有効です。分離性を確保し、デプロイメントを迅速化し、便利なオーケストレーション ツールを活用できるだけでなく、アプリケーション インフラストラクチャをより効率的に管理することができます。

たとえば現在、高可用性を備えた大規模な分散アプリケーションのデプロイメントを、9 つの Azure VM で構成しているとします。このアプリケーションのコンポーネントをコンテナーでデプロイすれば、わずか 4 つの VM を使用して、20 個のコンテナー内にアプリケーション コンポーネントをデプロイし、冗長性と負荷分散を実現することができます。

これは単なる例に過ぎませんが、組織のシナリオに合わせてこのようなデプロイメントを行えば、Azure VM ではなくコンテナーの追加によって処理パワーを調整し、残りの CPU パワーをより効率的に使用することができます。

またコンテナーは、マイクロサービス アプローチ以外にもさまざまなシナリオに適用できます。ここからは、マイクロサービスとコンテナーのメリットについてより具体的に説明していきます。

### 開発者にとってのコンテナーの利点

コンテナー テクノロジが新たなメリットをもたらすことは一般的な観点からも明らかですが、事業部単位でもさまざまなメリットが期待できます。Docker コンテナーの例で見てみましょう。このトピックでは Docker について深く掘り下げることはしませんが (詳細については、「[What is Docker?](https://www.docker.com/whatisdocker/)」や [Wikipedia](http://wikipedia.org/wiki/Docker_%28software%29) を参照してください)、Docker とそのエコシステムは開発者と IT プロフェッショナルに大きなメリットをもたらします。

Docker コンテナーは開発者の間で急速に普及していますが、これは、Linux および Windows コンテナーを使いやすくする次のようなメリットがあるためです。

- シンプルなコマンドを使用して固定イメージを作成し、それらを簡単にデプロイできるだけでなく、Dockerfile を使用してそれらのイメージ作成を自動化することができる
- シンプルな [Git](https://git-scm.com/) スタイルのコマンドを使用して、[パブリック](https://registry.hub.docker.com/)や[プライベートの Docker レジストリにイメージを](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md)プッシュ/プルし、それらを簡単に共有できる
- コンピューター単位ではなく、分離されたアプリケーション コンポーネント単位で開発が行える
- さまざまなツールを使用して、Docker コンテナーや各種ベース イメージの構成を把握できる

### オペレーション スタッフや IT プロフェッショナルにとってのコンテナーの利点

IT コンテナーと仮想マシンの組み合わせは、オペレーション スタッフや IT プロフェッショナルにもメリットをもたらします。

- コンテナー内のサービスを VM ホストの実行環境から分離できる
- コンテナー内のコードを完全に統一できる
- コンテナー内のサービスをすばやく起動、停止できるだけでなく、開発環境、テスト環境、および運用環境間の移動もすばやく行える。

これら機能は、老舗の大手企業にとっては特に魅力的なものです。このような企業の IT スタッフは、事業継続だけでなく、顧客満足度や顧客リーチの改善のために必要な各種のタスクに対し、処理パワーなどのリソースをバランスよく割り当てなければならないからです。小規模企業、ISV、新興企業などでも同様の要件はありますが、具体的な事情は多少異なるかもしれません。

## 仮想マシンの役割

仮想マシンはクラウド コンピューティングのバックボーンを提供するものであり、そのことは今でも変わりません。仮想マシンは起動が遅く、ディスク フットプリントが大きく、マイクロサービス アーキテクチャに直接マップすることもできませんが、非常に重要な役割を担っています。

1. ホスト コンピューターに対する強力なセキュリティ保護機能を標準で備えている
2. 主要な OS とアプリケーション構成をすべてサポートしている
3. コマンドや管理ツールのエコシステムが長年にわたって整備されている
4. コンテナーをホストするための実行環境を提供してくれる

最後の項目は特に重要です。コンテナー内のアプリケーションでは、プロセスの呼び出し方法に応じて特定のオペレーティング システムや CPU タイプが必要になるからです。コンテナーをインストールするには、あくまでも VM が必要です。コンテナーはデプロイするアプリケーションの格納手段であり、VM やオペレーティング システムに取って代わるものではないのです。

## VM とコンテナーの大まかな機能比較

次の表は、VM と Linux コンテナーの機能 の違いを、大規模な作業を伴わない機能に限って、きわめて大まかに示したものです。どちらの機能がより有効かは、組織のニーズによって変わってくる場合もあります。また、どのアプリケーションでも、追加作業によって機能のサポート レベルを向上させることは可能です (特にセキュリティ面)。

| 機能 | VM | コンテナー |
| :------------- |-------------| ----------- |
| "既定" のセキュリティ サポート | より充実している | 充実度においてやや劣る |
| 必要なディスク上のメモリ | OS 全体とアプリ | アプリ要件のみ |
| 起動にかかる時間 | 非常に長い: OS のブートとアプリの読み込み | 非常に短い: カーネルが既に実行されているため必要なのはアプリの起動のみ |
| 移植性 | 適切な準備をすれば移植可能 | イメージ フォーマット内でのみ移植可能 (通常はより限定的) |
| イメージの自動化 | OS やアプリによって大きく異なる | [Docker レジストリ](https://registry.hub.docker.com/)など

## VM とコンテナーのグループ作成とその管理

ここまでお読みになって、設計者、開発者、IT オペレーション スペシャリストの皆さんはこう思っているのではないでしょうか。「これなら何でも自動化できる。これこそ真の DCaaS (Data-Center-As-A-Service) だ」と。

その通りです。コンテナーにはそれだけの可能性があります。Azure VM のグループを管理したり、スクリプトを使用してカスタム コードを挿入できるシステムは既に多数存在しています (その多くは [CustomScriptingExtension for Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) や [CustomScriptingExtension for Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) を使用したものです)。既に使用している方もいらっしゃるかもしれませんが、PowerShell や Azure CLI のスクリプトを使用すれば、Azure デプロイメントを自動化することもできます。

また多くの場合、これらの機能は [Puppet](https://puppetlabs.com/) や [Chef](https://www.chef.io/) などのツールに移行され、VM の作成や構成を柔軟に自動化する目的に使用されます。(Azure でこれらのツールを使用する方法については、[こちら](#tools-for-working-with-containers)のリンクを参照してください。)

### Azure のリソース グループ テンプレート

Azure では先ごろ、[Azure リソース管理](../articles/virtual-machines/virtual-machines-windows-compare-deployment-models.md) REST API と、この API を簡単に使用できるように更新された、PowerShell および Azure CLI ツールをリリースしました。[Azure リリース マネージャー テンプレート](../articles/resource-group-authoring-templates.md)と Azure リソース管理 API、および下記のツールを使用すれば、アプリケーション トポロジ全体を効率的にデプロイ、変更、または再デプロイできます。

- [テンプレートを使用した Azure ポータル](https://github.com/Azure/azure-quickstart-templates)&mdash;"DeployToAzure" ボタンを使用するなど
- [Azure CLI](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md)
- [Azure PowerShell モジュール](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md)

### Azure VM とコンテナーのグループの一括的なデプロイメントと管理

複数の VM グループを一括でデプロイし、それらのデプロイメントに Docker (またはその他の Linux コンテナー ホスト システム) をインストールして、自動化可能なグループにするための主なシステムを紹介します。ダイレクト リンクは、[コンテナーとツール](#containers-and-vm-technologies)のセクションを参照してください。機能の範囲はシステムによって異なります。また、ここに挙げるものがこの種のシステムのすべてというわけではありません。どのシステムが便利かは、ユーザーのスキル セットやシナリオによって変わってきます。

Docker は、独自の VM 作成ツール セット ([docker-machine](../articles/virtual-machines/virtual-machines-linux-docker-machine.md)) と、負荷分散に対応した Docker コンテナー クラスター管理ツール ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md)) を提供しています。また [Azure Docker VM Extension](https://github.com/Azure/azure-docker-extension/blob/master/README.md) では、構成済みのアプリケーション コンテナーを複数のコンテナーにわたってデプロイできる、[`docker-compose`](https://docs.docker.com/compose/) が既定でサポートされています。

[Mesosphere のデータ センター オペレーティング システム (DCOS)](http://docs.mesosphere.com/install/azurecluster/) も有効なシステムです。DCOS は、[Mesos](http://mesos.apache.org/) というオープン ソースの "分散システム カーネル" をベースにしています、このカーネルでは、データ センターが 1 つのアドレス可能サービスとして扱われます。DCOS には、いくつかの重要なシステム ([Spark](http://spark.apache.org/) や [Kafka](http://kafka.apache.org/) など) に対応した組み込みパッケージが備わっているほか、[Marathon](https://mesosphere.github.io/marathon/) (コンテナー管理システム) や [Chronos](https://mesos.github.io/chronos/) (分散スケジューラー) などの組み込みサービスも含まれています。Mesos は、Twitter、AirBnb、およびその他の Web スケール ビジネスに影響を受けて開発されたものです。また、**swarm** をオーケストレーション エンジンとして使用することもできます。

[Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) は、Google に影響を受けて開発された、VM およびコンテナー グループ管理のためのオープン ソース システムです。[Kubernetes を Weave と併用して、ネットワーク サポートを提供する](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)こともできます。

[Deis](http://deis.io/overview/) は、独自のサーバー上アプリケーションを簡単にデプロイして管理できる、オープン ソースの PaaS (Platform-as-a-Service) です。Deis はDocker と CoreOS をベースとしており、それによって、Heroku 風のワークフローを使用した軽量な PaaS を実現しています。Azure では、[3 ノードの Azure VM グループを簡単に作成して Deis をインストール](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md)した後、[Hello World Go アプリケーションをインストール](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md#deploy-and-scale-a-hello-world-application)できます。

[CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html) は、最適なフット プリントと Docker サポート、および独自のコンテナー システム ([rkt](https://github.com/coreos/rkt)) を備えた Linux ディストリビューションで、[fleet](https://coreos.com/using-coreos/clustering/) というコンテナー グループ管理ツールも備えています。

Ubuntu も非常にポピュラーな Linux ディストリビューションで、Docker のサポートに優れているほか、[Linux (LXC スタイル) クラスター](https://help.ubuntu.com/lts/serverguide/lxc.html)もサポートしています 。

## Azure VM とコンテナーを操作するためのツール

コンテナーと Azure VM の操作には各種のツールを使用します。このセクションでは、コンテナー、グループ、またはより大きな構成を扱ううえで特に有用かつ重要な概念とツールを紹介するとともに、それらを操作するためのオーケストレーション ツールを列挙します。

> [AZURE.NOTE] この分野のトレンドはきわめて目まぐるしく変化するため、マイクロソフトではトピックとそのリンクをできる限り最新に維持するよう努めていますが、更新が追いつかない場合もあります。興味のあるテーマについては、ご自身でも検索を行い、最新の情報をチェックしてください。

### コンテナーと VM のテクノロジ

主な Linux コンテナー テクノロジ:

- [Docker](https://www.docker.com)
- [LXC](https://linuxcontainers.org/)
- [CoreOS と rkt](https://github.com/coreos/rkt)
- [Open Container Project](http://opencontainers.org/)
- [RancherOS](http://rancher.com/rancher-os/)

Windows コンテナー関連のリンク:

- [Windows コンテナー](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Visual Studio Docker 関連のリンク:

- [Visual Studio 2015 RC Tools for Docker - Preview](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Docker ツール:

- [Docker デーモン](https://docs.docker.com/installation/#installation)
- Docker クライアント
	- [Chocolatey の Windows Docker クライアント](https://chocolatey.org/packages/docker)
	- [Docker のインストール手順](https://docs.docker.com/installation/#installation)


Docker on Microsoft Azure:

- [Azure での Linux 用 Docker VM 拡張機能](../articles/virtual-machines/virtual-machines-linux-dockerextension.md)
- [Azure の Docker 用 VM 拡張機能のユーザー ガイド](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
- [Azure コマンド ライン インターフェイス (Azure CLI) での Docker VM 拡張機能の使用](../articles/virtual-machines/virtual-machines-linux-classic-cli-use-docker.md)
- [Azure ポータルでの Docker VM 拡張機能の使用](../articles/virtual-machines/virtual-machines-linux-classic-portal-use-docker.md)
- [Azure で docker マシンを使用する方法](../articles/virtual-machines/virtual-machines-linux-docker-machine.md)
- [Azure 上の Swarm における Docker の使用方法](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md)
- [Azure での Docker および Compose の概要](../articles/virtual-machines/virtual-machines-linux-docker-compose-quickstart.md)
- [Azure のリソース グループ テンプレートを使用して Azure 上に Docker ホストをすばやく作成する方法](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
- コンテナー内アプリケーションのための[組み込み `compose` サポート](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys)
- [Azure への独自のプライベート Docker Registry のデプロイ](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md)

Linux ディストリビューションと Azure での導入例:

- [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

構成、クラスター管理、およびコンテナー オーケストレーション:

- [CoreOS の Fleet](https://coreos.com/using-coreos/clustering/)

-	Deis
	- [3 ノードの Azure VM グループを作成し、Deis をインストールして、Hello World Go アプリケーションを起動する方法](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md)

-	Kubernetes
	- [CoreOS と Weave を使用した Kubernetes クラスターのデプロイの自動化に関する包括的なガイド](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

-	[Mesos](http://mesos.apache.org/)
	-	[Mesosphere のデータ センター オペレーティング システム (DCOS)](http://beta-docs.mesosphere.com/install/azurecluster/)

-	[Jenkins](https://jenkins-ci.org/) と [Hudson](http://hudson-ci.org/)
	- [ブログ: Azure 用 Jenkins スレーブ プラグイン](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub のリポジトリ: Azure 用 Jenkins ストレージ プラグイン](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [サード パーティ: Azure 用 Hudson スレーブ プラグイン](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [サード パーティ: Azure 用 Hudson ストレージ プラグイン](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

-	[Azure Automation](https://azure.microsoft.com/services/automation/)
	- [ビデオ: Linux VM で Azure Automation を使用する方法](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

-	Linux 用の Powershell DSC
    - [ビデオ: Linux 用 Powershell DSC を実行する方法](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: Docker クライアント DSC](https://github.com/anweiss/DockerClientDSC)

## 次のステップ

[Docker](https://www.docker.com) と [Windows コンテナー](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)についてチェックしてください。

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->

<!---HONumber=AcomDC_0824_2016-->