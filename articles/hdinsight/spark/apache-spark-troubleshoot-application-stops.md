---
title: Apache Spark Streaming アプリケーションが Azure HDInsight で 24 日後に停止する
description: Apache Spark Streaming アプリケーションは、24 日間の実行後に停止し、ログ ファイルにエラーはありません。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288031"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>シナリオ:Apache Spark Streaming アプリケーションが、Azure HDInsight で 24 日間実行された後に停止する

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

Apache Spark Streaming アプリケーションは、24 日間の実行後に停止し、ログ ファイルにエラーはありません。

## <a name="cause"></a>原因

`livy.server.session.timeout` 値は、Apache Livy がセッションの完了を待機する時間を制御します。 セッションの長さが `session.timeout` 値に達すると、Livy セッションとアプリケーションが自動的に強制終了されます。

## <a name="resolution"></a>解像度

長時間実行されるジョブの場合は、Ambari UI を使用して `livy.server.session.timeout` の値を大きくします。 URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` を使用して、Ambari UI から Livy 構成にアクセスできます。

`<yourclustername>` を、ポータルに表示されている HDInsight クラスターの名前に置き換えます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]