---
title: キーワード認識に関する推奨事項とガイドライン - Speech サービス
titleSuffix: Azure Cognitive Services
description: キーワード認識を使用する場合の推奨事項とガイドラインの概要です。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: a6b7a9c072951a7010403c9e54dff844eb910d95
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490672"
---
# <a name="recommendations-and-guidelines-for-keyword-recognition"></a>キーワード認識に関する推奨事項とガイドライン

この記事では、キーワードを選択してその精度特性を最適化する方法と、キーワード確認を使用してユーザー エクスペリエンスを設計する方法について説明します。 

## <a name="choosing-an-effective-keyword"></a>有効なキーワードの選択

有効なキーワードを作成することは、製品において正確で一貫した応答が行われることを保証するうえで不可欠です。 キーワードを選択するときには、以下のガイドラインを考慮に入れます。

> [!NOTE]
> 次の例では、英語が使用されていますが、カスタム キーワードでサポートされているすべての言語にこのガイドラインが適用されます。 サポートされているすべての言語の一覧については、[言語のサポート](language-support.md#custom-keyword-and-keyword-verification)に関するページを参照してください。

- 2 秒以内に読み上げることができる必要があります。
- 4 音節から 7 音節の単語が最適です。 たとえば、"Hey, Computer" は適切なキーワードです。 "Hey" だけではよくありません。
- キーワードは、エンドユーザーのネイティブ言語に固有の一般的な発音規則に従う必要があります。
- 一般的な発音規則に従った一意の単語や造語にすると、擬陽性が減る可能性があります。 たとえば "computerama" が適切なキーワードになる可能性があります。
- 一般的な単語は選択しないでください。 たとえば、"eat" や "go" は、普通の会話の中で頻繁に出てくる単語です。 これらの単語に関する他人受入率は、製品に対して求める割合よりも高くなる可能性があります。
- 別の発音をする可能性があるキーワードは使用しないようにします。 ユーザーは、音声で製品を有効にするための "正しい" 発音を知っている必要があります。 たとえば "509" は、"five zero nine"、"five oh nine"、または "five hundred and nine" と発音できます。 "R.E.I."  は、"R E I" または "Ray" として発音できます。 "Live" は、"/Līv/" または "/liv/" と発音できます。
- 特殊な文字、記号、または数字は使用しないでください。 たとえば、"Go#" や "20 + cats" は、問題を引き起こすキーワードになる可能性があります。 ただし、"go sharp" や "twenty plus cats" は機能するでしょう。 それでもブランディングにおいて記号を使用して、正しい発音が一般に認知されるように、マーケティングやドキュメントを利用することができます。


## <a name="user-experience-recommendations-with-keyword-verification"></a>キーワード検証に関するユーザー エクスペリエンスの推奨事項

[キーワード検証](keyword-recognition-overview.md#keyword-verification)が使用されるマルチステージのキーワード認識シナリオでは、アプリケーションでエンドユーザーにキーワード検出を通知させるタイミングを選択できます。 視覚または可聴インジケーターを表示する際には、キーワード検証サービスからの応答を頼りにすることをお勧めします。

![精度を最適化する際のユーザー エクスペリエンス ガイドライン。](media/custom-keyword/kw-verification-ux-accuracy.png)

これにより、誤って受け入れてしまうユーザー認識の影響を最小限に抑えるという精度の観点からは最適なエクスペリエンスを確保できますが、余分の待機時間が発生することになります。

待機時間を最適化する必要のあるアプリケーションでは、デバイス上のキーワード認識に基づいて、エンドユーザーに対して軽い控えめなインジケーターを提供できます。 たとえば、LED パターンを点灯させたり、アイコンを点滅させたりします。 キーワード検証でキーワードが受け入れられた場合は、インジケーターを存続させ、応答でキーワードが拒否された場合は、無視することができます。

![待機時間を最適化する際のユーザー エクスペリエンス ガイドライン。](media/custom-keyword/kw-verification-ux-latency.png)

## <a name="next-steps"></a>次の手順

* [Speech SDK を取得する。](speech-sdk.md)
* [音声アシスタントの詳細情報を確認する。](voice-assistants.md)
