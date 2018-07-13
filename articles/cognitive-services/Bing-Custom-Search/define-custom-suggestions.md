---
title: 'Bing Custom Search: Custom Autosuggest の検索候補を定義する | Microsoft Docs'
description: カスタム検索候補を使用して Custom Autosuggest を構成する方法について説明します
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374693"
---
# <a name="configure-your-custom-autosuggest-experience"></a>カスタム自動提案エクスペリエンスを構成する
適切なレベルで Custom Search をサブスクライブしている場合 ([料金ページ](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)を参照してください)、Custom Search エクスペリエンスの検索候補をカスタマイズすることができます。 Custom Autosuggest では、ユーザーが指定した部分的クエリ文字列に基づいて、候補となるクエリの一覧が返されます。 Custom Autosuggest を使うと、検索エクスペリエンスに関連するカスタムの検索候補を提供できます。 カスタム検索候補のみを返すか、Bing の検索候補を含めるかも指定します。 Bing の検索候補を含める場合、カスタム検索候補は Bing の検索候補の前に表示されます。 Bing の候補は、Custom Search インスタンスのコンテキストに制限されます。

## <a name="configure-custom-autosuggest"></a>Custom Autosuggest を構成する
次の手順を使用して、Custom Search インスタンスのための Custom Autosuggest を構成します。

1.  [Custom Search](https://customsearch.ai) にサインインします。
2.  Custom Search のインスタンスをクリックします。 インスタンスを作成するには、[最初の Bing Custom Search インスタンスを作成する](quick-start.md)ことに関するページを参照してください。
3.  **[Autosuggest]** タブをクリックします。

## <a name="enable-bing-suggestions"></a>Bing の検索候補を有効にする
Bing の検索候補を有効にするには、**[Automatic Bing suggestions]**(Bing の自動検索候補) スライダーをオンの位置にします。 スライダーが青になります。

## <a name="add-suggestions"></a>検索候補を追加する
検索候補を追加するには、テキスト ボックスに入力します。 Enter キーを押すか、**+** アイコンをクリックします。 カスタムの検索候補は、どの言語にすることもでき、Bing の検索候補の前に表示されます。

## <a name="upload-suggestions"></a>検索候補をアップロードする
検索候補の一覧をファイルからアップロードできます。 各検索候補を別の行に配置します。 アップロードのアイコン をクリックし、ファイルを選択します。

## <a name="remove-suggestions"></a>検索候補を削除する
検索候補を削除するには、削除する検索候補の横にある削除アイコンをクリックします。

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Custom Autosuggest の構成変更が有効になるまで、最大で 24 時間かかる場合があります。

## <a name="next-steps"></a>次の手順

- [カスタム検索候補を取得する](./get-custom-suggestions.md)
- [カスタム インスタンスを検索する](./search-your-custom-view.md)
- [カスタムのホステッド UI を構成して使用する](./hosted-ui.md)