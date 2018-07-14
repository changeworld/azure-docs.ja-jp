---
title: Azure 上の Custom Speech Service からエンドポイントの価格レベルを移行する | Microsoft Docs
description: 階層 S0、S1 から、デプロイを Cognitive Services の S2 の Custom Speech Service エンドポイントに移行する方法を説明します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 6d92459deb3464cd97c215cbf9a8320628b6da80
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374133"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>新しい価格モデルへ展開を移行する
Custom Speech Service は、2017 年 7 月の時点で、[新しい価格モデル](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)を提供します。 新しいモデルは*わかりやすく*、*より簡単にコストを計算でき*、スケーリングの観点から*より柔軟*です。 スケーリングについて、マイクロソフトは、スケール ユニットの概念を導入しました。 各スケール ユニットは、5 つの同時要求を処理できます。 同時要求のスケーリングは、古いモデルの S0 層では同時要求が 5 つに、S1 層では 12 の同時要求に設定されていました。 お客様のユース ケース要件の柔軟性を高めるために、これらの制限を撤廃しました。

古い S0 層または S1 層を実行しているお客様には、既存の展開を新しい S2 層に移行することをお勧めします。 新しい S2 層は、S0 と S1 の両方の階層に対応できます。 次の図で使用できるオプションをご確認ください。

!["価格レベルの選択" ページ](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

マイクロソフトは、半自動的に移行を処理します。 まず、お客様が新しい価格レベルを選択して、移行を開始します。 次に、マイクロソフトがお客様の展開を自動的に移行します。

古い階層からのスケール単位へのマッピングを、次の表に示します。

| レベル | 同時要求数 (旧モデル) | 移行 | 同時要求数 |
|----- | ----- | ---- | ---- |
| S0 |  5   |   1 スケール ユニットで => **S2** |   5 |
| S1 |  12  |   3 スケール ユニットで => **S2** |  15 |

新しい階層に移行するには、次の操作を行います。

## <a name="step-1-check-your-existing-deployment"></a>手順 1: 既存の展開を確認する
[Custom Speech Service ポータル](http://cris.ai)に移動し、既存の展開を確認します。 この例では、2 つの展開があります。 1 つの展開は S0 層で実行され、その他の展開は S1 層で実行されています。 展開は、次の表の**展開オプション**列に表示されています。

![展開ページ](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>手順 2: Azure Portal で新しい価格レベルを選択する
1. 新しいブラウザー タブで、[Azure Portal](http://ms.portal.azure.com/) にサインインします。 

2. **[Cognitive Services]** ウィンドウの **[サブスクリプション]** 一覧で、カスタム音声サブスクリプションを選択します。 

3. サブスクリプションのウィンドウで **[価格レベル]** を選択します。

    !["価格レベル" リンク](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. **[Choose your pricing tier]\(価格レベルの選択\)** ページで、**[S2 Standard]** を選択します。 この価格レベルは、より単純でより柔軟な新しい価格レベルです。

5. **[Select]\(選択\)** ボタンをクリックします。

    ![[Choose your pricing tier]\(価格レベルの選択\) ページ](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>手順 3: Custom Speech Service ポータルで移行の状態を確認する
Custom Speech Service ポータルに戻り、展開を確認します。 (ブラウザー ウィンドウがまだ開いている場合は、画面を更新します。) 

関連する展開の状態が *[処理中]* に変わります。 移行は、**[展開オプション]** 列をチェックすることでも確認できます。 ここでは、スケール ユニットとログ記録に関する情報を確認できるようになりました。 スケール ユニットには、以前の価格レベルが反映されています。 ログ記録も表のようにオンにしてください。

![[展開オプション] 列](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> 移行中に問題が発生した場合は、マイクロソフトにお問い合わせください。
>

## <a name="next-steps"></a>次の手順
その他のチュートリアルについては、以下を参照してください。
* [カスタム音響モデルを作成する](cognitive-services-custom-speech-create-acoustic-model.md)
* [カスタム言語モデルを作成する](cognitive-services-custom-speech-create-language-model.md)
* [カスタム Speech-to-Text エンドポイントの作成](cognitive-services-custom-speech-create-endpoint.md)
