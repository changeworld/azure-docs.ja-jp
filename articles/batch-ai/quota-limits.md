---
title: Azure Batch AI 用のサービスのクォータと制限 | Microsoft Docs
description: 既定の Azure Batch AI のクォータ、制限、および制約と、クォータの引き上げを要求する方法について説明します
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 16032ec5ba1e613462f92b86281ce93153b70923
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409719"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Batch AI サービスのクォータと制限

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

他の Azure サービスと同様に、Batch AI サービスに関連付けられている特定のリソースにも制限があります。 Batch AI では、これらの制限は、サービスがサービスが[使用可能な](https://azure.microsoft.com/global-infrastructure/services/)各リージョンのサブスクリプション レベルで適用される既定のクォータです。 ここでは、これらの既定の設定と、クォータの引き上げを要求する方法について説明します。

Batch AI リソースの設計やスケールアップを行う際は、これらのクォータに留意してください。 たとえば、クラスターのノード数がターゲットとして指定した数に満たない場合は、サブスクリプションの Batch AI コアの制限に達している可能性があります。

Batch AI で実稼働ワークロードを実行する予定がある場合は、1 つまたは複数のクォータについて既定値から増やすことが必要になる場合があります。

> [!NOTE]
> クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。
> 
> 

## <a name="resource-quotas"></a>リソース クォータ

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>その他の制限

以下は厳密な制限で、一度達すると超えることはできません。

| **リソース** | **上限** |
| --- | --- |
| リソース グループあたりの最大のワークスペース | 800 |
| 最大のクラスター サイズ | 100 ノード |
| ノードごとの最大 GPU MPI 処理 | 1-4 |
| ノードごとの最大の GPU ワークスペース | 1-4 |
| ジョブの最長有効期間 | 7 日間<sup>1</sup> |
| ノードごとの最大パラメーター サーバー | 1 |

<sup>1</sup>最長有効期間とは、ジョブの実行を始め、そして完了する時間です。 完了したタスクは、無期限に保持されます。最長有効期間内に完了しなかったタスクのデータにはアクセスできません。

## <a name="view-batch-ai-quotas"></a>Batch AI クォータの確認

[Azure portal][portal]で現在の Batch AI サブスクリプション クォータを表示します。

1. 左側のウィンドウで、 **すべてのサービス**を選択します。 **Batch AI**を検索し、 クリックしてサービスを開くきます。
2. Batch AI メニューの**使用量 + クォータ**をクリックします。
3. クォータ制限を表示するサブスクリプションを選択します。

## <a name="increase-a-batch-ai-cores-quota"></a>Batch AI のコア クォータを増やす

次の手順を実行し、[Azure Portal][portal] を使用して、Batch AI サブスクリプションに対するクォータの引き上げを要求します。 

1. 左側のウィンドウで、 **すべてのサービス**を選択します。 **Batch AI**を検索し、 クリックしてサービスを開くきます。
2. Batch AI メニューの**新しいサポート要求**をクリックします。
3. **[基本]** で次のようにします。
   
    a. **[問題の種類]** > **[クォータ]**
   
    b. **サブスクリプション** > サブスクリプションを選択します。
   
    c. **クォータの種類** > **Batch AI**
   
    d. **サポートプラン** > サポートプランを選択します。

    **[次へ]** をクリックします。
4. **[問題]** で次のようにします。
   
    a. [ビジネスへの影響][support_sev]に従って **[重要度]** を選択します。
   
    b. **クォータの詳細**で、場所、クォータの種類、およびリソースの種類を指定します。 要求する新しい制限値を指定します。 **保存して続行** をクリックします。

    c. オプション - 増加の理由に関する詳細情報を含むファイルをアップロードします。
   
    **[次へ]** をクリックします。
5. **[連絡先情報]** で次のようにします。
   
    a. **希望連絡方法**を選択します。
   
    b. 必要な連絡先情報を確認および入力します。
   
    **[作成]** をクリックしてサポート要求を送信します。

サポート要求を送信した後は、Azure サポートからの連絡を待ちます。 要求を完了するには最大で 2 営業日かかります。


## <a name="next-steps"></a>次の手順

クォータ制限に慣れたら、次の記事を確認してBatch AI の使用を開始します。

> [!div class="nextstepaction"]
> [Batch AI のクイック スタート チュートリアル](quickstart-tensorflow-training-cli.md)
> [Batch AI recipes](https://github.com/Azure/BatchAI/tree/master/recipes)
> [Batch AI リソースの詳細](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: http://aka.ms/supportseverity