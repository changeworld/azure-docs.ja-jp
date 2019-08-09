---
title: ノードを Azure HDInsight クラスターに追加できない
description: ノードを Azure HDInsight クラスターに追加できない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 6c2e2c7395fcc45fe74c50beb3624eabb1d395c9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828014"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>シナリオ: ノードを Azure HDInsight クラスターに追加できない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

ノードを Azure HDInsight クラスターに追加できません。

## <a name="cause"></a>原因

さまざまな理由が考えられます。

## <a name="resolution"></a>解決策

[クラスター サイズ](../hdinsight-scaling-best-practices.md)機能を使用して、クラスターに必要な追加のコア数を計算します。 これは、新しい worker ノードのコアの合計数に基づきます。 その後、次の手順を 1 つ以上試します。

* クラスターの場所に使用可能なコアがあるかどうかを確認します。

* 他の場所の使用可能なコアの数を確認します。 使用可能なコアが十分にある別の場所でクラスターを再作成することを検討します。

* 特定の場所のコア クォータを引き上げるには、HDInsight コア クォータの引き上げに関するサポート チケットを提出してください。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上させます。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
