---
title: サイトの検索、ホステッド UI Bing Custom Search を使用する
titlesuffix: Azure Cognitive Services
description: Bing Custom Search ホステッド UI を構成する方法について説明します。
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 77845cb60dac707326acdb08b0198f8725a36f62
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813976"
---
# <a name="configure-your-hosted-ui-experience"></a>ホステッド UI エクスペリエンスを構成する

カスタム検索のインスタンスを構成したら、Custom Search API を呼び出して検索結果を取得し、それらをアプリに表示することができます。 または、アプリが Web アプリの場合は、Custom Search で提供されるホステッド UI を使用できます。   

## <a name="configure-custom-hosted-ui"></a>カスタム ホステッド UI を構成する

Web アプリ用にホステッド UI を構成するには、次の手順のようにします。

1. Custom Search の[ポータル](https://customsearch.ai)にサインインします。  
  
2. Custom Search のインスタンスをクリックします。 インスタンスを作成するには、[最初の Bing Custom Search インスタンスを作成する](quick-start.md)ことに関するページを参照してください。  

3. **[Hosted UI]**(ホステッド UI) タブをクリックします。  
  
4. レイアウトを選択します。
  
  - [Search bar and results]\(検索バーと結果\) (既定値) &mdash; このレイアウトは、検索ボックスと検索結果を含む従来の検索ページです。
  - [Results only]\(結果のみ\) &mdash; このレイアウトでは、検索結果のみが表示されます。 検索ボックスは表示されません。 JavaScript スニペットまたは HTML エンドポイント リンクの要求 URL にクエリ パラメーター (&q=\<クエリ文字列>) を追加することで、検索クエリを提供する必要があります。
  - [Pop-over]\(ポップオーバー\) &mdash; このレイアウトでは、検索ボックスが提供され、検索結果がスライディング オーバーレイで表示されます。
      
5. 配色テーマを選択します。 可能なテーマは次のとおりです。 
  
  - クラシック
  - ダーク
  - スカイライン ブルー

  各テーマをクリックし、Web アプリに最適なテーマを確認します。 より適切に Web アプリと統合するために配色テーマを微調整する必要がある場合は、**[テーマのカスタマイズ]** をクリックします。 すべての配色構成がすべてのレイアウト テーマに適用されるわけではありません。 色を変更するには、対応するテキスト ボックスに色の RGB 16 進数 (たとえば、#366eb8) を入力します。 または、色のボタンをクリックし、適している網掛けをクリックします。 
  
  色を変更した後、右側のプレビュー例で変更の影響を確認します。 常に **[既定値にリセット]** をクリックすることで、選択したテーマの既定の色に戻すことができます。

  > [!NOTE]
  > テーマの色を選択するときは、アクセシビリティを考慮してください。

5. **[追加の構成]** で、実際のアプリにとって適切な値を指定します。 これらの設定は省略可能です。 適用または削除の影響を確認するには、右側のプレビュー ウィンドウを参照します。 使用できる構成は次のとおりです。  
  
  - Web 検索の構成:
    - [Web results enabled]\(Web の結果を有効化\) &mdash; Web 検索を有効にするかどうかを決定します (ページの上部に [Web] タブが表示されます)。
    - [Enable autosuggest]\(自動提案を有効にする\) &mdash; カスタム自動提案を有効にするかどうかを指定します (追加コストについては[価格](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)に関するページをご覧ください)。
    - [Web results per page]\(Web 結果数/ページ\) &mdash; 一度に表示する Web 検索結果の数です (上限は 1 ページあたり 50 件です)。
    - [Image caption]\(イメージのキャプション\) &mdash; イメージを検索結果と共に表示するかどうかを指定します。
  
    **[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。  
  
    - [Highlight words]\(語を強調表示する\) &mdash; 検索用語を太字にして結果を表示するかどうかを指定します。 
    - [Link target]\(リンク先\) &mdash; ユーザーが検索結果をクリックしたときに、新しいブラウザー タブ (空白) または同じブラウザー タブ (自己) のどちらで Web ページを開くかを決定します。 

  - イメージ検索の構成:
    - [Image results enabled]\(画像の結果を有効化\) &mdash; 画像検索を有効にするかどうかを決定します (ページの上部に [Images]\(画像\) タブが表示されます)。   
    - [Image results per page]\(画像結果数/ページ\) &mdash; 一度に表示する画像検索結果の数です (上限は 1 ページあたり 150 件です)。  
  
    **[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。  
  
    - [Enable filters]\(フィルターを有効にする\) &mdash; Bing が返す画像のフィルターに使用できるフィルターを追加します。 たとえば、ユーザーはアニメーション GIF のみに結果をフィルター処理できます。

  - 動画検索の構成:
    - [Video results enabled]\(動画の結果を有効化\) &mdash; 動画検索を有効にするかどうかを決定します (ページの上部に [Videos]\(動画\) タブが表示されます)。  
    - [Video results per page]\(動画結果数/ページ\) &mdash; 一度に表示する動画検索結果の数です (上限は 1 ページあたり 150 件です)。
  
    **[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。  
  
    - [Enable filters]\(フィルターを有効にする\) &mdash; Bing が返す動画のフィルターに使用できるフィルターを追加します。 たとえば、ユーザーは、結果をフィルター処理して、特定の解像度の動画や、過去 24 時間以内に検出された動画を検索できます。

  - その他の構成:
    - [Page title]\(ページ タイトル\) &mdash; 検索結果ページのタイトル領域に表示されるテキストです (ポップオーバー レイアウトの場合は表示されません)。
    - [Toolbar theme]\(ツール バーのテーマ\) &mdash; 検索結果ページのタイトル領域の背景色を指定します。  
  
    **[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。  
  
    - [Search box text placeholder]\(検索ボックスのテキスト プレース ホルダー\) &mdash; 入力前に、検索ボックスにテキストが表示されます。
    - [Title link url]\(タイトル リンク URL\) &mdash; タイトル リンクのターゲットです。
    - [Logo url]\(ロゴ URL\) &mdash; タイトルの横に表示される画像です。 
    - [Favicon url]\(Favicon URL\) &mdash; ブラウザーのタイトル バーに表示されるアイコンです。  

    次の構成は、HTML エンドポイントを通してホステッド UI を使用する場合にのみ適用されます (JavaScript のスニペットを使用する場合は適用されません)。
    
    - Page title (ページ タイトル)
    - Toolbar theme (ツール バーのテーマ)
    - Title link URL (タイトル リンク URL)
    - Logo URL (ロゴ URL)
    - Faviicon URL  
  
6. 検索サブスクリプション キーを入力するか、ドロップダウン リストから 1 つ選択します。 ドロップダウン リストには、使用している Azure アカウントのサブスクリプションからのキーが表示されます。 [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)についてのページを参照してください。  

7. 自動提案を有効にした場合は、自動提案のサブスクリプション キーを入力するか、ドロップダウン リストから 1 つ選択します。 ドロップダウン リストには、使用している Azure アカウントのサブスクリプションからのキーが表示されます。 カスタム自動提案を利用するには、特定のサブスクリプション レベルが必要です。[価格](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)に関するページをご覧ください。

> [!NOTE]
> ホステッド UI のカスタム構成に変更を加えると、右のウィンドウには、加えた変更が見てわかるように表示されます。 表示される検索結果は、インスタンスの実際の結果ではありません。

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>カスタム UI を使用する

ホステッド UI を使用するには、次のいずれかを実行します。 

- Web ページにスクリプトを含めます  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- または、Web ブラウザーで次の URL を使用します。   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > 必要に応じて、次のクエリ パラメーターを URL に追加します。 これらのパラメーターについては、[Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) のリファレンスをご覧ください。
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > このページには、プライバシーに関する声明や、その他の通知および条項を表示できません。 使用状況に適合するかどうかはさまざまです。  

カスタム構成 ID を含めて、詳細については、**[Production]**(運用) タブの下にある **[エンドポイント]** に移動してください。

## <a name="next-steps"></a>次の手順

- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)