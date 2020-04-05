---
title: カスタム ニューラル音声の制限の概要
titleSuffix: Azure Cognitive Services
description: カスタム ニューラル音声のための制限プロセスの概要。
services: cognitive-services
author: shlo
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: shlo
ms.openlocfilehash: 73e6e4e69a245485ca473f048bbeafef21dbbf37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836787"
---
# <a name="custom-neural-voice-gating-overview"></a>カスタム ニューラル音声の制限の概要

カスタム ニューラル音声を開始するためのプロセスについて説明します。

## <a name="commitment-to-responsible-innovation"></a>責任あるイノベーションへのコミットメント

責任ある AI の設計への Microsoft のコミットメントの一環として、Microsoft はカスタム ニューラル音声を使用する際の指針となる一連の資料を作成しました。 ここに記載されているガイドラインと分析情報は、Microsoft の [AI における責任あるイノベーションの原則](https://www.microsoft.com/AI/our-approach-to-ai)に基づいています。

### <a name="guidance-for-deploying-custom-neural-voice"></a>カスタム ニューラル音声をデプロイするためのガイダンス

- [責任あるデプロイのためのガイドライン](concepts-guidelines-responsible-deployment-synthetic.md): Microsoft の調査に基づく主な推奨事項
- [ボイス タレント向けの開示](https://aka.ms/disclosure-voice-talent): 責任を持ってテクノロジを使用するために、そのテクノロジについて何を理解し、ボイス タレントに何を通知すべきか
- [設計の開示](concepts-disclosure-guidelines.md): 合成音声が使用されていることをユーザーが認識し、サービスを信頼できるようにエクスペリエンスを設計する方法

### <a name="why-custom-neural-voice-is-a-gated-technology"></a>カスタム ニューラル音声が制限付きテクノロジである理由

Microsoft の目的は、個人や社会の権利を保護し、人間とコンピューターの透過的な対話を促進し、有害なディープフェイクと紛らわしいコンテンツの拡散を阻止することです。 このため、Microsoft ではカスタム ニューラル音声の使用を制限しています。 お客様がこのテクノロジにアクセスできるのは、アプリケーションの審査を受け、当社の倫理原則に沿って使用することを確約した後に限られます。

### <a name="our-gating-process"></a>Microsoft による制限プロセス

カスタム ニューラル音声にアクセスするには、最初にオンラインの取り込みフォームに入力する必要があります。 アプリケーションは[こちら](https://aka.ms/custom-neural-intake-form)から開始します。

カスタム ニューラル音声サービスへのアクセスは、この制限プレビュー期間中に限られた数のお客様をサポートするための Microsoft の資格基準、審査プロセス、および可用性に基づき、Microsoft の独自の判断によって決定されます。

申請プロセスの一環として、音声フォントを作成する前に、必ずボイス タレントから明示的な書面による許可を得る必要があり、これには[ボイス タレント向けの開示](https://aka.ms/disclosure-voice-talent)の共有が含まれます。 また、音声フォントをデプロイする場合、実装によって、サービスで[合成音声が使用されていることがユーザーに開示され](concepts-disclosure-guidelines.md)、Microsoft 合成音声サービスを利用していることがサービス利用規約に記載され、サービスのユーザーが問題を報告し、Microsoft との間で詳細情報を共有できるようにするフィードバック チャネルがサポートされることに同意する必要があります。 使用条件の詳細については、[こちら](https://aka.ms/custom-neural-code-of-conduct)を参照してください。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [ボイス タレント向けの開示](https://aka.ms/disclosure-voice-talent)
* [合成音声テクノロジの責任あるデプロイのためのガイドライン](concepts-guidelines-responsible-deployment-synthetic.md)
* [開示する方法](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>次のステップ

* [合成音声テクノロジの責任あるデプロイのためのガイドライン](concepts-guidelines-responsible-deployment-synthetic.md)
