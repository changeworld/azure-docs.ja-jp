---
title: Azure 上の Custom Speech Service のメーターおよびクォータ | Microsoft Docs
description: Azure 上の Custom Speech Service のメーターとクォータに関する情報。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: d2225dec818c600febfad2f9ebc42594f6ac09ac
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372856"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Custom Speech Service のメーターとクォータ

クラウド ベースの Custom Speech Service では、音声からテキストへの文字起こし用の音声モデルをカスタマイズできます。

Custom Speech Service の使用を開始するには、[Custom Speech Service ポータル](https://cris.ai)に移動します。

最新の価格メーターについては、「[Cognitive Services の価格 - Custom Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)」ページに移動します。

## <a name="tiers-explained"></a>レベルの説明
テストおよびプロトタイプについてのみ、Free (F0) レベルの使用をお勧めします。 実稼働システムには、S2 レベルの使用をお勧めします。 S2 レベルを使用して、デプロイをシナリオに必要なスケール ユニット (SU) 数にスケーリングできます。

> [!NOTE]
> F0 レベルと S2 レベルの間で移行することは*できません*。
>

## <a name="meters-explained"></a>メーターの説明

### <a name="scale-out"></a>Scale Out
Scale Out は新しい価格設定モデルでリリースされた新しい機能です。 Scale Out を使用して、モデルで処理できる同時要求の数を制御できます。

**[Create Model Deployment]\(モデル デプロイの作成\)** ビューの SU メジャーを使用して、同時要求を設定することができます。 詳しくは、「[Create a custom speech-to-text endpoint](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md)」(カスタム Speech-to-Text エンドポイントの作成) をご覧ください。 モデルで消費されると予想されるトラフィック量に応じて、適切な数の SU を選択できます。 

> [!NOTE]
> 各スケール ユニットでは、5 つの同時要求が保証されます。 必要に応じて、1 つまたは複数の SU を購入することができます。 SU の数は増分 1 で増加するので、同時要求数は 増分 5 で増加することが保証されます。
>

### <a name="log-management"></a>ログの管理
追加料金で新しくデプロイしたモデルのオーディオ トレースをオフにすることもできます。 Custom Speech Service では、そのモデルからのオーディオ要求またはトランスクリプトはログに記録されません。

## <a name="next-steps"></a>次の手順
Custom Speech Service の使用方法について詳しくは、[Custom Speech Service ポータル](https://cris.ai)に移動してください。

* [はじめに](cognitive-services-custom-speech-get-started.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
* [用語集](cognitive-services-custom-speech-glossary.md)
 