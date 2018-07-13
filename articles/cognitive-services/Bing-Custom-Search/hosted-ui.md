---
title: 'Bing Custom Search: サイト検索 | Microsoft Docs'
description: ホステッド UI を構成する方法について説明します
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374733"
---
# <a name="configure-your-hosted-ui-experience"></a>ホステッド UI エクスペリエンスを構成する
カスタム検索のインスタンスを構成したら、Custom Search API を呼び出して検索結果を取得し、それらをアプリに表示することができます。 または、アプリが Web アプリの場合は、Custom Search で提供されるホステッド UI を使用できます。   

## <a name="configure-custom-hosted-ui"></a>カスタム ホステッド UI を構成する
次の手順を使用して、Web アプリに含めるホステッド UI を構成します。
1.  Custom Search の[ポータル](https://customsearch.ai)にサインインします。
2.  Custom Search のインスタンスをクリックします。 インスタンスを作成するには、[最初の Bing Custom Search インスタンスを作成する](quick-start.md)ことに関するページを参照してください。
3.  **[Hosted UI]**(ホステッド UI) タブをクリックします。
4.  レイアウトを選択します。
    - [Search bar and results](検索バーと結果) (既定値)&mdash;: 検索ボックスと検索結果を表示します
    - [Results only](結果のみ)&mdash;: 検索ボックスは表示せず、結果のみを表示します
    - [Pop over](ポップオーバー)&mdash;: 検索ボックスは表示せず、結果のみを、スライドするオーバーレイ内に表示します
    
   > [!IMPORTANT]
   > **[Results only]** (結果のみ) のレイアウトを選択する場合は customConfig クエリ パラメーターを含めるようにしてください。[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)に関するページを参照してください。

5.  **[追加の構成]** で、実際のアプリにとって適切な値を指定します。 これらの設定は省略可能です。 適用または削除の影響を確認するには、右側のプレビュー ウィンドウを参照します。  使用できる構成は次のとおりです。
    - Web 検索の構成:
        - [Web results enabled](Web の結果を有効化)&mdash;: Web 検索の結果が返されるかどうかを指定します
        - [Enable autosuggest](自動提案を有効にする)&mdash;: カスタム自動提案が有効かどうかを指定します
        - [Web results per page](Web 結果数/ページ)&mdash;: 一度に表示する Web 検索結果の数
        - [Image caption](イメージのキャプション)&mdash;: イメージを検索結果と共に表示するかどうかを指定します
        - [Highlight words] (語を強調表示する)&mdash;: 検索用語を太字にして結果を表示するかどうかを指定します
    - イメージ検索の構成:
        - [Image results enabled](イメージの結果を有効化)&mdash;: イメージ検索の結果が返されるかどうかを指定します
    - その他の構成:
        - [ページ タイトル]&mdash;: ページのタイトル領域に表示されるテキスト
        - [Toolbar theme](ツールバーのテーマ)&mdash;: ページのタイトル領域の背景色を指定します
        - [Search box text placeholder](検索ボックスのテキスト プレース ホルダー)&mdash;: 入力前に、検索ボックスにテキストが表示されます
        - [Title link url](タイトル リンク URL)&mdash;: タイトル リンクのターゲット
        - [Logo url](ロゴ URL)&mdash;: タイトルの横に表示されるイメージ 
        - [Favicon url](Favicon URL)&mdash;: ブラウザーのタイトル バーに表示されるアイコン

   > [!IMPORTANT]
   > 少なくとも 1 つのイメージ検索または Web 検索が有効になっている必要があります。

6.  検索サブスクリプション キーを入力するか、ドロップダウン リストから 1 つ選択します。 ドロップダウン リストには、使っているアカウントの Azure サブスクリプションからのキーが表示されます。 [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)についてのページを参照してください。
7.  自動提案を有効にした場合は、自動提案のサブスクリプション キーを入力するか、ドロップダウン リストから 1 つ選択します。 ドロップダウン リストには、使っているアカウントの Azure サブスクリプションからのキーが表示されます。 カスタム自動提案を利用するには、特定のサブスクリプション レベルが必要です。[価格のページ](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)を参照してください。

> [!NOTE]
> ホステッド UI のカスタム構成に変更を加えると、右のウィンドウには、加えた変更が見てわかるように表示されます。 表示される検索結果は、インスタンスの実際の結果ではありません

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>カスタム UI を使用する
ホステッド UI を使用するには、次のいずれかを実行します。 

- Web ページにスクリプトを含めます
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- 指定された URL `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID` を使用します

  > [!IMPORTANT]
  > このページには、プライバシーに関する声明や、その他の通知および条項を表示できません。 使用状況に適合するかどうかはさまざまです。  

カスタム構成 ID を含めて、詳細については、**[Production]**(運用) タブの下にある **[エンドポイント]** に移動してください。

## <a name="next-steps"></a>次の手順
- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)