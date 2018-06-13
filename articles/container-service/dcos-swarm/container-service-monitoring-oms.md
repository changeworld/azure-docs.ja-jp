---
title: Azure DC/OS クラスターの監視 - Operations Management
description: Log Analytics で Azure Container Service DC/OS クラスターを監視します。
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: b326e5b686e14cefac4e6376bd3f26787ea1d10d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164593"
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>Log Analytics で Azure Container Service DC/OS クラスターを監視する

Log Analytics は、オンプレミスのインフラストラクチャやクラウド インフラストラクチャの管理および保護に役立つ、Microsoft のクラウド ベースの IT 管理ソリューションです。 コンテナー ソリューションは、コンテナー インベントリ、パフォーマンス、およびログを 1 つの場所で表示するのに役立つ、Log Analytics のソリューションです。 一元的な場所でログを表示して監査やコンテナーのトラブルシューティングを行い、ホスト上のノイズと消費の多いコンテナーを検索することができます。

![](media/container-service-monitoring-oms/image1.png)

コンテナー ソリューションの詳細については、[Log Analytics のコンテナー ソリューション](../../log-analytics/log-analytics-containers.md)に関するページを参照してください。

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>DC/OS Universe からの Log Analytics の設定


この記事では、DC/OS をセットアップし、クラスターに単純な Web コンテナー アプリケーションをデプロイしていることを前提としています。

### <a name="pre-requisite"></a>前提条件
- [Microsoft Azure サブスクリプション](https://azure.microsoft.com/free/) - 無料で取得できます。  
- Log Analytics ワークスペースのセットアップ - 下の「手順 3.」を参照
- [DC/OS CLI](https://dcos.io/docs/1.8/usage/cli/install/) がインストールされていること。

1. DC/OS ダッシュボードで、次のように [Universe] をクリックし、"OMS" を検索します。

![](media/container-service-monitoring-oms/image2.png)

2. **[インストール]** をクリックします。 バージョン情報と **[パッケージのインストール]** または **[Advanced Installation] (高度なインストール)** ボタンを含むポップアップが表示されます。 **[Advanced Installation (高度なインストール)]** をクリックすると、**[OMS specific configuration properties (OMS に固有の構成のプロパティ)]** ページが表示されます。

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. ここでは、`wsid` (Log Analytics ワークスペース ID) と `wskey` (ワークスペース ID の主キー) を入力するよう求められます。 `wsid` と `wskey` の両方を取得するには、<https://mms.microsoft.com> でアカウントを作成する必要があります。
手順に従ってアカウントを作成してください。 アカウントの作成が完了したら、次のように **[設定]**、**[接続されたソース]**、**[Linux サーバー]** の順にクリックして `wsid` と `wskey` を取得する必要があります。

 ![](media/container-service-monitoring-oms/image5.png)

4. 必要なインスタンスの数を選択し、[Review and Install] (確認およびインストール) ボタンをクリックします。 通常、インスタンスの数は、エージェント クラスター内の VM の数と同じにします。 OMS Agent for Linux は、監視のための情報やログ情報を収集する各 VM に個別のコンテナーとしてインストールされます。

## <a name="setting-up-a-simple-oms-dashboard"></a>単純な OMS ダッシュボードの設定

OMS Agent for Linux を VM にインストールしたら、次に OMS ダッシュボードを設定します。 これを行うには、OMS ポータルを使う方法と Azure Portal を使う方法の 2 つがあります。

### <a name="oms-portal"></a>OMS ポータル 

OMS ポータル (<https://mms.microsoft.com>) にログインし、**[ソリューション ギャラリー]** に移動します。

![](media/container-service-monitoring-oms/image6.png)

**[Solution Gallery (ソリューション ギャラリー)]** が表示されたら、**[コンテナー]** を選択します。

![](media/container-service-monitoring-oms/image7.png)

コンテナー ソリューションを選択すると、[OMS Overview (OMS の概要)] ダッシュボード ページにタイルが表示されます。 取り込んだコンテナー データに対してインデックスが作成されると、ソリューション ビューのタイルにその情報が表示されます。

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Azure Portal 

Azure Portal (<https://portal.microsoft.com/>) にログインします。 **[Marketplace]** で **[監視 + 管理]** を選択し、**[See All (すべて表示)]** をクリックします。 検索ボックスに「`containers`」と入力します。 検索結果に "コンテナー" が表示されます。 **[コンテナー]** を選択し、**[作成]** をクリックします。

![](media/container-service-monitoring-oms/image9.png)

**[作成]** をクリックすると、ワークスペースを指定するよう求められます。 ワークスペースを選択するか、ワークスペースがない場合は新しいワークスペースを作成します。

![](media/container-service-monitoring-oms/image10.PNG)

ワークスペースを選択したら、**[作成]** をクリックします。

![](media/container-service-monitoring-oms/image11.png)

Log Analytics コンテナー ソリューションの詳細については、[Log Analytics のコンテナー ソリューション](../../log-analytics/log-analytics-containers.md)に関するページを参照してください。

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>ACS DC/OS を使用して OMS エージェントの規模をスケールする方法 

実際のノード数に満たない OMS エージェントをインストールする必要がある場合や VM を追加して VMSS を拡大する場合は、`msoms` サービスの規模をスケールすることで目的の操作を実現できます。

Marathon または DC/OS UI サービスのタブに移動し、ノード数をスケールアップできます。

![](media/container-service-monitoring-oms/image12.PNG)

この操作を実行すると、OMS エージェントがデプロイされていない他のノードにデプロイできます。

## <a name="uninstall-ms-oms"></a>MS OMS のアンインストール

MS OMS をアンインストールするには、次のコマンドを入力します。

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>ご意見をお聞かせください。
どの部分が実用的でしたか。 足りない情報はありましたか。 この記事を実用的なものにするうえで他に必要なものはありますか。 <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a> までご意見をお寄せください。

## <a name="next-steps"></a>次の手順

 これで、コンテナーを監視するように Log Analytics が設定されたので、[コンテナー ダッシュボードを参照してください](../../log-analytics/log-analytics-containers.md)。
