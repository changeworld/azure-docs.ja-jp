---
title: Azure HDInsight で Apache Ambari UI にダウンしたホストとサービスが表示される
description: Azure HDInsight でダウンしたホストとサービスが表示されるときの Apache Ambari UI の問題のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: b8cd9c4e00eb3dc454098dbd127622342bc690f7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291459"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で Apache Ambari UI にダウンしたホストとサービスが表示される

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

## <a name="resolution"></a>解決方法

通常、アクティブなヘッドノードを再起動すると、この問題が軽減されます。 されない場合は、HDInsight サポート チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]