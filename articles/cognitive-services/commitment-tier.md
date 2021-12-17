---
title: コミットメント レベル価格で Azure Cognitive Services リソースを作成する
description: 従量課金制の料金とは異なるコミットメント レベル価格にサインアップする方法について説明します。
author: aahill
ms.author: aahi
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/04/2021
ms.openlocfilehash: 94bd9d22876f264ffd438d579c48aa037ed87c3f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486623"
---
# <a name="quickstart-purchase-commitment-tier-pricing"></a>クイックスタート: コミットメント レベル価格で購入する

Cognitive Services ではコミットメント レベル価格が提供されており、それぞれ従量課金制の価格モデルと比べて割引料金が提供されています。 コミットメント レベル価格では、次の Cognitive Services 機能を固定料金で使用できるようになり、これによってワークロードのニーズに基づく合計コストが予測可能になります。

* 音声テキスト変換 (標準)
* テキスト読み上げ (ニューラル)
* テキスト翻訳 (標準)
* Language Understanding 標準 (テキスト要求)
* Azure Cognitive Service for Language
    * 感情分析
    * キー フレーズ抽出
    * 言語検出
* Computer Vision - 読み取り

コミットメント レベル価格は、次の Applied AI Services でも使用できます。
* Form Recognizer - カスタム/請求書

詳細については、[Azure Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)に関するページを参照してください。

## <a name="request-approval-to-purchase-a-commitment-plan"></a>コミットメント プランを購入するための承認を要求する

コミットメント プランを購入する前に、[オンライン申請を送信する](https://aka.ms/csgatecommitment)必要があります。 申請が承認された場合は、新規および既存の両方の Azure リソースについて、Azure portal でコミットメント レベルを購入できます。 

* このフォームでは、Azure サブスクリプション ID に関連付けられている会社のメール アドレスを使用する必要があります。

* 申請の状態に関する最新情報については、`csgate@microsoft.com` からのメール (受信トレイと迷惑フォルダーの両方) を確認してください。

承認されたら、コミットメント プランを使用するための新しいリソースを作成するか、既存のリソースを更新します。 

## <a name="create-a-new-resource"></a>新しいリソースを作成

> [!NOTE]
> コミットメント プランを購入して使用するには、リソースに Standard 価格レベルが必要です。 Free レベルのリソースについては、コミットメント プランの購入 (またはオプションの表示) ができません。

1. [Azure portal](https://portal.azure.com/) にサインインし、上の一覧にある該当するいずれかの Cognitive Services または Applied AI サービスに対して **[新しいリソースを作成する]** を選択します。 

2. リソースを作成するための該当する情報を入力します。 必ず Standard 価格レベルを選択してください。

    :::image type="content" source="media/commitment-tier/create-resource.png" alt-text="Azure portal でのリソースの作成を示すスクリーンショット。" lightbox="media/commitment-tier/create-resource.png":::

3. リソースが作成されると、従量課金制からコミットメント プランに価格を変更できるようになります。  

## <a name="purchase-a-commitment-plan-by-updating-your-azure-resource"></a>Azure リソースを更新してコミットメント プランを購入する

1. 承認された Azure サブスクリプションを使用して [Azure portal](https://portal.azure.com/) にサインインします。 
2. 上記の該当するいずれかの機能で、 **[Commitment tier pricing]\(コミットメント レベル価格\)** を選択します。

    > [!NOTE]
    > 次の場合にのみ、コミットメント プランを購入するオプションが表示されます。
    > * リソースで Standard 価格レベルが使用されている。
    > * コミットメント レベル価格での購入が承認されている。 
 
1. **[変更]** を選択して、ホストされている API とコンテナーの使用に関して利用可能なコミットメントを表示します。 

    :::image type="content" source="media/commitment-tier/commitment-tier-pricing.png" alt-text="Azure portal のコミットメント レベル価格に関するページを示すスクリーンショット。" lightbox="media/commitment-tier/commitment-tier-pricing.png":::

4. 表示されるウィンドウで、 **[レベル]** と **[自動更新]** の両方のオプションを選択します。

    * **[コミットメント レベル]** - 機能のコミットメントレベル。 コミットメント レベルは **[購入]** をクリックした時点ですぐに有効になり、コミットメント金額は日割り計算で課金されます。
    
    * **[自動更新]** - 次回の請求サイクルから、現在のコミットメント プランを更新、変更、またはキャンセルする方法を選択します。 自動更新を選択した場合、 **[自動更新の日付]** は、次回の請求サイクルに対して課金される (ローカル タイムゾーンの) 日付になります。 この日付は、カレンダー月の開始日と一致します。
    

    > [!CAUTION]
    > **[購入]** をクリックすると、選択したレベルに対して課金されます。 購入すると、コミットメント プランは払い戻しできません。
    > 
    > コミットメント プランは月単位で課金されます。ただし、購入の最初の月は、その月の残りの日数に基づいて日割り計算されます (コストとクォータ)。 翌月以降は、月の最初の日に料金が発生します。

    :::image type="content" source="media/commitment-tier/enable-commitment-plan.png" alt-text="Azure portal のコミットメント レベル価格と更新の詳細を示すスクリーンショット。" lightbox="media/commitment-tier/enable-commitment-plan-large.png":::


## <a name="overage-pricing"></a>超過分の価格

指定されたクォータを超えるリソースを使用した場合、コミットメント レベルに規定されている超過分に従い、追加の使用量に対して課金されます。

## <a name="purchase-a-different-commitment-plan"></a>別のコミットメント プランを購入する 

コミットメント プランのコミットメント期間は、カレンダー月の 1 か月です。 コミットメント プランは、既定の従量課金制の価格モデルの利用中にいつでも購入できます。 プランを購入すると、月の残りの期間は日割り料金が課金されます。 コミットメント期間中は、現在の月のコミットメント プランを変更できません。 ただし、カレンダー月の翌月について別のコミットメント プランを選択できます。 翌月分の請求は、翌月の最初の日に行われます。

提供されているものよりも大きなコミットメント プランが必要な場合は、`csgate@microsoft.com` にお問い合わせください。

## <a name="end-a-commitment-plan"></a>コミットメント プランを終了する

コミットメント プランの購入を継続しない場合、リソースの自動更新を **[自動更新しない]** に設定することができます。 コミットメント プランは、表示されたコミットメントの終了日に期限切れになります。 この日以降、コミットメント プランの料金は課金されません。 引き続き Azure リソースを使用して API を呼び出すことができ、従量課金制の料金で課金されます。 各月の最終日の午前 0 時 (UTC) までコミットメント プランを終了することができ、その場合次の月に対しては課金されません。 

## <a name="see-also"></a>関連項目

* [Azure Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)。
* [コミットメント レベル価格で購入するための申請を送信する](https://aka.ms/csgatecommitment)
