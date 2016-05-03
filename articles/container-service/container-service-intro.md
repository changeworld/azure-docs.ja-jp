<properties
   pageTitle="Azure Container Service の概要 | Microsoft Azure"
   description="Azure Container Service を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、コンテナー、マイクロ サービス、Mesos、Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Azure Container Service の概要

Azure Container Service を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。Azure Container Service では、評判のよいオープンソースのスケジューリングおよびオーケストレーション ツールの最適化された構成を使用します。これにより、既存のスキルを使用して、または増え続けている大規模な専門家コミュニティの知恵に頼って、Microsoft Azure にコンテナーベースのアプリケーションをデプロイおよび管理できます。

<br /> ![Azure Container Service では、Azure 上の複数のホストのコンテナー化されたアプリケーションを管理できます。](./media/acs-intro/acs-cluster.png) <br /><br />

Azure Container Service では、Docker コンテナー形式を活用し、アプリケーション コンテナーの完全な移植性を保証しています。また、このようなアプリケーションを何千、何万ものコンテナーに拡張できるように、ユーザーの選択に応じて Marathon と DC/OS または Docker Swarm もサポートします。

Azure Container Service を使用することで、オーケストレーション レイヤーでの移植性も含め、アプリケーションの移植性を維持しながら、エンタープライズ レベルの Azure の機能を活用することができます。

Azure Container Service の使用
-----------------------------

Azure Container Service は、今日お客様に人気のあるオープンソースのツールとテクノロジを使用することで、コンテナー ホスト環境を提供することを目的としています。これに対応するために、選択したオーケストレーター用の標準 API エンドポイントを公開しています。これらのエンドポイントを使用することにより、それらのエンドポイントと通信できる任意のソフトウェアを活用できます。たとえば、Docker Swarm エンドポイントの場合、Docker コマンド ライン インターフェイス (CLI) を使用することができます。DC/OS の場合、DCOS CLI を使用することもできます。

Azure Container Service を使用した Docker クラスターの作成
-------------------------------------------------------

Azure Container Service の使用を開始するには、Azure Resource Manager のテンプレートを使用して Azure Container Service クラスターをデプロイします。このデプロイは、DC/OS または Docker Swarm を使用して、さまざまなサイズと可用性のオプションで構成できます。Azure Resource Manager のテンプレートは、Azure ポータルで Azure CLI を、または PowerShell を使用してデプロイできます。また、テンプレートを変更して、Azure の追加または高度な構成を含めることもできます。Azure Container Service クラスターのデプロイの詳細については、「[Azure コンテナー サービス クラスターのデプロイ](./container-service-deployment.md)」を参照してください。

アプリケーションのデプロイ
------------------------

Azure Container Service では、オーケストレーション用に Docker Swarm または DC/OS を選択できます。

### DC/OS の使用

DC/OS は、Apache Software Foundation のオープンソース プロジェクトです。[IT 業界の著名人](http://mesos.apache.org/documentation/latest/powered-by-mesos/)がユーザーおよび共同作成者として挙げられています。

![Swarm の構成がされた Azure Container Service のエージェントとマスターを示します。](media/acs-intro/dcos.png)

DC/OS には、優れた機能セットが含まれています。

-   10,000 ノードまでの拡張性

-   Apache ZooKeeper を使用したフォールト トレランス機能を備えたレプリケートされたマスターおよびスレーブ

-   Docker 形式コンテナーのサポート

-   Linux コンテナーを使用したタスク間のネイティブな分離

-   (メモリ、CPU、ディスクおよびポートの) マルチリソース スケジューリング

-   新しい並列アプリケーションを開発するための Java、Python、および C++ API

-   クラスターの状態を参照するための Web UI

DC/OS では、Azure Container Service でワークロードのスケジューリングに使用できる多数の[フレームワーク](http://mesos.apache.org/documentation/latest/frameworks/)をサポートしています。Azure Container Service には、既定で Marathon および Chronos フレームワークが含まれています。

#### Marathon および Chronos の使用

Marathon は、クラスター全体の cgroups (または Azure Container Service の場合は Docker 形式コンテナー) のサービスを、初期化および制御するシステムです。依存関係と時間ベースのスケジュールを処理する、フォールト トレランス機能を備えた DC/OS のジョブ スケジューラである Chronos にとって、最適なパートナーです。

Marathon および Chronos には、アプリケーションをデプロイできる Web UI があります。これには、`http://DNS\_PREFIX.REGION.cloudapp.azure.com` のような URL からアクセスできます (この、DNS\_PREFIX と REGION はいずれもデプロイ時に定義します)。独自の DNS 名を作成することも可能です。Marathon Web UI を使用したコンテナーの実行の詳細については、「[Web UI 経由のコンテナー管理](./container-service-mesos-marathon-ui.md)」を参照してください。

Marathon と Chronos との通信には、REST API を使用することも可能です。各ツールで利用可能なクライアント ライブラリが多数あります。さまざまな言語に対応するだけでなく、当然、任意の言語で HTTP プロトコルを使用することもできます。さらに、これらのスケジューラは、多数の人気の DevOps ツールによってもサポートされています。このため、Azure Container Service クラスターで作業する際に、オペレーション チームに最大限の柔軟性を提供します。Marathon REST API を使用したコンテナーの実行の詳細については、「[REST API を使用したコンテナー管理](./container-service-mesos-marathon-rest.md)」を参照してください。

### Docker Swarm の使用

Docker Swarm は、Docker のネイティブ クラスタリングを提供しています。Docker Swarm には標準の Docker API があるため、Docker デーモンと既に通信するすべてのツールは Swarm を使用し、Azure Container Service の複数のホストに透過的にスケーリングできます。

![Azure Container Service configured to use DC/OS--showing jumpbox, agents, and masters.](media/acs-intro/acs-swarm2.png)

Swarm クラスターでコンテナーを管理するためにサポートされているツールには、(これらに限定はされていませんが) 以下があります。

-   Dokku

-   Docker CLI および Docker Compose

-   Krane

-   Jenkins

ビデオ
------
AzureCon に関するお知らせ:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Azure Container Service の概要:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0420_2016-->