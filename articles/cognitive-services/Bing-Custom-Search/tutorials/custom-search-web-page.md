---
title: 'Bing Custom Search: カスタム検索 Web ページの作成 | Microsoft Docs'
description: カスタム検索インスタンスを構成して、Web ページに統合する方法について説明します
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374757"
---
# <a name="build-a-custom-search-web-page"></a>Custom Search Web ページの作成
Bing Custom Search を使用すると、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。 たとえば、検索エクスペリエンスを提供する武術に関する Web サイトを所有している場合は、Bing が検索するドメイン、サブサイト、および Web ページを指定できます。 ユーザーは、関係のないコンテンツが含まれる可能性がある通常の検索結果のページを移動し続けるのではなく、関心のあるコンテンツに合わせてカスタマイズされた検索結果を参照できます。 

このチュートリアルでは、カスタム検索インスタンスを構成して、新しい Web ページに統合する方法について説明します。

説明するタスクは次のとおりです。

> [!div class="checklist"]
> - カスタム検索インスタンスの作成
> - アクティブなエントリの追加
> - ブロックするエントリの追加
> - ピン留めするエントリの追加
> - カスタム検索の Web ページへの統合

## <a name="prerequisites"></a>前提条件
- チュートリアルを進めるには、Bing Custom Search API のサブスクリプション キーが必要です。  キーの入手については、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)」をご覧ください。
- まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。

## <a name="create-a-custom-search-instance"></a>カスタム検索インスタンスの作成
Bing Custom Search インスタンスを作成するには:

