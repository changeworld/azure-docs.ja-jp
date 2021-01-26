---
title: Azure HDInsight で Apache Ambari UI にダウンしたホストとサービスが表示される
description: Azure HDInsight でダウンしたホストとサービスが表示されるときの Apache Ambari UI の問題のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 9c9676fa1457be014098b8190986c617262e628d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286493"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>シナリオ:Azure HDInsight で Apache Ambari UI にダウンしたホストとサービスが表示される

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari UI にアクセスできますが、ほとんどのすべてのサービスがダウンしていると UI に表示され、すべてのホストがハートビート喪失を示しています。

## <a name="cause"></a>原因

ほとんどのシナリオで、これはアクティブなヘッドノードで実行されていない Ambari サーバーの問題です。 どのヘッドノードがアクティブなヘッドノードであるかを確認し、ambari-server が正しいヘッドノードで実行されていることを確認します。 ambari-server を手動で開始するのではなく、フェールオーバー コントローラー サービスの責任下で ambari-server を正しいヘッドノードで開始してください。 アクティブなヘッドノードを再起動して、フェールオーバーを強制します。

この問題はネットワークの問題によって発生する可能性もあります。 各クラスター ノードから、ping `headnodehost` を実行できるかどうか確認します。 次のように、どのクラスター ノードも `headnodehost` に接続できないまれな状況があります。

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>解像度

通常、アクティブなヘッドノードを再起動すると、この問題が軽減されます。 されない場合は、HDInsight サポート チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]