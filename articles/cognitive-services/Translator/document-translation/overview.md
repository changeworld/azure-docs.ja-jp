---
title: ドキュメント変換とは
description: クラウドベースのバッチ ドキュメント翻訳サービスとプロセスの概要について説明します。
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: fc75bfc3b79cd6aed51a44fc47787532a9d61e8b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713016"
---
# <a name="what-is-document-translation-preview"></a>ドキュメント翻訳とは (プレビュー)

ドキュメント翻訳は、[Azure Translator](../translator-info-overview.md) サービスのクラウドベースの機能であり、REST API の Azure Cognitive Services ファミリの一部です。 Document Translation API は、ドキュメントの構造とデータ形式を維持しながら、90 の言語および方言との間でドキュメントを翻訳します。

## <a name="document-translation-key-features"></a>ドキュメント翻訳の主要機能

| 機能 | 説明 |
| ---------| -------------|
| **大きなファイルを翻訳する**| ドキュメント全体を非同期に翻訳します。|
|**多数のファイルを翻訳する**|複数のファイルを、90 の言語および方言との間で翻訳します。|
|**ソース ファイルの表示を保持する**| 元のレイアウトと形式を維持したままファイルを翻訳します。|
|**カスタム翻訳を適用する**| 汎用および[カスタム翻訳](../customization.md#custom-translator)モデルを使用してドキュメントを翻訳します。|
|**カスタム用語集を適用する**|カスタム用語集を使用してドキュメントを翻訳します。|

## <a name="how-to-get-started"></a>開始する方法

この攻略ガイドでは、Document Translator をすぐに使い始める方法について説明します。 開始するには、アクティブな [Azure アカウント](https://azure.microsoft.com/free/cognitive-services/)が必要です。  アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/free)ことができます。

> [!div class="nextstepaction"]
> [Get Started (概要)](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>サポートされるドキュメントの形式

ドキュメント翻訳では、次の種類のドキュメント ファイルがサポートされています。

| ファイルの種類| [ファイル拡張子]|説明|
|---|---|--|
|Adobe PDF|.pdf|Adobe Acrobat Portable Document Format|
|HTML|.html|ハイパー テキスト マークアップ言語。|
|Localization Interchange File Format|.xlf 、.xliff| 並列ドキュメント形式、翻訳メモリ システムのエクスポート。 使用される言語はファイル内で定義されています。|
|Microsoft Excel|.xlsx|データ分析とドキュメント用のスプレッドシート ファイル。|
|Microsoft Outlook|.msg|Microsoft Outlook で作成または保存された電子メール メッセージ。|
|Microsoft PowerPoint|.pptx| スライドショー形式でコンテンツを表示するために使用されるプレゼンテーション ファイル。|
|Microsoft Word|.docx| テキスト ドキュメント ファイル。|
|Tab Separated Values/TAB|.tsv/.tab| スプレッドシート プログラムによって使用される、タブ区切りの生データ ファイル。|
|テキスト|.txt| 書式設定のないテキスト ドキュメント。|
|Translation Memory eXchange|.tmx|コンピューター支援翻訳 (CAT) とローカライズ アプリケーションによって作成された翻訳メモリ (TM) データを交換するために使用される、オープンな XML 規格。|

## <a name="supported-glossary-formats"></a>サポートされている用語集の形式

ドキュメント翻訳では、次の種類の用語集ファイルがサポートされています。

| ファイルの種類| [ファイル拡張子]|説明|
|---|---|--|
|Localization Interchange File Format|.xlf 、.xliff| 並列ドキュメント形式、翻訳メモリ システムのエクスポート。 使用される言語はファイル内で定義されています。|
|Tab Separated Values/TAB|.tsv/.tab| スプレッドシート プログラムによって使用される、タブ区切りの生データ ファイル。|

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ドキュメント翻訳を使ってみる](get-started-with-document-translation.md)
>
>
