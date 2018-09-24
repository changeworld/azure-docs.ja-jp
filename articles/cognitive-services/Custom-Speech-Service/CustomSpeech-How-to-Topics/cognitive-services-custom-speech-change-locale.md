---
title: Azure 上の Custom Speech Service でサポートされるロケールと言語 | Microsoft Docs
description: Cognitive Services の Custom Speech Service でサポートされる言語の概要。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 1f186681c7e46d2e47ed7eee55c8f61290c48fcb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987528"
---
# <a name="supported-locales-in-custom-speech-service"></a>Custom Speech Service でサポートされるロケール
Custom Speech Service では、次のロケールのモデルのカスタマイズが現在サポートされています。

| モデルの種類 | 言語のサポート |
|----|-----|
| 音響モデル | 英語 (米国) (en-us) |
| 言語モデル | 英語 (米国) (en-US)、中国語 (zh-CN) |

音響モデルのカスタマイズは英語 (米国) でのみサポートされており、中国語音響データのインポートはカスタマイズされた中国語モデルのオフライン テストの目的でサポートされています。

アクションを実行する前に、適切なロケールを選択する必要があります。 現在のロケールは、すべてのデータ、モデル、デプロイのページでのテーブルのタイトルで示されます。 ロケールを変更するには、テーブルのタイトルの下にある [Change Locale]\(ロケールの変更\) をクリックします。 これにより、ロケールの確認ページに移動します。 テーブルに戻るには [OK] をクリックします。

次の手順に従う必要があります
* [カスタム音響モデルを作成する方法](cognitive-services-custom-speech-create-acoustic-model.md)を学習して認識精度を向上させる
* [カスタム言語モデルを作成する方法](cognitive-services-custom-speech-create-language-model.md)を学習して認識率を向上させる
* [トランスクリプションのガイドライン](cognitive-services-custom-speech-transcription-guidelines.md)に従ってデータを準備する
