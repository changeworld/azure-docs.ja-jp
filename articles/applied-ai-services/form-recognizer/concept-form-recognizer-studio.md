---
title: Form Recognizer Studio | プレビュー
titleSuffix: Azure Applied AI Services
description: '概念: Form Recognizer Studio (プレビュー) を使用したフォームとドキュメントの処理、データ抽出、分析'
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: sajagtap
ms.openlocfilehash: e476f6da88688cb055c741f0888001d864908bae
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812383"
---
# <a name="form-recognizer-studio--preview"></a>Form Recognizer Studio | プレビュー

>[!NOTE]
> Form Recognizer Studio は現在、パブリック プレビューの段階です。 一部の機能がサポートされなかったり、機能が制限されたりすることがあります。

[Form Recognizer Studio プレビュー](https://formrecognizer.appliedai.azure.com/)は、アプリケーションの Form Recognizer サービスの機能を視覚的に探索、理解、および統合するためのオンライン ツールです。 サンプル ドキュメントまたは独自のものを使用して、事前トレーニング済みのモデルの探索を始めるには、[Form Recognizer Studio のクイックスタート](quickstarts/try-v3-form-recognizer-studio.md)を使用してください。 [Python SDK プレビュー](quickstarts/try-v3-python-sdk.md)や他のクイックスタートを使用して、カスタム フォーム モデルを構築し、アプリケーションでモデルを参照するプロジェクトを作成します。

次の図は、請求書の事前構築済みモデルの機能の動きを示したものです。

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Form Recognizer の事前構築済みの例":::

## <a name="form-recognizer-studio-features"></a>Form Recognizer Studio の機能

Studio では、Form Recognizer サービスの次の機能を利用できます。

* **レイアウト**: Form Recognizer のレイアウト機能を使用すると、ドキュメント (PDF、TIFF) と画像 (JPG、PNG、BMP) から、テキスト、テーブル、選択マーク、および構造情報を抽出できます。 [Studio のレイアウト クイックスタート](quickstarts/try-v3-form-recognizer-studio.md#layout)から始めてください。 サンプル ドキュメントと独自のドキュメントを使用して探索します。 対話型の視覚化と JSON 出力を使用して、機能のしくみを理解します。 詳細については[レイアウトの概要](concept-layout.md)に関する記事を参照し、[レイアウトに関する Python SDK クイックスタート](quickstarts/try-v3-python-sdk.md#try-it-layout-model)で始めてください。

* **事前構築済みモデル**: Form Recognizer の事前に構築されたモデルを使用すると、独自のモデルをトレーニングして構築しなくても、インテリジェントなフォーム処理をアプリやフローに追加できます。 [Studio の事前構築済みモデル クイックスタート](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)から始めてください。 サンプル ドキュメントと独自のドキュメントを使用して探索します。 対話型の視覚化、抽出されたフィールドの一覧、JSON 出力を使用して、機能のしくみを理解します。 詳細については[モデルの概要](concept-model-overview.md)に関する記事を参照し、[事前構築済みの請求書に関する Python SDK クイックスタート](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-invoice-model)で始めてください。

* **カスタム モデル**: Form Recognizer のカスタム モデルを使用すると、データでトレーニングされ、フォームやドキュメントに合わせて調整されたモデルから、フィールドと値を抽出できます。 スタンドアロン カスタム モデルを作成するか、2 つ以上のカスタム モデルを結合して、複数のフォームの種類からデータを抽出する作成済みモデルを作成します。 [Studio のカスタム モデル クイックスタート](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics)から始めてください。  オンライン ウィザード、ラベル付けインターフェイス、トレーニング ステップ、視覚化を使用して、機能のしくみを理解します。 サンプル ドキュメントを使用してカスタム モデルをテストし、反復処理してモデルを改善します。 詳細については[カスタム モデルの概要](concept-custom.md)に関する記事を参照し、[Form Recognizer v3.0 プレビュー移行ガイド](v3-migration-guide.md)を使用して、新しいモデルとアプリケーションの統合を始めてください。

* **カスタム モデル: ラベル付け機能**: Form Recognizer のカスタム モデルの作成では、カスタム モデルをトレーニングする前に、抽出するフィールドを識別し、それらのフィールドでドキュメント内のコンテンツにラベルを付ける必要があります。 テキスト、選択マーク、表形式データ、その他のコンテンツの種類のラベル付けは、通常、トレーニング ワークフローを容易にするためのユーザー インターフェイスで支援されます。 たとえば、[テーブルとしてのラベル付け](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables)と[署名検出のラベル付け](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection)のクイックスタートを使用して、Form Recognizer Studio でのラベル付けエクスペリエンスを理解します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Form Recognizer Studio (プレビュー) の使用を開始する](https://formrecognizer.appliedai.azure.com)

