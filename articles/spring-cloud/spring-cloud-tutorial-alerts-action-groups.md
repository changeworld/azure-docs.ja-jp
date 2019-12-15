---
title: チュートリアル:アラートとアクション グループを使用して Azure Spring Cloud のリソースを監視する | Microsoft Docs
description: Spring Cloud のアラートの使用方法について説明します。
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689567"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>チュートリアル:アラートとアクション グループを使用して Spring Cloud のリソースを監視する

Azure Spring Cloud のアラートは、使用可能なストレージ、要求の速度、データ使用状況などの条件に基づくリソースの監視をサポートします。 速度や条件が定義済みの仕様を満たしたときにアラートから通知が送信されます。

アラートのパイプラインを設定するには、次の 2 つの手順があります。 
1. アラートがトリガーされたときに実行されるアクション (メール、SMS、Runbook、Webhook など) を含んだアクション グループを設定します。 アクション グループは、異なるアラートで再利用できます。
2. アラート ルールを設定します。 これらのルールによってメトリックのパターンが、ターゲット リソース、メトリック、条件、累計時間などに基づいてアクション グループにバインドされます。

## <a name="prerequisites"></a>前提条件
Azure Spring の要件に加え、このチュートリアルでは、次のリソースを利用します。

* デプロイされた Azure Spring Cloud インスタンス。  [クイックスタート](spring-cloud-quickstart-launch-app-cli.md)に従って始めてください。

* 監視する Azure リソース (記事「[Azure Cosmos DB で Spring Data Apache Cassandra API を使用する方法](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)」で構築したデータベースなど)。
 
以降の手順では、Spring Cloud インスタンスの左側のナビゲーション ペインにある **[アラート]** オプションから **[アクション グループ]** と **[アラート]** の両方を初期化します (この手順は、Azure portal の **[Monitor Overview]\(監視の概要\)** ページから開始することもできます)。 

リソース グループから自分の Spring Cloud インスタンスに移動します。 左側のペインで **[アラート]** を選択し、 **[アクションの管理]** を選択します。

![ポータルのリソース グループ ページのスクリーンショット](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>アクション グループを設定する

新しい**アクション グループ**を初期化する手順を開始するには、 **[+ アクション グループの追加]** を選択します。

![ポータルの [アクション グループの追加] のスクリーンショット](media/alerts-action-groups/action-1.png)

**[アクション グループの追加]** ページでは:

 1. **[アクション グループ名]** と **[短い名前]** を指定します。

 1. **[サブスクリプション]** と **[リソース グループ]** を指定します。

 1. **[アクション名]** を指定します。

 1. **[アクション タイプ]** を選択します。  アクティブ化時に実行されるアクションを定義するための別のペインが右側に表示されます。

 1. 右側のペインにあるオプションを使用してアクションを定義します。  この場合は、メール通知を使用します。

 1. 右側の操作ペインで **[OK]** をクリックします。

 1. **[アクション グループの追加]** ダイアログで **[OK]** をクリックします。 

  ![ポータルのアクション定義のスクリーンショット](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>アラートを設定する 

前の手順では、メールを使用する**アクション グループ**を作成しました。 電話による通知や Webhook、Azure 関数なども使用できます。  

**アラート**を構成するには、 **[アラート]** ページに戻って **[アラート ルールの管理]** をクリックします。

  ![ポータルのアラート定義のスクリーンショット](media/alerts-action-groups/alerts-2.png)

1. アラート対象の**リソース**を選択します。

1. **[+ 新しいアラート ルール]** をクリックします。

  ![ポータルの [新しいアラート ルール] のスクリーンショット](media/alerts-action-groups/alerts-3.png)

1. **[ルールの作成]** ページで、 **[リソース]** 、 **[条件]** 、 **[アクション]** を指定します。  **[アクション]** ペインで、先ほど定義した**アクション グループ**を選択します。

1. **[アラートの詳細]** で、アラート ルールに名前を付けます。

1. **[アラート ルールの作成]** をクリックします。

  ![ポータルの [新しいアラート ルール] のスクリーンショット](media/alerts-action-groups/alerts-4.png)

新しいアラート ルールが有効になっていることを確認します。

  ![ポータルの [新しいアラート ルール] のスクリーンショット](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>次の手順
* [Azure portal でのアクション グループの作成および管理](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [アクション グループの SMS アラート動作](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [チュートリアル:Azure Spring Cloud で分散トレースを使用する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
