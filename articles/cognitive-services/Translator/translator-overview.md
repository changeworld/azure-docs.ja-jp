---
title: Microsoft Azure Cognitive Services Translator とは
titlesuffix: Azure Cognitive Services
description: Translator をアプリケーション、Web サイト、ツール、またはその他のソリューションに統合して、多言語ユーザー エクスペリエンスを提供できます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 11/16/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Translator, テキスト翻訳, 機械翻訳, 翻訳サービス, カスタム翻訳ツール
ms.openlocfilehash: 5c0d2553438f1765ff5f95d6264961728ee94675
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706568"
---
# <a name="what-is-translator"></a>Translator とは

:::image type="content" source="media/translator-icon.png" alt-text="Azure portal の Translator アイコン。":::

 Translator は、クラウドベースのニューラル機械翻訳サービスであり、REST API の [Azure Cognitive Services](../what-are-cognitive-services.md) ファミリに含まれます。  Translator は、どのオペレーティング システムでも使用でき、世界中の何千もの企業によって使用されている Microsoft の製品やサービスで、言語翻訳や言語に関連するその他の操作を行うのに使用できます。 この概要では、Translator を使用して、[サポートされているすべての言語](./language-support.md)でアプリケーションに対してインテリジェントな複数言語ソリューションを構築する方法について説明します。

Translator のドキュメントには、次の種類の記事が含まれています。

* [**クイック スタート**](quickstart-translator.md)。 サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](translator-how-to-signup.md)。 より具体的またはカスタマイズした方法でサービスにアクセスまたは使用するための手順が記載されています。
* [**関連記事**](reference/v3-0-reference.md)。 REST API ドキュメントとプログラミング言語ベースのコンテンツです。

## <a name="translator-features-and-development-options"></a>Translator の機能と開発オプション

Translator サービスでは、次の機能がサポートされています。 各機能の詳細を確認したり、API リファレンスを参照したりするには、次の表のリンクを使用してください。

| 機能 | 説明 | 開発オプション |
|----------|-------------|--------------------------|
| [**テキスト翻訳**](text-translation-overview.md) | サポートされているソースとターゲットの言語間でテキスト変換をリアルタイムで実行します。 | <ul><li>[**REST API**](reference/rest-api-guide.md) </li><li>[テキスト翻訳 Docker コンテナー](containers/translator-how-to-install-container.md) — 現在は限定的なプレビューです。</li></ul> |
| [**ドキュメント変換**](document-translation/overview.md) | 元のドキュメントの構造と形式を維持したまま、バッチ ファイルと複雑なファイルを変換します。 | <ul><li>[**REST API**](document-translation/reference/rest-api-guide.md)</li><li>[**クライアント ライブラリ SDK**](document-translation/client-sdks.md)</li></ul> |
| [**Custom Translator**](custom-translator/overview.md) | ドメインおよび業界固有の言語、用語、スタイルを翻訳するために、カスタマイズされたモデルを作成します。 | <ul><li>[**カスタム翻訳ツール ポータル**](https://portal.customtranslator.azure.ai/)</li></ul> |

## <a name="try-the-translator-service-for-free"></a>Translator サービスを無料で試す

まず、Microsoft アカウントが必要です。お持ちでない場合は、[**Microsoft アカウント ポータル**](https://account.microsoft.com/account)で無料サインアップできます。  **[Microsoft アカウントを作成]** を選択し、新しいアカウントを作成して確認する手順に従います。

次に Azure アカウントを作成する必要があります。[**Azure サインアップ ページ**](https://azure.microsoft.com/free/ai/)に移動し、 **[無料で始める]** ボタンを選択し、Microsoft aアカウント資格情報を使用して新しい Azure アカウントを作成します。

これで始める準備が整いました。 [**Translator サービスを作成して**](translator-how-to-signup.md "Azure Portal にアクセスします。")、[**アクセス キーと API エンドポイントを取得し**](translator-how-to-signup.md#authentication-keys-and-endpoint-url "認証には、エンドポイント URL と読み取り専用キーが必要です。")、[**クイックスタート**](quickstart-translator.md "REST 経由で Translator を使用する方法について説明します。")を試してください。

## <a name="next-steps"></a>次のステップ

* 次の機能の詳細をご覧ください。
  * [**テキスト翻訳**](text-translation-overview.md)
  * [**ドキュメント変換**](document-translation/overview.md)
  * [**Custom Translator**](custom-translator/overview.md)
* [**Translator の価格**](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)を確認します。
