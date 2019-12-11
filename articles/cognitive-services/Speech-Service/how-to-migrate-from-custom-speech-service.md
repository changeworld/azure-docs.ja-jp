---
title: Custom Speech Service から音声サービスに移行する
titleSuffix: Azure Cognitive Services
description: Custom Speech Service は音声サービスの一部になっています。 音声サービスに切り替えると、最新の品質と機能の更新のベネフィットがあります。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805928"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Custom Speech Service から音声サービスに移行する

アプリケーションを Custom Speech Service から音声サービスに移行するには、この記事を参考にしてください。

Custom Speech Service は音声サービスの一部になっています。 音声サービスに切り替えると、最新の品質と機能の更新のベネフィットがあります。

## <a name="migration-for-new-customers"></a>新しいお客様の移行

音声サービスの価格モデルは時間ベースになっており、より単純です。  

1. お使いのアプリケーションが使用可能なリージョンごとに Azure リソースを作成します。 Azure リソース名は **Speech** です。 同じリージョン内では、以下のサービスについて、個別のリソースを作成する代わりに、1 つの Azure リソースを使用できます。

    * 音声テキスト変換
    * カスタム音声テキスト変換
    * テキスト読み上げ
    * 音声翻訳

2. [Speech SDK](speech-sdk.md) をダウンロードします｡

3. クイック スタート ガイドと SDK サンプルに従って、適切な API を使用します。 REST API を使用する場合は、正しいエンドポイントとリソース キーを使用する必要もあります。

4. Speech Service とその API を使用するようにクライアント アプリケーションを更新します｡

## <a name="migration-for-existing-customers"></a>既存のお客様の移行

音声サービス ポータルで音声サービスに既存のリソース キーを移行します。 次の手順に従います。

> [!NOTE]
> リソース キーは、同じリージョン内でのみ移行できます。

1. [cris.ai](https://cris.ai/Home/CustomSpeech) ポータルにサインインし、右上のメニューでサブスクリプションを選択します。

2. **[Migrate selected subscription]\(選択したサブスクリプションの移行\)** を選択します。

3. テキスト ボックスにサブスクリプション キーを入力し、 **[Migrate]\(移行\)** を選択します。

## <a name="next-steps"></a>次の手順

* [音声サービスを無料で試す](get-started.md)
* [Speech to Text API](./speech-to-text.md) の概念を学習する

## <a name="see-also"></a>関連項目

* [Speech Service とは](overview.md)
* [音声サービスと Speech SDK のドキュメント](speech-sdk.md#get-the-sdk)
