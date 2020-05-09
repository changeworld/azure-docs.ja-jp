---
title: Azure HDInsight でのリソース作成エラーをトラブルシューティングする
description: 容量に関する一般的な問題のエラーと軽減策については、この記事で説明しています。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 527d2d8cb8086ed6b5e87417e2bc80dd52aa6e63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188414"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Azure HDInsight でのリソース作成エラーをトラブルシューティングする

この記事では、Azure HDInsight を操作する際の問題のトラブルシューティング手順と想定できる解決策について説明します。

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>エラー:The deployment would exceed the quota of '800' (デプロイが '800' のクォータを超えています)

Azure では、リソース グループあたり 800 デプロイのクォータ制限があります。 リソース グループ、サブスクリプション、アカウント、または他のスコープごとに異なるクォータが適用されています。 たとえば、ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 許可されているよりも多くのコアを使用して仮想マシンをデプロイしようとすると、クォータを超過したことを示すエラー メッセージが表示されます。

この問題を解決するには、Azure portal、CLI、または PowerShell を使用して、不要になったデプロイを削除します。

詳細については、「[リソース クォータのエラーを解決する](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)」を参照してください。

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>エラー:The maximum node exceeded the available cores in this region (最大ノードがこのリージョンで使用できるコア数を超えました)

ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 許可されているよりも多くのコアを使用してリソースをデプロイしようとすると、クォータを超過したことを示すエラー メッセージが表示されます。

クォータの引き上げを依頼するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にアクセスし、 **[ヘルプとサポート]** を選択します。

1. **[新しいサポート リクエスト]** を選択します。

1. **[新しいサポート リクエスト]** ページの **[基本]** タブに次の情報を指定します。

   * **問題の種類:** **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
   * **サブスクリプション:** 変更するサブスクリプションを選択します。
   * **クォータの種類**: **[HDInsight]** を選択します。

詳細については、[サポート チケットを作成してコア数を増やす](hdinsight-capacity-planning.md#quotas)方法に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
