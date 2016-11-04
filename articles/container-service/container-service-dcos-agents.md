---
title: パブリックおよびプライベート DC/OS エージェント プール ACS | Microsoft Docs
description: パブリックおよびプライベートのエージェント プールが Azure Container Service クラスターで機能する仕組み。
services: container-service
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker、コンテナー、マイクロ サービス、Mesos、Azure

ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: adegeo

---
# Azure Container Service の DC/OS エージェント プール
DC/OS Azure Container Service では、エージェントをパブリック プールまたはプライベート プールに分割します。どちらのプールにもデプロイできますが、コンテナー サービスのコンピューター間のアクセスに影響があります。コンピューターはインターネットに公開することも (パブリック)、内部だけで維持することも (プライベート) できます。この記事では、パブリック プールとプライベート プールがある理由の概要について説明します。

### プライベート エージェント
プライベート エージェント ノードは、ルーティング不可能なネットワークを介して実行されます。このネットワークには、管理者ゾーンまたはパブリック ゾーン エッジ ルーターを介してのみアクセスできます。既定では、DC/OS は、プライベート エージェント ノードでアプリを起動します。ネットワーク セキュリティの詳細については、[DC/OS のドキュメント](https://dcos.io/docs/1.7/administration/securing-your-cluster/)を参照してください。

### パブリック エージェント
パブリック エージェント ノードは、パブリックにアクセスできるネットワークを介して DC/OS アプリとサービスを実行します。ネットワーク セキュリティの詳細については、[DC/OS のドキュメント](https://dcos.io/docs/1.7/administration/securing-your-cluster/)を参照してください。

## エージェント プールの使用
既定では、**Marathon** は新しいアプリケーションを*プライベート* エージェント ノードにデプロイします。アプリケーションの作成中に、アプリケーションを*パブリック* ノードに明示的にデプロイする必要があります。**[Optional (オプション)]** タブを選択し、**[Accepted Resource Roles (承認されたリソース ロール)]** の値として「**slave\_public**」と入力します。このプロセスについては、[ここ](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)と [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) のドキュメントに記載されています。

## 次のステップ
[DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)の詳細をお読みください。

DC/OS コンテナーへのパブリック アクセスが可能になるように Azure によって提供される[ファイアウォールを開く](container-service-enable-public-access.md)方法について説明します。

<!---HONumber=AcomDC_0907_2016-->