---
title: "パブリックおよびプライベート DC/OS エージェント プール ACS | Microsoft Docs"
description: "パブリックおよびプライベートのエージェント プールが Azure Container Service クラスターで機能する仕組み。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker、コンテナー、マイクロ サービス、Mesos、Azure"
ms.assetid: 36d657c9-8845-4bf7-bed2-088323b67406
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a6d9ab6d95de936e4f1d28d47d4e1d74c080bdae


---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure Container Service の DC/OS エージェント プール
DC/OS Azure Container Service では、エージェントをパブリック プールまたはプライベート プールに分割します。 どちらのプールにもデプロイできますが、コンテナー サービスのコンピューター間のアクセスに影響があります。 コンピューターはインターネットに公開することも (パブリック)、内部だけで維持することも (プライベート) できます。 この記事では、パブリック プールとプライベート プールがある理由の概要について説明します。

### <a name="private-agents"></a>プライベート エージェント
プライベート エージェント ノードは、ルーティング不可能なネットワークを介して実行されます。 このネットワークには、管理者ゾーンまたはパブリック ゾーン エッジ ルーターを介してのみアクセスできます。 既定では、DC/OS は、プライベート エージェント ノードでアプリを起動します。 ネットワーク セキュリティの詳細については、 [DC/OS のドキュメント](https://dcos.io/docs/1.7/administration/securing-your-cluster/) を参照してください。

### <a name="public-agents"></a>パブリック エージェント
パブリック エージェント ノードは、パブリックにアクセスできるネットワークを介して DC/OS アプリとサービスを実行します。 ネットワーク セキュリティの詳細については、 [DC/OS のドキュメント](https://dcos.io/docs/1.7/administration/securing-your-cluster/) を参照してください。

## <a name="using-agent-pools"></a>エージェント プールの使用
既定では、 **Marathon** は新しいアプリケーションを *プライベート* エージェント ノードにデプロイします。 アプリケーションの作成中に、アプリケーションを *パブリック* ノードに明示的にデプロイする必要があります。 **[Optional (オプション)]** タブを選択し、**[Accepted Resource Roles (承認されたリソース ロール)]** の値として「**slave_public**」と入力します。 このプロセスについては、[こちら](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)と[ DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) のドキュメントに記載されています。

## <a name="next-steps"></a>次のステップ
[DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)の詳細をお読みください。

DC/OS コンテナーへのパブリック アクセスが可能になるように Azure によって提供される [ファイアウォールを開く](container-service-enable-public-access.md) 方法について説明します。




<!--HONumber=Nov16_HO3-->


