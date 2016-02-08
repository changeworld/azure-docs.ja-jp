<properties
   pageTitle="Azure Container Service の概要 | Microsoft Azure"
   description="Azure Container Service (ACS) を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。"
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、コンテナー、マイクロ サービス、Mesos、Azure"/>
   
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>

# Azure Container Service の概要

Azure Container Service (ACS) を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。最適に構成された評判のよいオープンソースのスケジューリングおよびオーケストレーション ツールが使用された ACS では、既存のスキルを使用して、または増え続けている大規模な専門家コミュニティの知恵に頼って、Microsoft Azure にコンテナーベースのアプリケーションをデプロイおよび管理できます。

<br /> ![ACS では、Azure 上の複数のホストのコンテナー化されたアプリケーションを管理できます。](./media/acs-intro/acs-cluster.png) <br /><br />

ACS では、Docker を活用し、アプリケーション コンテナーの完全な移植性を保証しています。また、何千、何万ものコンテナーにさえ拡張可能であることを保証している、ユーザーが選択している Marathon、Chronos と Apache Mesos または Docker Swarm もサポートしています。

Azure Container Service では、オーケストレーション レイヤーでの場合も含む、アプリケーションの移植性を維持しながら、エンタープライズ レベルの Azure の機能を使用できます。

このサービスはプレビュー期間中ですが、サービスのテストにご興味のある方は、[自己推薦](http://aka.ms/acspreview)していただきたいと思います。プレビュー アクセスが提供できたら、デプロイメント テンプレートおよび開始手順を含む詳細を電子メールでお送りします。これらのサービスを使用するには、Azure サブスクリプションが必要です。まだ所持していない場合は[無料試用版](https://azure.microsoft.com/ja-JP/pricing/free-trial/)にサインアップしてください。

Azure Container Service の使用
-----------------------------

Azure Container Service は、今日お客様に人気のあるオープン ソースのツールとテクノロジを使用した、コンテナー ホスト環境を提供することを目的としています。これに対応するために、Docker と選択したオーケストレーター用の標準 API エンドポイントを公開しています。これらのエンドポイントを使用すると、それらのエンドポイントと通信できる任意のソフトウェアを使用できます。たとえば、Docker Swarm エンドポイントでは、Docker Compose を使用でき、Apache Mesos では DCOS CLI を使用できます。

Azure Container Service を使用した Docker Cluster の構成
-------------------------------------------------------

プレビューに[要求](http://aka.ms/acspreview)を行い、アクセスが許可されたら、最初のクラスターのデプロイに使用できる、Azure リソース マネージャーの多数あるテンプレートのいずれかを Azure ポータルから使用できます。これらのテンプレートを使用すると、サービスを迅速に作成して、すぐにアプリケーションをデプロイ開始できます。クラスター サイズを決定し、アプリケーションの管理に Docker Swarm または Apache Mesos のいずれを使用するか決定するのみで開始できます。

また、[コマンドラインを使用すると](/documentation/articles/resource-group-template-deploy/)、これらの同じテンプレートを使用する Azure Container Service を作成できます。これらのテンプレートの構造を理解できたら、独自のものを記述し、Azure Container Service クラスターの作成を完全に自動化できます。

プレビュー参加者には、すべてのドキュメントとサポートを提供します。また、サービスを公開したら、ここに公開します。

アプリケーションのデプロイ
------------------------

プレビュー期間中、オーケストレーションには (DCOS Marathon および DCOS Chronos フレームワークで) Docker Swarm または Apache Mesos のいずれかの選択肢があります。

### Apache Mesos の使用

Apache Mesos は、Apache Software Foundation のオープン ソース プロジェクトです。[IT 業界の著名人](http://mesos.apache.org/documentation/latest/powered-by-mesos/)がユーザーおよび共同作成者として挙げられています。

![Swarm の構成がされた ACS のエージェントとマスターを示します。](media/acs-intro/acs-mesos.png)

Mesos には、優れた機能セットがあります。

-   何万ノードにも拡張できるスケーラビリティ

-   ZooKeeper を使用したフォールト トレランス機能を備えたレプリケートされたマスターおよびスレーブ

-   Docker コンテナーのサポート

-   Linux コンテナーを使用したタスク間のネイティブな分離

-   (メモリ、CPU、ディスクおよびポートの) マルチリソース スケジューリング

-   新しい並列アプリケーションを開発するための Java、Python、および C++ API

-   クラスターの状態を参照するための Web UI

Mesos では、Azure Container Service でワークロードのスケジューリングに使用できる多数の[フレームワーク](http://mesos.apache.org/documentation/latest/frameworks/)をサポートしています。ACS には、既定で Marathon および Chronos フレームワークが含まれています。

#### Marathon および Chronos の使用

Marathon は、クラスター全体の cgroups (または ACS の場合は Docker コンテナー) のサービスを、初期化および制御するシステムです。依存関係と時間ベースのスケジュールを処理する、フォールト トレランス機能を備えた Mesos のジョブ スケジューラである Chronos の最適なパートナーです。

Marathon および Chronos には、アプリケーションをデプロイする Web UI があります。これには、`http://DNS\_PREFIX.REGION.cloudapp.azure.com` のような URL からアクセスできます (この、DNS\_PREFIX と REGION はいずれもデプロイメント時に定義します)。独自の DNS 名を作成することも可能です。

Marathon と Chronos との通信には、REST API を使用することも可能です。各ツールには、さまざまな言語の多数のクライアント ライブラリがあり、任意の言語で HTTP プロトコルを使用することももちろん可能です。さらに、これらのスケジューラは、多数の人気の DevOps ツールによってもサポートされています。これは ACS クラスターで作業するオペレーション チームに、最大限の柔軟性を提供します。

プレビュー参加者には、すべてのドキュメントとサポートを提供します。また、サービスを公開したら、ここに公開します。

### Docker Swarm の使用

Docker Swarm は、Docker のネイティブ クラスタリングを提供しています。Docker Swarm には標準の Docker API があるため、Docker デーモンと既に通信するすべてのツールは Swarm を使用し、Azure Container Service の複数のホストに透過的にスケーリングできます。

![Apache Mesos の使用が構成がされた ACS のジャンプボックス、エージェントおよびマスターを示します。](media/acs-intro/acs-swarm.png)

Swarm クラスターでコンテナーを管理するためにサポートされているツールには、(これらに限定はされていませんが) 以下があります。

-   Dokku

-   Docker CLI および Docker Compose

-   Krane

-   Jenkins

プレビュー参加者には、すべてのドキュメントとサポートを提供します。また、サービスを公開したら、ここに公開します。

アクセスの取得
--------------

このサービスはプレビュー期間中ですが、サービスのテストにご興味のある方は、[自己推薦](http://aka.ms/acspreview)していただきたいと思います。これには、まず Azure サブスクリプションが必要です。まだ所持していない場合は[無料試用版](https://azure.microsoft.com/ja-JP/pricing/free-trial/)にサインアップしてください。

ビデオ
------
AzureCon に関するお知らせ:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

ACS の概要:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0128_2016-->