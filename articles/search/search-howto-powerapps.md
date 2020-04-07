---
title: Power Apps から Azure Cognitive Search に対してクエリを実行する方法
titleSuffix: Azure Cognitive Search
description: Cognitive Search へのカスタム コネクタの作成方法と、Power App で視覚化する方法に関するステップ バイ ステップのガイダンス
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385114"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Power Apps から Cognitive Search インデックスに対してクエリを実行する方法

このドキュメントでは、検索インデックスから検索結果を取得できるように、Power Apps カスタム コネクタを作成する方法について説明します。 また、検索クエリを発行し、その結果を Power App から視覚化する方法についても説明します。 

## <a name="prerequisites"></a>前提条件
*    カスタム コネクタの作成機能を備えた Power Apps アカウントへのアクセス。
*    Azure Search インデックスが既に作成されていることを前提とします。

## <a name="create-a-custom-connector-to-query-azure-search"></a>Azure Search に対してクエリを実行するためのカスタム コネクタの作成

Azure Cognitive Search の結果を表示する PowerApp を用意するには、主に 2 つの手順があります。 まず、検索インデックスに対してクエリを実行できるコネクタを作成してみましょう。 [次のセクション](#visualize-results-from-the-custom-connector)では Power Apps アプリケーションを更新して、コネクタによって返される結果を視覚化します。

1. [make.powerapps.com](http://make.powerapps.com) にアクセスして、**サインイン**します。

1. **[データ]**  >  **[カスタム コネクタ]** を探します
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="[カスタム コネクタ] メニュー" border="true":::

1. **[+ New custom connector]\(+ 新しいカスタム コネクタ\)** をクリックし、 **[一から作成]** を選択します。

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="[一から作成] メニュー" border="true":::

1. カスタム コネクタに名前を付けます。 (ここでは *AzureSearchQuery* とします) そして、 **[続行]** をクリックします。 これにより、新しいコネクタを作成するためのウィザードが表示されます。

1. [全般] ページに情報を入力します。

    - [アイコンの背景色] (たとえば、#007ee5)
    - [説明] (例:「Azure Cognitive Search へのコネクタ」)
    - [ホスト] には、検索サービスの URL (たとえば `<yourservicename>.search.windows.net`) を入力する必要があります
    - [ベース URL] には、単に「/」と入力します
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="[全般情報] ダイアログ" border="true":::

1. [セキュリティ] ページで、 **[認証の種類]** として *[API キー]* を設定し、パラメーター ラベルとパラメーター名フィールドを「*api-key*」に設定します。 **[パラメーターの場所]** については、次に示すように *[ヘッダー]* を選択します。
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="[認証の種類] オプション" border="true":::

1. [定義] ページで **[+ 新しいアクション]** を選択して、インデックスに対してクエリを実行するアクションを作成します。 概要および操作 ID の名前として「Query」という値を入力します。 *「検索インデックスに対してクエリを実行する」* のような説明を入力します。
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="[新しいアクション] オプション" border="true":::


1. **[+ サンプルからのインポート]** ボタンをクリックして、パラメーターとヘッダーを定義します。 次に、クエリ要求を定義します。  

    * [動詞] は `GET` を選択します
    * [URL] には、検索インデックスのサンプル クエリを入力します。たとえば、次のようになります。
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search= *&api-version=2019-05-06-Preview
    

    **Power Apps** では、クエリからのパラメーターの抽出に構文が使用されます。 検索フィールドが明示的に定義されていることに注目してください。 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="[サンプルからのインポート]" border="false":::

1.  **[インポート]** をクリックすると、[要求] ダイアログが前もって自動的に入力されます。

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="サンプル ダイアログからのインポート" border="false":::


1. 各パラメータの横にある **...** シンボルをクリックして、パラメーターのメタデータの設定を 完了します。

    - *search* では: **[default value]** を `*` に、 **[required]** を *[false]* に、 **[Visibility]** を *[none]* に設定します。 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="検索パラメーターのメタデータ" border="true":::

    - *api-version* では: **[default value]** を `2019-05-06-Preview` に、 **[Visibility]** を [internal] に、 **[required]** を *[True]* に設定します。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="バージョン パラメーターのメタデータ" border="true":::

    - 同様に *api-key* の場合は、 **[required]** 、 **[Visibility]** を *[internal]* として設定します。 **[default value]** として、検索サービスの API キーを入力します。
    
    これらの変更を行った後、**Swagger エディター**ビューに切り替えます。 [parameters] セクションには、次の構成が表示されます。    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. [応答] セクションで、 **[既定の応答を追加する]** をクリックします。 これは **Power Apps** に応答のスキーマを理解させるのに役立つため、非常に重要です。 応答のサンプルを貼り付けます。

    > [!TIP] 
    > 入力できる JSON の応答には文字数制限があります。そのため、JSON を貼り付ける前に簡略化することをお勧めします。 重要なのは、応答のスキーマや形式です。 応答のサンプル内の実際の値は重要度が低いため、文字数を減らすために簡略化できます。
    

1.    テストする前に、画面の右上にある **[コネクタの作成]** ボタンをクリックします。

1.  [テスト] ページで **[+ 新しい接続]** をクリックし、 *[api-key]* の値として検索サービスのクエリ キーを入力します。

    この手順を実行するとウィザードが終了し、[接続] ページが表示される場合があります。 カスタム接続エディターに戻り、実際に接続をテストすることをお勧めします。 **[カスタム コネクタ]** にアクセス > 新しく作成されたコネクタを選択 > *...* >  **[プロパティの表示]**  >  **[編集]**  >  **[4.テスト]** の順に選択して、[テスト] ページに戻ります。

1.    次に、 **[テスト操作]** をクリックして、インデックスから結果が取得されていることを確認します。 成功した場合は、200 のステータスが表示され、応答の本文には検索結果を示す JSON が表示されます。




## <a name="visualize-results-from-the-custom-connector"></a>カスタム コネクタによる結果を視覚化する
このチュートリアルの目的は、Power Apps で凝ったユーザー エクスペリエンスを作成する方法を説明することではないため、UI レイアウトは最低限のものになります。 検索ボックスと検索ボタンを備えた PowerApp を作成し、結果をギャラリー コントロールに表示してみましょう。  PowerApp では先ほど作成したカスタム コネクタに接続して、Azure Search からデータを取得します。

1. 新しい Power App を作成します。 **[アプリ]** セクションにアクセスし、 **[+ 新しいアプリ]** をクリックして、 **[キャンバス]** を選択します。

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="キャンバス アプリの作成" border="true":::

1. アプリケーションの種類を選択します。 このチュートリアルでは、 **[携帯電話レイアウト]** を使用して **[空のアプリ]** を作成します。 **[Power Apps Studio]** が表示されます。

1. Studio で **[データ ソース]** タブを選択し、先ほど作成した新しいコネクタをクリックします。 この例では、*AzureSearchQuery* という名前です。 **[接続の追加]** をクリックします。

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="コネクタの接続" border="true":::

    これで *AzureSearchQuery* が、アプリケーションから使用できるデータ ソースとなりました。
    
1. フォームにいくつかのコントロールを追加するために、 **[挿入] タブ**に移動します。

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="コントロールの挿入" border="true":::

1.  次の要素を挿入します。
    -   「Query:」 という値を持つテキスト ラベル
    -   テキスト入力要素 (*txtQuery* と呼びます。既定値は「*」です)
    -   「検索」 というテキストが表示されたボタン 
    -   呼び出された垂直ギャラリー (*galleryResults* と呼びます。)
    
    フォームは次のようになります。

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="コントロールのレイアウト" border="true":::

1. **[検索] ボタン**にクエリを実行させるには、ボタンを選択し、次のアクションを **OnSelect** に貼り付けます。

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="ボタンの OnSelect" border="true":::
 
    この操作により、 *[txtQuery]* テキスト ボックス内のテキストをクエリ語句として使用した検索クエリの結果によって、*azResult* という名前の新しいコレクションが更新されます。
    
1.  次の手順として、作成した垂直ギャラリーを *azResult* コレクションにリンクします。 ギャラリー コントロールを選択し、[プロパティ] ペインで次の操作を実行します。

    -  **[データソース]** を *azResult* に設定します。
    
    -  インデックス内のデータの種類に基づいて、適切な **[レイアウト]** を選択します。 この例では、 *[Title, subtitle and body]\(タイトル、サブタイトル、および本文\)* レイアウトを使用しています。
    
    -  **[フィールド] を編集**し、視覚化するフィールドを選択します。

    コネクタを定義したときにサンプルの結果を提供しているため、アプリによりインデックスで使用できるフィールドが認識されます。
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="ギャラリー フィールド" border="true":::   
 
1.  **F5** キーを押して、アプリをプレビューします。  

    フィールドには計算値を設定できることに注意してください。      
    例として、 *[Image, Title and Subtitle]\(イメージ、タイトル、サブタイトル\)* レイアウトを使用し、*イメージ*機能をデータのルート パスとファイル名の連結 (たとえば、`"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) として指定した場合は、次の結果が生成されます。

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="最終的なアプリ" border="true":::        

## <a name="next-steps"></a>次のステップ

詳細およびオンライン トレーニングについては、[「Power Apps の学習カタログ」](https://docs.microsoft.com/powerapps/learning-catalog/get-started)に関するページを参照してください。

