<properties 
	pageTitle="Azure API Management で開発者ポータルの外観をカスタマイズする方法" 
	description="Azure API Management で開発者ポータルの外観をカスタマイズする方法について説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Azure API Management で開発者ポータルの外観をカスタマイズする方法

開発者ポータルの外観の色、フォント、サイズ、文字間隔、その他の側面は、スタイルのルールによって定義されます。これらのルールのセットは、ページの構造要素 (ヘッダー、メニュー、コンテンツ本文、ページ タイトルなど) ごとに存在します。このチュートリアルでは、スタイルのルールを変更する方法について説明します。

スタイル ルールを編集するには、パブリッシャー ポータルの **[開発者ポータル]** メニューにある **[外観]** をクリックします。そこで **[カスタマイズの開始]** をクリックすると、スタイル エディターが有効になります。

ご利用のブラウザーが、開発者ポータル内に置かれている非表示のページに切り替わり、サンプル コンテンツや、サイト上のいたるところに使用されている、スタイル上のさまざまなルールが例示されます。スタイル エディターを開くには、ページの左端に設けられた縦長の薄い灰色の領域上にカーソルを移動します。次のようにエディター ツール バーが表示されます。

![Customization toolbar][api-management-customization-toolbar]

スタイルのルールは、主に 2 つの方法で編集することができます。**[すべてのルールを編集]** を選択すると、あらゆる場所で使用されているスタイルのルールがすべて一覧表示されます。これに対し、**[要素を選択]** を選択した場合は、表示されているページから特定の要素を選択することができます。その場合、表示されるのは、その特定の要素に関するスタイルだけです。

このセクションでは、特定の要素 (たとえば、ページ ヘッダー) のスタイルのみを変更することにします。スタイル エディターのツール バーで **[要素を選択]** オプションをクリックし、続けて、**[カスタマイズする要素の選択]** をクリックします。要素にマウス カーソルを重ねると、その要素が強調表示され、クリックした場合に適用される要素のスタイルが表示されます。

ヘッダー内のページ タイトルを表すテキスト上にマウス カーソルを移動してクリックします。名前とカテゴリを持つ一連のスタイル ルールがスタイル エディター内に表示されます。

それぞれのルールは、選択された要素のスタイルに関するプロパティを表します。たとえば、上で選択したヘッダー テキストの場合、テキストのサイズは @font-size-h1 で定義され、フォント (代替候補を含む) の名前は @headings-font-family で定義されています。

> [Bootstrap](http://getbootstrap.com/) をご存じの方のためにいうと、実際には、開発者ポータルに使用されている Bootstrap テーマの [LESS 変数](http://getbootstrap.com/css/)にこれらのルールが含まれています。

ここで、見出しテキストの色を変更してみましょう。 **[@headings-color]** ボックスの入力内容を選択し、「#000000」と入力します。これは、黒色を表す 16 進コードです。すると、テキスト ボックスの端に正方形のカラー インジケーターが表示されます。このインジケーターをクリックするとカラー ピッカーが表示され、色を選択することができます。

![Color picker][api-management-customization-toolbar-color-picker]

選択した要素のスタイルに変更を加えたら、**[変更のプレビュー]** をクリックして画面上で結果を確認します。この時点では、管理者にしか表示されません。変更を全員が閲覧できるようにするには、スタイル エディターで **[発行]** をクリックして変更を確定してください。

![Publish form][api-management-customization-toolbar-publish-form]

> ページ上のヘッダー以外の要素についても、適用されるスタイル ルールを変更する手順は同じです。つまり、スタイル エディターから **[要素を選択]** をクリックし、目的の要素を選択したうえで、画面に表示されるスタイル ルールの値に変更を加えます。


[Next steps]: #next-steps


[api-management-customization-toolbar]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png

<!---HONumber=AcomDC_0309_2016-->