---
title: Azure HDInsight での Apache Spark アクティビティのための IllegalArgumentException
description: Azure Data Factory 用の Azure HDInsight での Apache Spark アクティビティのための IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 54e07a840aac1e754db68d9a14403750757f4bcf
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620607"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>シナリオ: Azure HDInsight での Apache Spark アクティビティのための IllegalArgumentException

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Azure Data Factory パイプラインで Spark アクティビティを実行しようとすると、次の例外が発生します。

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

アプリケーション jar ファイルが Spark クラスターの既定のストレージまたはプライマリ ストレージに配置されていない場合、Spark ジョブは失敗します。

これは、このバグで追跡されている Spark オープンソース フレームワークに関する既知の問題です。[fs.defaultFS とアプリケーション jar の URL が異なる場合に Spark のジョブが失敗する](https://issues.apache.org/jira/browse/SPARK-22587)。

この問題は Spark 2.3.0 で解決されました。

## <a name="resolution"></a>解決策

アプリケーション jar が、HDInsight クラスターの既定のストレージまたはプライマリ ストレージに格納されていることを確認します。 Azure Data Factory 場合は、ADF のリンクされたサービスが、HDInsight の既定のコンテナー (セカンダリ コンテナーではなく) を指していることを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を確認してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
