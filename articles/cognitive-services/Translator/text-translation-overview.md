---
title: Microsoft Azure Cognitive Services テキスト翻訳とは
titlesuffix: Azure Cognitive Services
description: Text Translation API をアプリケーション、Web サイト、ツール、またはその他のソリューションに統合して、多言語ユーザー エクスペリエンスを提供できます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/09/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Translator, テキスト翻訳, 機械翻訳, 翻訳サービス, カスタム翻訳ツール
ms.openlocfilehash: 68354706935d0dcfb29055a1eb5b0e273022d306
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787968"
---
# <a name="what-is-text-translation"></a>テキスト翻訳とは

 テキスト翻訳は、Translator サービスのクラウドベースの REST API 機能で、ニューラル機械翻訳テクノロジを使用して、[サポートされているすべての言語](language-support.md)でソースからターゲットへの迅速かつ正確なテキスト翻訳をリアルタイムで実現します。 この概要では、Text Translation REST API を使用して、アプリケーションとワークフローのためのインテリジェントなソリューションを構築する方法について説明します。

テキスト翻訳のドキュメントには、次の種類の記事が含まれています。

* [**クイック スタート**](quickstart-translator.md)。 サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](translator-how-to-signup.md)。 より具体的またはカスタマイズした方法でサービスにアクセスまたは使用するための手順が記載されています。
* [**関連記事**](reference/v3-0-reference.md)。 REST API ドキュメントとプログラミング言語ベースのコンテンツです。

## <a name="text-translation-features"></a>テキスト翻訳機能

 テキスト翻訳機能では、次のメソッドがサポートされています。

* [**Languages**](reference/v3-0-languages.md)。 **Translate**、**Transliterate**、および **辞書検索** 操作でサポートされる言語の一覧を返します。 この要求には認証は必要ではありません。次の GET 要求をコピーし、Postman またはお気に入りの API ツールまたはブラウザーに貼り付けます。

    ```http
    https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
    ```

* [**Translate**](reference/v3-0-translate.md#translate-to-multiple-languages)。 1 つの要求で、1 つのソース言語テキストを複数のターゲット言語テキストにレンダリングします。

* [**Transliterate**](reference/v3-0-transliterate.md)。 ソース言語の文字を、ターゲット言語の対応する文字に変換します。

* [**Detect**](reference/v3-0-detect.md)。 ソース コードの言語コードと、検出された言語がテキスト翻訳と表記変換でサポートされているかどうかを示すブール型変数を返します。

    > [!NOTE]
    > テキストの **Translate、Transliterate、Detect** 処理は、[単一の REST API 呼び出し](reference/v3-0-translate.md#translate-a-single-input-with-language-autodetection)で実行できます。

* [**辞書検索**](reference/v3-0-dictionary-lookup.md)。 ターゲット言語から、ソース用語に相当する単語を返します。
* [**辞書の例**](reference/v3-0-dictionary-examples.md) ソース用語とターゲット用語のペアの文法構造とコンテキストの例を返します。

## <a name="text-translation-deployment-options"></a>テキスト翻訳のデプロイ オプション

次のリソースを使用して、プロジェクトとアプリケーションにテキスト翻訳を追加します。

* Azure で利用可能な [**REST API**](reference/rest-api-guide.md) を介して、クラウドベースの Translator サービスにアクセスします。

* REST API [翻訳要求](containers/translator-container-supported-parameters.md)を [**テキスト翻訳 Docker コンテナー**](containers/translator-how-to-install-container.md)と一緒に使用します。

    > [!IMPORTANT]
    >
    > * Translator コンテナーは限定的なプレビューです。 これを使用するには、[**限定サービス用 Azure Cognitive Services アプリケーション**](https://aka.ms/csgate-translator)のオンライン要求フォームに入力して送信し、コンテナーへのアクセス権を取得する承認を得る必要があります。
    >
    > * [**Translator コンテナー イメージ**](https://hub.docker.com/_/microsoft-azure-cognitive-services-translator-text-translation)では、クラウド オファリングと比較して限定された機能がサポートされています。
    >

## <a name="get-started-with-text-translation"></a>テキスト翻訳の概要

開始する準備ができましたか?

* Azure portal で [**Translator リソースを作成**](translator-how-to-signup.md "Azure Portal にアクセスします。")します。

* [**アクセス キーと API エンドポイントを取得します**](translator-how-to-signup.md#authentication-keys-and-endpoint-url)。 認証には、エンドポイント URL と読み取り専用キーが必要です。

* [**クイック スタート**](quickstart-translator.md "REST と任意のプログラミング言語を介して Translator を使用する方法について説明します。")を確認し、次のプログラミング言語の使用例とコード サンプルを確認します。 
  * [**C#/.NET**](quickstart-translator.md?tabs=csharp)
  * [**Go**](quickstart-translator.md?tabs=go)
  * [**Java**](quickstart-translator.md?tabs=java)
  * [**JavaScript/Node.js**](quickstart-translator.md?tabs=nodejs)
  * [**Python**](quickstart-translator.md?tabs=python)

## <a name="next-steps"></a>次のステップ

Text Translation REST API について詳しく調べます。

> [!div class="nextstepaction"]
> [REST API リファレンスを参照してください](./reference/v3-0-reference.md)
