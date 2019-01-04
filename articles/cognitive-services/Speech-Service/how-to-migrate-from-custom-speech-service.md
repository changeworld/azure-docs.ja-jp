---
title: Custom Speech Service から Speech Services に移行する
titlesuffix: Azure Cognitive Services
description: Custom Speech Service は Speech Service の一部になっています。 Speech Service に切り替えると、最新の品質と機能の更新のベネフィットがあります。
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b5879fabba70308c33101699dae4443e6b1b7070
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084534"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Custom Speech Service から Speech Service に移行する

アプリケーションを Custom Speech Service から Speech Service に移行するには、この記事を参考にしてください。

Custom Speech Service は Speech Service の一部になっています。 Speech Service に切り替えると、最新の品質と機能の更新のベネフィットがあります。

## <a name="migration-for-new-customers"></a>新しいお客様の移行

Speech Service の価格モデルは時間ベースになっており、より単純です。  

1. お使いのアプリケーションが使用可能なリージョンごとに Azure リソースを作成します。 Azure リソース名は **Speech** です。 同じリージョン内では、以下のサービスについて、個別のリソースを作成する代わりに、1 つの Azure リソースを使用できます。

    * 音声テキスト変換
    * カスタム音声テキスト変換
    * テキスト読み上げ
    * 音声翻訳

2. [Speech SDK](speech-sdk.md) をダウンロードします｡

3. クイック スタート ガイドと SDK サンプルに従って、適切な API を使用します。 REST API を使用する場合は、正しいエンドポイントとリソース キーを使用する必要もあります。

4. Speech Service とその API を使用するようにクライアント アプリケーションを更新します。

> [!NOTE]
> * Language Understanding (LUIS) で音声を有効にした場合は、同じリージョン内の 1 つの LUIS リソースで、LUIS だけでなく、すべての音声サービスに対応できます。 詳細については、「[音声から意図を認識する](how-to-recognize-intents-from-speech-csharp.md)」を参照してください。
> * テキストからテキストへの翻訳は Speech Service の一部ではありません。 この機能には、専用の Azure リソース サブスクリプションが必要です。
 


## <a name="migration-for-existing-customers"></a>既存のお客様の移行

Speech Service ポータルで Speech Service に既存のリソース キーを移行します。 次の手順に従います。

> [!NOTE]
> リソース キーは、同じリージョン内でのみ移行できます。

1. [cris.ai](http://www.cris.ai) ポータルにサインインし、右上のメニューでサブスクリプションを選択します。

2. **[Migrate selected subscription]\(選択したサブスクリプションの移行\)** を選択します。

3. テキスト ボックスにサブスクリプション キーを入力し、**[Migrate]\(移行\)** を選択します。

## <a name="next-steps"></a>次の手順

* [Speech Service を無料で試す](get-started.md)
* [Speech to Text API](./speech-to-text.md) の概念を学習する

## <a name="see-also"></a>関連項目

* [Speech Service とは](overview.md)
* [Speech Service および Speech SDK のドキュメント](speech-sdk.md#get-the-sdk)
