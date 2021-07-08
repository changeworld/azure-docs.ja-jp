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
ms.openlocfilehash: df34d0ad7aa2c8249f013d430cedd72dd86eb6a2
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110451999"
---
# <a name="what-is-optical-character-recognition"></a>光学式文字認識とは

光学式文字認識 (OCR) を使用すると、道路標識や製品の写真などの画像や、送り状、請求書、財務報告書、記事などのドキュメントから、印刷または手書きのテキストを抽出できます。 Microsoft の OCR テクノロジでは、[複数の言語](./language-support.md)で印刷されたテキストの抽出がサポートされます。 [クイックスタート](./quickstarts-sdk/client-library.md)に従って始めてください。

![OCR のデモ](./Images/ocr-demo.gif)

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./quickstarts-sdk/client-library.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* [攻略ガイド](./Vision-API-How-to-Topics/call-read-api.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="read-api"></a>Read API 

Computer Vision の [Read API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) は、印刷されたテキスト (複数の言語)、手書きのテキスト (英語のみ)、数字、通貨記号を、画像や複数ページの PDF ドキュメントから抽出する、Azure の最新の OCR テクノロジです ([新機能について学習する](./whats-new.md))。 これは、テキストの多い画像や、混合言語を含む複数ページの PDF ドキュメントからテキストを抽出するように最適化されています。 同じ画像またはドキュメントで、印刷と手書き両方のテキストの検出がサポートされています。

![OCR で画像やドキュメントからテキストを抽出して構造化された出力に変換するしくみ](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>入力の要件

**Read** 呼び出しにより、画像とドキュメントが入力として取得されます。 これには次の要件があります。

* サポートされているファイル形式: JPEG、PNG、BMP、PDF、TIFF
* PDF ファイルと TIFF ファイルの場合は、最大 2,000 ページ (Free レベルの場合は最初の 2 ページのみ) が処理されます。
* ファイル サイズは 50 MB 未満 (Free レベルの場合は 6 MB)、寸法は 50 x 50 ピクセル以上 10,000 x 10,000 ピクセル以下にする必要があります。 

## <a name="supported-languages"></a>サポートされている言語
読み取り API では、印刷スタイルのテキストとして合計 73 言語がサポートされています。 [OCR でサポートされている言語](./language-support.md#optical-character-recognition-ocr)の完全な一覧を参照してください。 手書きスタイルの OCR は、英語でのみサポートされています。

## <a name="key-features"></a>主要な機能

Read API には次の機能があります。 

* 73 言語の印刷テキストを抽出
* 英語の手書きテキストを抽出
* テキスト行と単語に位置と信頼度スコアを追加
* 言語の識別が不要
* 混合言語、混合モード (印刷と手書き) のサポート
* 複数ページから成る大きなドキュメントからページやページ範囲を選択
* テキスト行に自然な読みの順序を使用
* テキスト行の手書き分類
* オンプレミス デプロイ用の Distroless Docker コンテナーとして利用可能

[OCR 機能の使用方法](./vision-api-how-to-topics/call-read-api.md)を参照してください。

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>クラウド API の使用またはオンプレミスでのデプロイ
Read 3.x クラウド API は、統合が簡単で、すぐに生産性を上げることができるため、ほとんどのお客様にとって推奨される選択肢です。 Azure と Computer Vision サービスがスケール、パフォーマンス、データ セキュリティ、コンプライアンスのニーズに対応する一方で、お客様は顧客のニーズを満たすことに集中できます。

オンプレミスでのデプロイの場合、[Read Docker コンテナー (プレビュー)](./computer-vision-how-to-install-containers.md) を使用すると、独自のローカル環境に新しい OCR 機能をデプロイできます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。

> [!WARNING]
> Computer Vision 2.0 RecognizeText 操作は非推奨になる予定であり、この記事で取り上げている新しい [Read API](#read-api) がその代わりになります。 既存顧客の皆様には、[読み取り操作をご利用いただくように](upgrade-api-versions.md)お願いします。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Computer Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

- [OCR (Read) REST API またはクライアント ライブラリのクイックスタート](./quickstarts-sdk/client-library.md)を参照して使用を開始します。
- [Read 3.2 REST API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) について学習します。
