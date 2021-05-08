---
title: 光学式文字認識とは
titleSuffix: Azure Cognitive Services
description: 光学式文字認識 (OCR) サービスは、画像内に表示されているテキストを抽出し、構造化された文字列として返します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 41b3552a633c9cebce1138fa042dbd154eee0cb5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314118"
---
# <a name="what-is-optical-character-recognition"></a>光学式文字認識とは

光学式文字認識 (OCR) サービスを使用すると、道路標識や製品の写真などの画像や、送り状、請求書、財務報告書、記事などのドキュメントから、印刷または手書きのテキストを抽出できます。 ディープ ラーニング ベースのモデルを使用して、さまざまなサーフェスや背景のテキストを操作します。

OCR API では、[複数の言語](./language-support.md)で印刷されたテキストの抽出がサポートされます。 [クイックスタート](./quickstarts-sdk/client-library.md)に従って始めてください。

![OCR のデモ](./Images/ocr-demo.gif)

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./quickstarts-sdk/client-library.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* [攻略ガイド](./Vision-API-How-to-Topics/call-read-api.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>サポートされている言語
OCR API では、印刷スタイルのテキストとして合計 73 言語がサポートされています。 [OCR でサポートされている言語](./language-support.md#optical-character-recognition-ocr)の完全な一覧を参照してください。 手書きスタイルの OCR は、英語でのみサポートされています。

## <a name="input-requirements"></a>入力の要件

**Read** 呼び出しにより、画像とドキュメントが入力として取得されます。 これには次の要件があります。

* サポートされているファイル形式: JPEG、PNG、BMP、PDF、TIFF
* PDF ファイルと TIFF ファイルの場合は、最大 2,000 ページ (Free レベルの場合は最初の 2 ページのみ) が処理されます。
* ファイル サイズは 50 MB 未満 (Free レベルの場合は 4 MB)、寸法は 50 x 50 ピクセル以上 10,000 x 10,000 ピクセル以下にする必要があります。 
* PDF の寸法は、17 x 17 インチ以下である必要があります (リーガル サイズまたは A3 サイズ以下の用紙に対応します)。

## <a name="read-api"></a>Read API 

Computer Vision の [Read API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) は、印刷されたテキスト (複数の言語)、手書きのテキスト (英語のみ)、数字、通貨記号を、画像や複数ページの PDF ドキュメントから抽出する、Azure の最新の OCR テクノロジです ([新機能について学習する](./whats-new.md))。 これは、テキストの多い画像や、混合言語を含む複数ページの PDF ドキュメントからテキストを抽出するように最適化されています。 同じ画像またはドキュメントで、印刷と手書き両方のテキストの検出がサポートされています。

![OCR で画像やドキュメントからテキストを抽出して構造化された出力に変換するしくみ](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>クラウド API の使用またはオンプレミスでのデプロイ
Read 3.x クラウド API は、統合が簡単で、すぐに生産性を上げることができるため、ほとんどのお客様にとって推奨される選択肢です。 Azure と Computer Vision サービスがスケール、パフォーマンス、データ セキュリティ、コンプライアンスのニーズに対応する一方で、お客様は顧客のニーズを満たすことに集中できます。

オンプレミスでのデプロイの場合、[Read Docker コンテナー (プレビュー)](./computer-vision-how-to-install-containers.md) を使用すると、独自のローカル環境に新しい OCR 機能をデプロイできます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。

## <a name="ocr-api"></a>OCR API

従来の [OCR API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) では、古い認識モデルが使用されており、画像のみがサポートされ、同期的に実行されて、検出されたテキストは直ちに返されます。 サポートされている言語の一覧については、[サポートされている言語](./language-support.md#optical-character-recognition-ocr)の OCR の列を参照してください。

> [!WARNING]
> Computer Vision 2.0 RecognizeText 操作は非推奨になる予定であり、この記事で取り上げている新しい [Read API](#read-api) がその代わりになります。 既存顧客の皆様には、[読み取り操作をご利用いただくように](upgrade-api-versions.md)お願いします。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Computer Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

- [OCR (Read) REST API またはクライアント ライブラリのクイックスタート](./quickstarts-sdk/client-library.md)を参照して使用を開始します。
- [Read 3.2 REST API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) について学習します。
