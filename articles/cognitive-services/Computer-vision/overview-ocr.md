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
ms.date: 06/21/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 02654c3f196b6c3bc199e636b3601777ed372a99
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992536"
---
# <a name="what-is-optical-character-recognition"></a>光学式文字認識とは

光学式文字認識 (OCR) を使用すると、道路標識や製品の写真などの画像や、送り状、請求書、財務報告書、記事などのドキュメントから、印刷または手書きのテキストを抽出できます。 Microsoft の OCR テクノロジでは、<bpt id="p1">[</bpt>複数の言語<ept id="p1">](./language-support.md)</ept>で印刷されたテキストの抽出がサポートされます。 <bpt id="p1">[</bpt>クイックスタート<ept id="p1">](./quickstarts-sdk/client-library.md)</ept>に従って始めてください。

![OCR のデモ](./Images/ocr-demo.gif)

このドキュメントには、次のような記事が記載されています。
* <bpt id="p1">[</bpt>クイックスタート<ept id="p1">](./quickstarts-sdk/client-library.md)</ept>は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* <bpt id="p1">[</bpt>攻略ガイド<ept id="p1">](./Vision-API-How-to-Topics/call-read-api.md)</ept>には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="read-api"></a>Read API 

Computer Vision の [Read API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) は、印刷されたテキスト (複数の言語)、手書きのテキスト (複数の言語)、数字、通貨記号を、画像や複数ページの PDF ドキュメントから抽出する、Azure の最新 OCR テクノロジです ([新機能について学習する](./whats-new.md))。 これは、テキストの多い画像や、混合言語を含む複数ページの PDF ドキュメントからテキストを抽出するように最適化されています。 同じ画像またはドキュメントで、印刷と手書き両方のテキストの検出がサポートされています。

![OCR で画像やドキュメントからテキストを抽出して構造化された出力に変換するしくみ](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>入力の要件

<bpt id="p1">**</bpt>Read<ept id="p1">**</ept> 呼び出しにより、画像とドキュメントが入力として取得されます。 これには次の要件があります。

* サポートされているファイル形式: JPEG、PNG、BMP、PDF、TIFF
* PDF ファイルと TIFF ファイルの場合は、最大 2,000 ページ (Free レベルの場合は最初の 2 ページのみ) が処理されます。
* ファイル サイズは 50 MB 未満 (Free レベルの場合は 6 MB)、寸法は 50 x 50 ピクセル以上 10,000 x 10,000 ピクセル以下にする必要があります。 

## <a name="supported-languages"></a>サポートされている言語
Read API は、プレビューの言語と機能を含め、印刷テキストでは 122 言語、手書きテキストでは 7 言語をサポートしています。

印刷用テキストの OCR には、英語、フランス語、ドイツ語、イタリア語、ポルトガル語、スペイン語、中国語、日本語、韓国語、ロシア語 (プレビュー) に加え、最新のプレビュー更新プログラムではラテン語とキリル文字もサポートしています。

手書きテキストの OCR には、英語に加えて、フランス語、ドイツ語、イタリア語、ポルトガル語、スペイン語、中国語のプレビューがサポートされています。

プレビューの言語と機能を使用するための[モデル バージョンの指定方法](./Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional)に関するページを参照してください。 <bpt id="p1">[</bpt>OCR でサポートされている言語<ept id="p1">](./language-support.md#optical-character-recognition-ocr)</ept>の完全な一覧を参照してください。 プレビュー モデルには、現在の GA バージョンに対する拡張機能が含まれています。

## <a name="key-features"></a>主要な機能

Read API には次の機能があります。

* 122 言語の印刷テキストの抽出
* 7 つの言語での手書きテキストの抽出
* テキスト行と単語に位置と信頼度スコアを追加
* 言語の識別が不要
* 混合言語、混合モード (印刷と手書き) のサポート
* 複数ページから成る大きなドキュメントからページやページ範囲を選択
* テキスト行出力の自然な読み取り順オプション (ラテン語のみ)
* テキスト行の手書き分類 (ラテン語のみ)
* オンプレミス デプロイ用の Distroless Docker コンテナーとして利用可能

<bpt id="p1">[</bpt>OCR 機能の使用方法<ept id="p1">](./vision-api-how-to-topics/call-read-api.md)</ept>を参照してください。

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>クラウド API の使用またはオンプレミスでのデプロイ
Read 3.x クラウド API は、統合が簡単で、すぐに生産性を上げることができるため、ほとんどのお客様にとって推奨される選択肢です。 Azure と Computer Vision サービスがスケール、パフォーマンス、データ セキュリティ、コンプライアンスのニーズに対応する一方で、お客様は顧客のニーズを満たすことに集中できます。

オンプレミスでのデプロイの場合、<bpt id="p1">[</bpt>Read Docker コンテナー (プレビュー)<ept id="p1">](./computer-vision-how-to-install-containers.md)</ept> を使用すると、独自のローカル環境に新しい OCR 機能をデプロイできます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。

> [!WARNING]
> Computer Vision 2.0 RecognizeText 操作は非推奨になる予定であり、この記事で取り上げている新しい <bpt id="p1">[</bpt>Read API<ept id="p1">](#read-api)</ept> がその代わりになります。 既存顧客の皆様には、<bpt id="p1">[</bpt>読み取り操作をご利用いただくように<ept id="p1">](upgrade-api-versions.md)</ept>お願いします。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Computer Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの <bpt id="p1">[</bpt>Cognitive Services のページ<ept id="p1">](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)</ept>を参照してください。

## <a name="next-steps"></a>次のステップ

- [OCR (Read) REST API またはクライアント ライブラリのクイックスタート](./quickstarts-sdk/client-library.md)を参照して使用を開始します。
- [Read 3.2 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) について学習します。
