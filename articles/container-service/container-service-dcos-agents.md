<properties
   pageTitle="パブリックおよびプライベートのエージェント プールの ACS |Microsoft Azure"
   description="パブリックおよびプライベートのエージェント プールが Azure Container Service クラスターで機能する仕組み。"
   services="container-service"
   documentationCenter=""
   authors="Thraka"
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
   ms.date="08/16/2016"
   ms.author="adegeo"/>

# エージェント プール

Azure Container Service では、エージェントを複数のプールに分割します。プールの各仮想マシンは同じですが、各プールは独自のサブネットを持ちます。そのため、各プールはパブリックまたはプライベートのどちらかとしてマークでき、コンテナー サービスにおけるマシン間のアクセシビリティに影響を与えます。

### プライベート エージェント

プライベート エージェント ノードは、ルーティング不可能なネットワークを介して実行されます。このネットワークには、管理者ゾーンまたはパブリック ゾーン エッジ ルーターを介してのみアクセスできます。既定では、DC/OS は、プライベート エージェント ノードでアプリを起動します。ネットワーク セキュリティの詳細については、[DC/OS のドキュメント](https://dcos.io/docs/1.7/administration/securing-your-cluster/)を参照してください。

### パブリック エージェント

パブリック エージェント ノードは、パブリックにアクセスできるネットワークを介して DC/OS アプリとサービスを実行します。ネットワーク セキュリティの詳細については、[DC/OS のドキュメント](https://dcos.io/docs/1.7/administration/securing-your-cluster/)を参照してください。

## エージェント プールの使用

既定では、**Marathon** は新しいアプリケーションを*プライベート* エージェント ノードにデプロイします。*パブリック* ノードには、明示的にアプリケーションをデプロイする必要があります。ただし、パブリック ノードへのデプロイは、チェックボックスをオンにするほど簡単ではありません。新しいアプリケーションの作成中に、**[オプション]** タブを選択し、**[Accepted Resource Roles (承認されたリソース ロール)]** に「**slave\_public**」と入力します。このプロセスについては、[ここ](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)と [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) のドキュメントに記載されています。

## 次のステップ

[DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)の詳細をお読みください。

<!---HONumber=AcomDC_0824_2016-->