---
title: Azure 上の Custom Speech Service のサブスクリプション キーを取得する | Microsoft Docs
description: Cognitive Services の Custom Speech Service の呼び出しのためにサブスクリプション キーを取得する方法を説明します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d3969114323f5675c5e14ab36990b124e84ead37
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427569"
---
# <a name="obtain-subscription-keys"></a>サブスクリプション キーを取得する
Azure Custom Speech Service を開始するには、最初にユーザー アカウントを Azure サブスクリプションにリンクする必要があります。 無料レベルと有料レベルのサブスクリプションがあります。 レベルについて詳しくは、[価格に関するページ](https://www.microsoft.com/cognitive-services/en-us/pricing)をご覧ください。

## <a name="get-a-subscription-key"></a>サブスクリプション キーを取得する
1. 次の 2 つの方法のいずれかで Azure Portal からサブスクリプション キーを取得できます。

    * [Azure Portal](https://ms.portal.azure.com) に移動し、_Cognitive Services_ を検索してから、**[Cognitive Services APIs]** を選択することで、新しい Cognitive Services API を追加します。

      ![Cognitive Services の検索](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * あるいは、直接 [[Cognitive Services APIs]](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices) に移動します。

        ![Cognitive Services APIs](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. 次の必須フィールドに入力します。

      a. **[アカウント名]**。 任意の名前を使用します。 リソース一覧で自分の Cognitive Services サブスクリプションを見つけられるように、この名前を覚えておいてください。

      b. **サブスクリプション**。 ご利用の Azure サブスクリプションから 1 つ選択します。

      c. **[API の種類]**。 **[Custom Speech Service (プレビュー)]** を選択します。

      d. **場所**。 現在は **[米国西部]** です。

      e. **[価格レベル]**。 適切なレベルを選択します。 **F0** は無料レベルです。 許可されるクォータについては、[価格に関するページ](https://www.microsoft.com/cognitive-services/en-us/pricing)をご覧ください。

      ![Cognitive Services アカウントの作成](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. ダッシュボードで表示を確認するか、リソース一覧で指定したアカウント名のサービスを確認します。 選択すると、サービスの概要が表示されます。 左側の一覧の **[リソース管理]** の下で **[キー]** を選択します。 **[キー 1]** をコピーします。

      このサブスクリプション キーは次の手順で必要です。

      ![キー 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > [概要] ページの **[サブスクリプション ID]** はコピーしないでください。 次の手順ではサブスクリプション キーが必要です。
      >

      ![[概要] の [サブスクリプション ID]](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. サブスクリプション キーを入力するには、右上のリボンで、自分のユーザー アカウントを選択します。 ドロップダウン メニューで、**[サブスクリプション]** を選択します。

      ![[サブスクリプション] メニュー項目](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    サブスクリプションの表が表示されますが、最初に開いたときは空です。

    ![サブスクリプション表](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. **[新規追加]** を選択します。 サブスクリプションの名前とサブスクリプション キーを入力します。 これは自分のサブスクリプションの **[キー 1]** (主キー) または **[キー 2]** (セカンダリ キー) のいずれかです。

      ![サブスクリプション キー名](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

データのアップロード、モデルのトレーニング、またはデプロイの実行を行うには、Custom Speech Service アクティビティを Azure サブスクリプションにリンクする必要があります。 無料レベルのサブスクリプションまたは有料レベルのサブスクリプションを使用できます。 詳細については、 [価格に関するページ](https://www.microsoft.com/cognitive-services/en-us/pricing)を参照してください。

## <a name="get-a-subscription-id"></a>サブスクリプション ID を取得する
サブスクリプション ID を取得したら、Azure Portal に移動します。 *Cognitive Services* と *Custom Speech Service* を検索し、手順に従います。

> [!NOTE]
> このプロセスの後半ではサブスクリプション キーが必要です。
>

## <a name="next-steps"></a>次の手順
* [カスタム音響モデル](cognitive-services-custom-speech-create-acoustic-model.md)の作成を開始します。
* [カスタム言語モデル](cognitive-services-custom-speech-create-language-model.md)の作成を開始します。
