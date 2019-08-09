---
title: Apache Hive での結合により Azure HDInsight で OutOfMemory エラーが発生する
description: OutOfMemory エラー "GC オーバーヘッド制限超過エラー" の処理
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 45b19bdfc12313974252c81c2597645b1ceb2018
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668659"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>シナリオ: Apache Hive での結合により Azure HDInsight で OutOfMemory エラーが発生する

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive 結合の既定の動作では、Map/Reduce 手順を行うことなく結合を実行できるように、テーブルの内容全体がメモリに読み込まれます。 Hive テーブルが大きすぎてメモリに収まりきらない場合、クエリは失敗する可能性があります。

## <a name="cause"></a>原因

十分なサイズの Hive で結合を実行すると、次のエラーが発生します。

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>解決策

次の Hive 構成値を設定することにより、結合時に Hive で (Map/Reduce 手順を行う代わりに) テーブルがメモリに読み込まれないようにします。

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>次の手順

この値を設定しても問題が解決しない場合は、次のいずれかにアクセスしてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を確認してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
