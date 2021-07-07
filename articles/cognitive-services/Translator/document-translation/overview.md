---
title: ドキュメント変換とは
description: クラウドベースのバッチ ドキュメント翻訳サービスとプロセスの概要について説明します。
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 05/20/2021
ms.openlocfilehash: 7359be792d8a832f6dcddc2c4d3caaf000930e83
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110453564"
---
# <a name="what-is-document-translation"></a>ドキュメント変換とは

ドキュメント翻訳は、[Azure Translator](../translator-info-overview.md) サービスのクラウドベースの機能であり、REST API の Azure Cognitive Services ファミリの一部です。 Document Translation API は、ドキュメントの構造とデータ形式を維持しながら、90 の言語および方言との間でドキュメントを翻訳します。

このドキュメントには、次の種類の記事が含まれています。  

* [**クイックスタート**](get-started-with-document-translation.md)は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](create-sas-tokens.md)には、より具体的またはカスタマイズした方法で機能を使用するための手順が記載されています。  

## <a name="document-translation-key-features"></a>ドキュメント翻訳の主要機能

| 機能 | 説明 |
| ---------| -------------|
| **大きなファイルを翻訳する**| ドキュメント全体を非同期に翻訳します。|
|**多数のファイルを翻訳する**|複数のファイルを、90 の言語および方言との間で翻訳します。|
|**ソース ファイルの表示を保持する**| 元のレイアウトと形式を維持したままファイルを翻訳します。|
|**カスタム翻訳を適用する**| 汎用および[カスタム翻訳](../customization.md#custom-translator)モデルを使用してドキュメントを翻訳します。|
|**カスタム用語集を適用する**|カスタム用語集を使用してドキュメントを翻訳します。|
|**ドキュメントの言語を自動的に検出する**|ドキュメントの言語を決定を、ドキュメント翻訳サービスに委ねます。|
|**コンテンツで複数の言語が使用されているドキュメントを翻訳する**|自動検出機能を使用して、複数の言語のコンテンツが含まれるドキュメントをターゲット言語に変換します。|

> [!NOTE]
> 複数の言語のコンテンツが含まれるドキュメントを翻訳する場合、この機能では完全な文には 1 つの言語が使用されているものと想定されています。 複数の言語で文が構成されている場合、コンテンツが対象言語に翻訳されないことがあります。
> 
## <a name="how-to-get-started"></a>開始する方法

この攻略ガイドでは、Document Translator をすぐに使い始める方法について説明します。 開始するには、アクティブな [Azure アカウント](https://azure.microsoft.com/free/cognitive-services/)が必要です。  アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/free)ことができます。

> [!div class="nextstepaction"]
> [Get Started (概要)](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>サポートされるドキュメントの形式

ドキュメント翻訳では、次の種類のドキュメント ファイルがサポートされています。

| ファイルの種類| [ファイル拡張子]|説明|
|---|---|--|
|Adobe PDF|.pdf|Adobe Acrobat Portable Document Format|
|コンマ区切りの値 |.csv| スプレッドシート プログラムによって使用される、コンマ区切りの生データ ファイル。|
|HTML|.html、.htm|ハイパー テキスト マークアップ言語。|
|Localization Interchange File Format|.xlf 、.xliff| 並列ドキュメント形式、翻訳メモリ システムのエクスポート。 使用される言語はファイル内で定義されています。|
|Microsoft Excel|.xls、.xlsx|データ分析とドキュメント用のスプレッドシート ファイル。|
|Microsoft Outlook|.msg|Microsoft Outlook で作成または保存された電子メール メッセージ。|
|Microsoft PowerPoint|.ppt、.pptx| スライドショー形式でコンテンツを表示するために使用されるプレゼンテーション ファイル。|
|Microsoft Word|.doc、.docx| テキスト ドキュメント ファイル。|
|OpenDocument テキスト|.odt|オープンソースのテキスト ドキュメント ファイル。|
|OpenDocument プレゼンテーション|.odp|オープンソースのプレゼンテーション ファイル。|
|OpenDocument スプレッドシート|.ods|オープンソースのスプレッドシート ファイル。|
|リッチ テキスト形式|.rtf|書式設定を含むテキスト ドキュメント。|
|Tab Separated Values/TAB|.tsv/.tab| スプレッドシート プログラムによって使用される、タブ区切りの生データ ファイル。|
|テキスト|.txt| 書式設定のないテキスト ドキュメント。|

## <a name="supported-glossary-formats"></a>サポートされている用語集の形式

ドキュメント翻訳では、次の種類の用語集ファイルがサポートされています。

| ファイルの種類| [ファイル拡張子]|説明|
|---|---|--|
|Localization Interchange File Format|.xlf 、.xliff| 並列ドキュメント形式、翻訳メモリ システムのエクスポート。 使用される言語はファイル内で定義されています。|
|Tab Separated Values/TAB|.tsv/.tab| スプレッドシート プログラムによって使用される、タブ区切りの生データ ファイル。|

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ドキュメント翻訳を使ってみる](get-started-with-document-translation.md)
