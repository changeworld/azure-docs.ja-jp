---
title: (非推奨) Azure DC/OS クラスターの監視 - Datadog
description: Datadog を使用して Azure コンテナー サービス クラスターを監視します。 DC/OS の Web UI を使用して、クラスターに Datadog エージェントをデプロイします。
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166149"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(非推奨) Datadog を使用した Azure Container Service DC/OS クラスターの監視

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

この記事では、Azure コンテナー サービス クラスター内のすべてのエージェント ノードに Datadog エージェントをデプロイする方法について説明します。 この構成を行うには、Datadog のアカウントが必要です。 

## <a name="prerequisites"></a>前提条件
Azure Container Service によって構成されたクラスターを[デプロイ](container-service-deployment.md)して[接続](../container-service-connect.md)してください。 [Marathon UI](container-service-mesos-marathon-ui.md)の詳細を確認してください。 [https://datadoghq.com](https://datadoghq.com) に移動して、Datadog アカウントを設定します。 

## <a name="datadog"></a>Datadog
Datadog は監視サービスです。Azure コンテナー サービス クラスター内のコンテナーから監視データを収集することができます。 Datadog には、コンテナー内の特定のメトリックを表示できる Docker 統合ダッシュボードがあります。 コンテナーから収集されたメトリックは、CPU、メモリ、ネットワーク、I/O 別に整理されます。 メトリックは Datadog によってコンテナーとイメージに分割されます。 CPU 使用率の UI の例を以下に示します。

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Marathon を使用した Datadog デプロイメントの構成
以下の手順は、Marathon を使用して Datadog アプリケーションを構成し、クラスターにデプロイする方法を示しています。 

`http://localhost:80/` で DC/OS の UI にアクセスします。 DC/OS の UI が表示されたら、左下にある [Universe (ユニバース)] に移動し、[Datadog] を検索して、[Install (インストール)] をクリックします。

![Datadog package within the DC/OS Universe](./media/container-service-monitoring/datadog1.png)

この構成作業には、Datadog アカウントまたは無料試用版アカウントが必要です。 Datadog Web サイトにログインしたら、左側の [Integrations (統合)]、[[APIs (API)]](https://app.datadoghq.com/account/settings#api) の順にクリックします。 

![Datadog API key](./media/container-service-monitoring/datadog2.png)

次に、DC/OS Universe の Datadog 構成に自分の API キーを入力します。 

![Datadog configuration in the DC/OS Universe](./media/container-service-monitoring/datadog3.png) 

上記の構成で、インスタンス数を「10000000」に設定します。これでクラスターに新しいノードが追加されると、そのノードに対して自動的にエージェントがデプロイされます。 これは暫定的な措置です。 パッケージをインストールしたら、Datadog Web サイトに戻り、[[Dashboards (ダッシュ ボード)]](https://app.datadoghq.com/dash/list) を見つけます。 ここには、カスタム ダッシュボードと統合ダッシュボードが表示されます。 [Docker ダッシュボード](https://app.datadoghq.com/screen/integration/docker)には、クラスターの監視に必要なすべてのコンテナー メトリックが表示されます。 

