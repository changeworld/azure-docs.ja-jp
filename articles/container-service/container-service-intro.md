<properties
   pageTitle="Azure Container Service の概要 | Microsoft Azure"
   description="Azure Container Service (ACS) を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。"
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

Azure Container Service (ACS) を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。最適に構成された評判のよいオープンソースのスケジューリングおよびオーケストレーション ツールが使用された ACS では、既存のスキルを使用して、または増え続けている大規模な専門家コミュニティの知恵に頼って、Microsoft Azure にコンテナーベースのアプリケーションをデプロイおよび管理できます。

<br /> ![ACS では、Azure 上の複数のホストのコンテナー化されたアプリケーションを管理できます。](./media/acs-intro/acs-cluster.png) <br /><br />

ACS では、Docker コンテナー形式を活用し、アプリケーション コンテナーの完全な移植性を保証しています。また、何千、何万ものコンテナーにさえ拡張可能であることを保証している、ユーザーが選択している Marathon と Apache Mesos または Docker Swarm もサポートしています。

Azure Container Service では、オーケストレーション レイヤーでの場合も含む、アプリケーションの移植性を維持しながら、エンタープライズ レベルの Azure の機能を使用できます。

Azure Container Service の使用
-----------------------------

Azure Container Service は、今日お客様に人気のあるオープン ソースのツールとテクノロジを使用した、コンテナー ホスト環境を提供することを目的としています。これに対応するために、選択したオーケストレーター用の標準 API エンドポイントを公開しています。これらのエンドポイントを使用すると、それらのエンドポイントと通信できる任意のソフトウェアを使用できます。たとえば、Docker Swarm エンドポイントでは、Docker CLI を使用でき、Apache Mesos では DCOS CLI を使用できます。

Azure Container Service を使用した Docker Cluster の構成
-------------------------------------------------------

Azure Container Service の使用を開始するには、Azure リソース マネージャーのテンプレートを使用して ACS クラスターをデプロイします。このデプロイは、異なるサイズと可用性のオプションで構成でき、Apache Mesos または Docker Swarm を使用して構成します。Azure Resource Manager のテンプレートは、Azure ポータル、Azure CLI、または PowerShell を使用してデプロイできます。また、テンプレートを変更して、Azure の追加または高度な構成を含めることもできます。デプロイと ACS クラスターの詳細については、「[Azure コンテナー サービス クラスターのデプロイ](./container-service-deployment.md)」を参照してください。

アプリケーションのデプロイ
------------------------

プレビュー期間中、オーケストレーションには (DCOS Marathon および DCOS Chronos フレームワークで) Docker Swarm または Apache Mesos のいずれかの選択肢があります。

### Apache Mesos の使用

Apache Mesos は、Apache Software Foundation のオープン ソース プロジェクトです。[IT 業界の著名人](http://mesos.apache.org/documentation/latest/powered-by-mesos/)がユーザーおよび共同作成者として挙げられています。

![Swarm の構成がされた ACS のエージェントとマスターを示します。](media/acs-intro/acs-mesos.png)

Mesos には、優れた機能セットがあります。

-   何万ノードにも拡張できるスケーラビリティ

-   ZooKeeper を使用したフォールト トレランス機能を備えたレプリケートされたマスターおよびスレーブ

-   Docker 形式コンテナーのサポート

-   Linux コンテナーを使用したタスク間のネイティブな分離

-   (メモリ、CPU、ディスクおよびポートの) マルチリソース スケジューリング

-   新しい並列アプリケーションを開発するための Java、Python、および C++ API

-   クラスターの状態を参照するための Web UI

Mesos では、Azure Container Service でワークロードのスケジューリングに使用できる多数の[フレームワーク](http://mesos.apache.org/documentation/latest/frameworks/)をサポートしています。ACS には、既定で Marathon および Chronos フレームワークが含まれています。

#### Marathon および Chronos の使用

Marathon は、クラスター全体の cgroups (または ACS の場合は Docker 形式コンテナー) のサービスを、初期化および制御するシステムです。依存関係と時間ベースのスケジュールを処理する、フォールト トレランス機能を備えた Mesos のジョブ スケジューラである Chronos の最適なパートナーです。

Marathon および Chronos には、アプリケーションをデプロイする Web UI があります。これには、`http://DNS\_PREFIX.REGION.cloudapp.azure.com` のような URL からアクセスできます (この、DNS\_PREFIX と REGION はいずれもデプロイメント時に定義します)。独自の DNS 名を作成することも可能です。Marathon Web UI を使用したコンテナーの実行の詳細については、「[Web UI 経由のコンテナー管理](./container-service-mesos-marathon-ui.md)」を参照してください。

Marathon と Chronos との通信には、REST API を使用することも可能です。各ツールには、さまざまな言語の多数のクライアント ライブラリがあり、任意の言語で HTTP プロトコルを使用することももちろん可能です。さらに、これらのスケジューラは、多数の人気の DevOps ツールによってもサポートされています。これは ACS クラスターで作業するオペレーション チームに、最大限の柔軟性を提供します。Marathon REST API を使用したコンテナーの実行の詳細については、「[REST API によるコンテナー管理](./container-service-mesos-marathon-rest.md)」を参照してください。

### Docker Swarm の使用

Docker Swarm は、Docker のネイティブ クラスタリングを提供しています。Docker Swarm には標準の Docker API があるため、Docker デーモンと既に通信するすべてのツールは Swarm を使用し、Azure Container Service の複数のホストに透過的にスケーリングできます。

![Apache Mesos の使用が構成がされた ACS のジャンプボックス、エージェントおよびマスターを示します。](media/acs-intro/acs-swarm.png)

Swarm クラスターでコンテナーを管理するためにサポートされているツールには、(これらに限定はされていませんが) 以下があります。

-   Dokku

-   Docker CLI および Docker Compose

-   Krane

-   Jenkins

ビデオ
------
AzureCon に関するお知らせ:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

ACS の概要:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0323_2016-->