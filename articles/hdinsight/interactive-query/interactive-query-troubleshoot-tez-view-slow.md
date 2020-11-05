---
title: Azure HDInsight 内での Apache Ambari Tez ビューの読み込みに時間がかかる
description: Azure HDInsight で Apache Ambari Tez ビューの読み込みに時間がかかる場合や、まったく読み込まれない場合があります
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 0e294566da4c6f514704abc2ac014b8345020b5a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288851"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>シナリオ:Azure HDInsight 内での Apache Ambari Tez ビューの読み込みに時間がかかる

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari Tez ビューの読み込みに時間がかかる場合や、まったく読み込まれない場合があります。 Ambari Tez ビューを読み込むときに、応答しなくなっているプロセスがヘッドノードに表示されることがあります。

## <a name="cause"></a>原因

Yarn ATS API にアクセスすると、クラスター上で多数の Hive ジョブが実行されている場合に、2017 年 10 月より前に作成されたクラスター上でパフォーマンスが低下することがあります。

## <a name="resolution"></a>解像度

これは、2017 年 10 月に修正された問題です。 クラスターを再作成すると、この問題が再び発生することはなくなります。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]