1.  インターネット ブラウザーを開きます。
2.  カスタム検索[ポータル](https://customsearch.ai)に移動します。
3.  Microsoft アカウント (MSA) を使用して、ポータルにサインインします。 MSA を持っていない場合は、**[Microsoft アカウントの作成]** をクリックします。 初めてポータルを使用する場合、データにアクセスするための権限を求められます。 **[はい]** をクリックします。
4.  サインイン後、**[New custom search]**(新しいカスタム検索) をクリックします。 **[Create a new custom search instance]**(新しいカスタム検索インスタンスの作成) ウィンドウで、検索で返されるコンテンツの種類を説明するわかりやすい名前を入力します。 名前はいつでも変更できます。
 
    ![新しいカスタム検索インスタンスの作成ボックスのスクリーン ショット](../media/newCustomSrch.png)

5.  [OK] をクリックして、URL と、ベース ページのサブページを含めるかどうかを指定します。

    ![URL 定義ページのスクリーン ショット](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>アクティブなエントリの追加
特定のサイトや URL からの結果を含めるには、それらを **[アクティブ]** タブに追加します。

1.  **[定義エディター]** で **[アクティブ]** タブをクリックし、検索に含める 1 つまたは複数のサイトの URL を入力します。

    ![定義エディターのアクティブ タブのスクリーン ショット](../media/customSrchEditor.png)

2.  インスタンスが結果を返すことを確認するには、右側のプレビュー ウィンドウにクエリを入力します。 Bing は、インデックスが付けられている公開サイトの結果のみを返します。

## <a name="add-blocked-entries"></a>ブロックするエントリの追加
特定のサイトや URL からの結果を除外するには、それらを **[ブロック]** タブに追加します。

1. **[定義エディター]** で **[ブロック]** タブをクリックし、検索から除外する 1 つまたは複数のサイトの URL を入力します。

    ![定義エディターのブロック タブのスクリーン ショット](../media/blockedCustomSrch.png)


2. インスタンスがブロックするサイトの結果を返さないことを確認するには、右側のプレビュー ウィンドウにクエリを入力します。 

## <a name="add-pinned-entries"></a>ピン留めするエントリの追加
特定の Web ページを検索結果の最上位にピン留めするには、**[Pinned]**(ピン留め) タブに Web ページと検索語を追加します。**[Pinned]**(ピン留め) タブには、特定のクエリの最上位の結果として表示される Web ページを指定する Web ページと検索語のペアの一覧が含まれています。 Web ページが上部にピン留めされるには、ユーザーの検索語がピン留めされた検索語と正確に一致する必要があります。

1. **[定義エディター]** で **[Pinned]**(ピン留め) タブをクリックし、上位の検索結果として返される Web ページと検索語を入力します。

    ![定義エディターのピン留めタブのスクリーン ショット](../media/pinnedCustomSrch.png)

2. インスタンスが上位の検索結果として指定された Web ページを返すことを確認するには、右側のプレビュー ウィンドウにピン留めした検索語を入力します。

## <a name="configure-hosted-ui"></a>ホステッド UI の構成
カスタム検索は、カスタム検索インスタンスの JSON 応答を表示するためにホステッド UI を提供します。  UI エクスペリエンスを定義するには:

1. **[Hosted UI]**(ホステッド UI) タブをクリックします。
2. レイアウトを選択します。

    ![ホステッド UI のレイアウト選択手順のスクリーン ショット](./media/custom-search-hosted-ui-select-layout.png)

3. 配色テーマを選択します。

    ![ホステッド UI のレイアウト選択手順のスクリーン ショット](./media/custom-search-hosted-ui-select-color-theme.png)

4. その他の構成オプションを指定します。

    ![ホステッド UI のその他の構成手順のスクリーン ショット](./media/custom-search-hosted-ui-additional-configurations.png)

5. **[サブスクリプション キー]** を貼り付けます。 「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api)」を参照してください。

    ![ホステッド UI のその他の構成手順のスクリーン ショット](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>ホステッド UI の使用
ホステッド UI は、2 つの方法で使用することができます。  

- オプション 1: 提供される JavaScript スニペットをアプリケーションに統合
- オプション 2: 提供される HTML エンドポイントを使用

このチュートリアルの残りの部分では、**オプション 1: Javascript スニペット**について説明します。  

## <a name="set-up-your-visual-studio-solution"></a>Visual Studio ソリューションのセットアップ
1. コンピューターで **Visual Studio** を開きます。
2. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。
3. **[新しいプロジェクト]** ウィンドウで、**[Visual C#] / [Web] / [ASP.NET Core Web アプリケーション]** の順に選択し、プロジェクトに名前を付けて **[OK]** をクリックします。
   
    ![新しいプロジェクト ウィンドウのスクリーン ショット](./media/custom-search-new-project.png)

4. **[新しい ASP.NET Core Web アプリケーション]** ウィンドウで、**[Web アプリケーション]** を選択して **[OK]** をクリックします。
    
    ![新しいプロジェクト ウィンドウのスクリーン ショット](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>index.cshtml の編集
1. **[ソリューション エクスプローラー]** で **[ページ]** を展開し、**index.cshtml** をダブルクリックしてこのファイルを開きます。

    ![ページを展開して index.cshtml を選択したソリューション エクスプローラーのスクリーン ショット](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. index.cshtml の 7 行目以降をすべてを削除します。
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. 改行要素とコンテナーとして機能する div を追加します。

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. **[Hosted UI]**(ホステッド UI) タブで、**[Consuming the UI]**(UI の使用) セクションまで下にスクロールします。 JavaScript スニペットをコピーします。

    ![ホステッド UI の保存ボタンのスクリーン ショット](./media/custom-search-hosted-ui-consuming-ui.png)

5. script 要素を追加したコンテナーに貼り付けます。

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch">
        <script type="text/javascript"
                id="bcs_js_snippet"
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. **[ソリューション エクスプローラー]** で **wwwroot** を右クリックし、**[ブラウザーで表示]** をクリックします。

    ![wwwroot コンテキスト メニューのブラウザーで表示を選択したソリューション エクスプローラーのスクリーン ショット](./media/custom-search-webapp-view-in-browser.png)

新しいカスタム検索 Web ページは、次のようになります。

![カスタム検索 Web ページのスクリーン ショット](./media/custom-search-webapp-browse-index.png)

検索を実行すると、次のような結果が表示されます。

![カスタム検索結果のスクリーン ショット](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> - カスタム検索インスタンスの作成
> - アクティブなエントリの追加
> - ブロックするエントリの追加
> - ピン留めするエントリの追加
> - カスタム検索の Web ページへの統合

これで、プログラムからの Bing Custom Search エンドポイントを呼び出しについて学習できるようになりました。

> [!div class="nextstepaction"]
> [Bing Custom Search エンドポイントを呼び出す (C#)](../call-endpoint-csharp.md)