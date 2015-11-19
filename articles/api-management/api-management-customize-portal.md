<properties
	pageTitle="Azure API Management での開発者ポータルのカスタマイズ | Microsoft Azure"
	description="Azure API Management での開発者ポータルのカスタマイズ方法について説明します。"
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/27/2015"
	ms.author="sdanie"/>

# Azure API Management での開発者ポータルのカスタマイズ

このガイドでは、Azure API Management における開発者ポータルのルック アンド フィールをお客様のブランドに合わせてカスタマイズする方法について説明します。

## <a name="change-page-headers"> </a>ページ ヘッダーのテキストまたはロゴを変更する

ポータルの代表的なカスタマイズとして、すべてのページの一番上に表示されるテキストを貴社の会社名またはロゴに置き換えることが挙げられます。

開発者ポータル内のコンテンツは、Azure ポータルからパブリッシャー ポータルにアクセスして変更します。API パブリッシャー ポータルにアクセスするには、API Management サービスの Azure ポータルで **[管理]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

開発者ポータルは、コンテンツ管理システム (CMS) がベースとなっています。すべてのページに表示されるヘッダーは、"ウィジェット" と呼ばれる特殊なタイプのコンテンツです。そのウィジェットの内容を編集するには、左側の **[開発者ポータル]** メニューで **[ウィジェット]** をクリックし、一覧から **[ヘッダー]** ウィジェットを選択します。

![ウィジェット ヘッダー][api-management-widgets-header]

ヘッダーの内容は、**[本文]** フィールドで編集することができます。このテキストを "Fabrikam Developer Portal" に変更し、ページ下部の **[保存]** をクリックします。

以後、この新しいヘッダーが開発者ポータル内のすべてのページに表示されます。

> パブリッシャー ポータルで開発者ポータルを開くには、上部のバーにある **[開発者ポータル]** をクリックします。

## <a name="change-headers-styling"> </a>ヘッダーのスタイルを変更する

色、フォント、サイズ、文字間隔など、ポータル ページのスタイルに関連したあらゆる要素は、スタイルのルールによって定義されます。スタイルを編集するには、パブリッシャー ポータルで **[開発者ポータル]** メニューの **[外観]** をクリックし、**[カスタマイズの開始]** をクリックしてスタイル エディターを有効にします。

ブラウザーが開発者ポータル内に置かれている非表示のページに切り替わり、サンプル コンテンツや、サイト上のいたるところに使用されている、スタイル上のさまざまなルールが例示されます。スタイル エディターを開くには、ページの左端に設けられた縦長の薄い灰色の領域上にカーソルを移動します。エディター ツール バーが表示されます。

![カスタマイズ ツールバー][api-management-customization-toolbar]

スタイル上のルールは、主に 2 つの方法で編集できます。**[すべてのルールを編集]** を選択すると、あらゆる場所で使用されているすべてのスタイル ルールが一覧表示されます。これに対し、**[要素を選択]** を選択した場合は、表示されているページから特定の要素を選択することができます。その場合、表示されるのは、選択した要素に関するスタイルだけです。

このセクションでは、ヘッダーのスタイルのみを変更することにします。スタイル エディターのツール バーで **[要素を選択]** オプションをクリックし、続けて、**[カスタマイズする要素の選択]** をクリックします。要素にマウスを重ねると、その要素が強調表示されるようになり、クリックした場合にどの要素のスタイルが編集対象となるかを把握できます。ヘッダー内の会社名を表すテキスト (前セクションの手順でいうと "Fabrikam Developer Portal") 上にマウス カーソルを移動してクリックします。名前とカテゴリを持つ一連のスタイル ルールがスタイル エディター内に表示されます。

それぞれのルールは、選択された要素のスタイルに関するプロパティを表します。たとえば、上で選択したヘッダー テキストの場合、テキストのサイズは @font-size-h1 で定義され、フォント (代替候補を含む) の名前は @headings-font-family で定義されています。

> [Bootstrap][] をご存じの方のためにいうと、実際には、開発者ポータルに使用されている Bootstrap テーマの [LESS 変数][]にこれらのルールが含まれています。

見出しテキストの色を変更してみましょう。**[@headings-color]** ボックスの入力内容を選択し、「**#000000**」と入力します。これは、黒色を表す 16 進コードです。すると、テキスト ボックスの端に正方形のカラー インジケーターが表示されます。このインジケーターをクリックするとカラー ピッカーが表示され、色を選択することができます。

![カラー ピッカー][api-management-customization-toolbar-color-picker]

選択した要素のスタイルに変更を加えたら、**[変更のプレビュー]** をクリックして画面上で結果を確認します。この時点では、管理者にしか表示されません。変更を全員が閲覧できるようにするには、スタイル エディターで **[発行]** をクリックして変更を確定してください。

![[発行] メニュー  
][api-management-customization-toolbar-publish-form]

> ページの他の要素に適用されるスタイル ルールを変更するには、ヘッダーの場合と同じ手順を実行します。スタイル エディターで **[要素を選択]** をクリックし、対象の要素を選択して、画面に表示されるスタイル ルールの値の変更を開始します。

## <a name="edit-page-contents"> </a>ページの内容を編集する

開発者ポータルは、自動的に生成されるページ ([API]、[成果物]、[アプリケーション]、[問題] など) と手動で作成するコンテンツとで構成されます。コンテンツ管理システムがベースとなっているため、そのようなコンテンツを必要に応じて作成することができます。

既存のコンテンツ ページをすべて一覧表示するには、パブリッシャー ポータルの **[開発者ポータル]** メニューにある **[コンテンツ]** をクリックします。

![コンテンツの管理][api-management-customization-manage-content]

開発者ポータルのホーム ページに表示される内容を編集するには、**ウェルカム** ページをクリックします。目的の変更を行い、必要に応じてプレビューしてから **[今すぐ発行]** をクリックすると、すべての人がその内容を閲覧できるようになります。

> ホーム ページには、最上部にバナーを表示できる特殊なレイアウトが使用されています。このバナーを **[コンテンツ]** セクションから編集することはできません。このバナーを編集するには、**[開発者ポータル]** メニューの **[ウィジェット]** をクリックし、**[現在のレイヤー]** ボックスの一覧の **[ホーム ページ]** を選択して、**[おすすめ]** セクションにある **[バナー]** 項目を開きます。このウィジェットの内容は、他のページとまったく同じように編集できます。

## <a name="next-steps"> </a>次のステップ

-	「[Azure API Management の詳細な構成について][]」チュートリアルにあるその他のトピックもチェックしてください。

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[Azure API Management の詳細な構成について]: api-management-get-started-advanced.md
[bootstrap]: http://getbootstrap.com/
[LESS 変数]: http://getbootstrap.com/css/

<!----HONumber=Nov15_HO1-->