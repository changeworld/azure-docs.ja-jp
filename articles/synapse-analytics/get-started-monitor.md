---
title: チュートリアル:Azure Synapse Analytics の使用を開始する - Synapse ワークスペースを監視する
description: このチュートリアルでは、ご利用の Synapse ワークスペースでのアクティビティを監視する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 10/15/2020
ms.openlocfilehash: 2970bb58bb73d52c75729b00a8209a9c576d4ec0
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427413"
---
# <a name="monitor-your-synapse-workspace"></a>Synapse ワークスペースを監視する

このチュートリアルでは、ご利用の Synapse ワークスペースでのアクティビティを監視する方法について説明します。 SQL、Apache Spark、および Pipelines の現在および過去のアクティビティを監視できます。 

## <a name="introduction-to-the-monitor-hub"></a>監視ハブの概要

Synapse Studio を開き、 **[監視]** ハブに移動します。 ここには、ワークスペースで発生したすべてのアクティビティの履歴と、現在アクティブになっているアクティビティが表示されます。 

* **[Integration]\(統合\)** で、パイプライン、トリガー、統合ランタイムを監視できます
* **[アクティビティ]** で、Spark と SQL のアクティビティを監視できます。 

## <a name="integration"></a>統合

1. **[Integration]\(統合\) > [パイプライン]** の順に移動します。 このビューでは、ご利用のワークスペース内で特定のパイプラインが実行された時刻をすべて確認できます。 
1. 前の手順で実行したパイプラインを見つけて、その **パイプライン名** をクリックし、詳細を表示します。
1. Synapse Studio の上部近くにある **[階層リンク バー]** をクリックします。 **[すべてのパイプラインの実行]** をクリックすると、前のビューに戻ります。

## <a name="apache-spark-activities"></a>Apache Spark アクティビティ

1. **[Integration]\(統合\) > [アクティビティ] > [Apache Spark applications]\(Apache Spark アプリケーション\)** の順に移動します。 これで、ご利用のワークスペース内で実行中の、または実行されたすべての Spark アプリケーションを確認できます。
1. 現在実行されていないアプリケーションを見つけて、その **[アプリケーション名]** をクリックします。 これで、その Spark アプリケーションの詳細を確認できます。
1. Apache Spark に慣れている場合は、 **[Spark History Server]** をクリックすることで、標準の Apache Spark History Server UI を見つけることができます。

## <a name="sql-activities"></a>SQL アクティビティ

1. **[Integration]\(統合\) > [アクティビティ] > [SQL requests]\(SQL 要求\)** の順に移動します。
1. このビューでは、SQL 要求を確認できます。
1. 監視する **[プール]** を選択します。 これで、そのプールにあるご利用のワークスペース内で実行中の、または実行されたすべての SQL 要求を確認できます。
1. 特定の SQL 要求を検索し、その項目の上にマウス ポインターを置きます。 マウス ポインターを合わせると、SQL スクリプト アイコンが表示されるのがわかります。
1. SQL スクリプト アイコンをクリックして、SQL 要求の全文を表示します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Knowledge Center を探索する](get-started-knowledge-center.md)
