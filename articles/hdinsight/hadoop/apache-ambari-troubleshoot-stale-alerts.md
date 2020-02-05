---
title: Azure HDInsight での Apache Ambari の古いアラート
description: HDInsight での Apache Ambari の古いアラートについて、考えられる理由と解決策を説明し、分析します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722667"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>シナリオ:Azure HDInsight での Apache Ambari の古いアラート

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari UI から、次の図のようなアラートが表示される場合があります。

![Apache Ambari の古いアラートの例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

多くのリソースの正常性を監視するために、Ambari エージェントによって、正常性チェックが継続的に実行されます。 各アラートは、事前に定義された間隔で実行するように構成されています。 各アラートの実行後、Ambari エージェントから Ambari サーバーに状態が報告されます。 この時点で、アラートが適切なタイミングで実行されなかったことが Ambari サーバーによって検出された場合は、"Ambari サーバー アラート" がトリガーされます。 定義された間隔で正常性チェックが実行されない理由はさまざまです。

* ホストの使用率が高い (高い CPU 使用率) 場合、Ambari エージェントによるアラートの適時実行に十分なシステム リソースが取得できなかった可能性があります。

* クラスターでは、大きな負荷がかかっている最中に多くのジョブやサービスが実行されます。

* クラスター内で多数のコンポーネントをホストするホストは少ないため、多くのアラートを実行する必要があります。 コンポーネントの数が多い場合は、アラート ジョブがスケジュールされた間隔で実行されない可能性があります。

## <a name="resolution"></a>解決策

### <a name="increase-alert-interval-time"></a>アラート間隔の時間を増やす

クラスターの応答時間と負荷に基づいて、個々のアラート間隔の値を増やすことができます。

1. Apache Ambari UI で、 **[Alerts]\(アラート\)** タブを選択します。
1. 目的のアラート定義名を選択します。
1. 定義から、 **[Edit]\(編集\)** を選択します。
1. 必要に応じて **[Check Interval]\(チェック間隔\)** の値を変更し、 **[Save]\(保存\)** を選択します。

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Ambari サーバー アラートのアラート間隔の時間を増やす

1. Apache Ambari UI で、 **[Alerts]\(アラート\)** タブを選択します。
1. **[Groups]\(グループ\)** ドロップダウン リストで、 **[AMBARI Default]\(AMBARI の既定値\)** を選択します。
1. **[Ambari Server Alerts]\(Ambari サーバー アラート\)** というアラートを選択します。
1. 定義から、 **[Edit]\(編集\)** を選択します。
1. 必要に応じて、 **[Check Interval]\(チェック間隔\)** 値を変更します。
1. 必要に応じて **[Interval Multiplier]\(間隔の乗数\)** 値を変更し、 **[Save]\(保存\)** を選択します。

### <a name="disable-and-enable-the-alert"></a>アラートを無効にしてから有効にする

アラートを無効にしてから再度有効にして、古いアラートを破棄することができます。

1. Apache Ambari UI で、 **[Alerts]\(アラート\)** タブを選択します。
1. 目的のアラート定義名を選択します。
1. 定義から、右端にある **[Enabled]\(有効\)** を選択します。
1. **[Confirmation]\(確認\)** ポップアップで、 **[Confirm Disable]\(無効化の確認\)** を選択します。
1. ページに表示されているすべてのアラート "インスタンス" がクリアされるまで数秒待ちます。
1. 定義から、右端にある **[Disabled]\(無効\)** を選択します。
1. **[Confirmation]\(確認\)** ポップアップで、 **[Confirm Enable]\(有効化の確認\)** を選択します。

### <a name="increase-alert-grace-time"></a>アラートの猶予時間を増やす

Ambari エージェントには、構成されたアラートがスケジュールどおりに実行されなかったことを報告する前に、猶予時間が適用されます。 アラートがスケジュールされた時刻に実行されなくても、アラートの猶予時間内にトリガーされた場合は、古いアラートは発生しません。

既定の `alert_grace_period` 値は 5 秒です。 この `alert_grace_period` の設定は `/etc/ambari-agent/conf/ambari-agent.ini` 内で構成できます。 古いアラートが一定の間隔で発生するホストについては、値を 10 に増やします。 その後、Ambari エージェントを再起動します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
