<properties pageTitle="Azure API Management での開発者ポータルのカスタマイズ" metaKeywords="" description="Azure API Management での開発者ポータルのカスタマイズ。" metaCanonical="" services="api-management" documentationCenter="API Management" title="Azure API Management での開発者ポータルのカスタマイズ" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Azure API Management での開発者ポータルのカスタマイズ

このガイドでは、API Management における開発者ポータルのルック アンド フィールをお客様のブランドに合わせてカスタマイズする方法について説明します。

## このトピックの内容

-   [ページ ヘッダーのテキスト/ロゴを変更する][ページ ヘッダーのテキスト/ロゴを変更する]
-   [ヘッダーのスタイルを変更する][ヘッダーのスタイルを変更する]
-   [ページの内容を編集する][ページの内容を編集する]
-   [次のステップ][次のステップ]

## <a name="change-page-headers"> </a>ページ ヘッダーのテキスト/ロゴを変更する

ポータルの代表的なカスタマイズとして、すべてのページの一番上に表示されるテキストを貴社の会社名またはロゴに置き換えることが挙げられます。

開発者ポータル内のコンテンツは、Azure の管理ポータルからパブリッシャー ポータルにアクセスして変更します。API Management コンソールには、API Management サービスの Azure ポータルの **[管理コンソール]** をクリックしてアクセスします。

![Management console][Management console]

開発者ポータルは、コンテンツ管理システム (CMS) がベースとなっています。すべてのページに表示されるヘッダーは、"ウィジェット" と呼ばれる特殊なタイプのコンテンツです。そのウィジェットの内容を編集するには、左側の **[開発者ポータル]** メニューで **[ウィジェット]** をクリックし、一覧から **[ヘッダー]** ウィジェットを選択します。

![Widgets header][Widgets header]

ヘッダーの内容は、**[本文]** フィールドで編集することができます。このテキストを "Fabrikam Developer Portal" に変更し、ページ下部の **[保存]** をクリックします。

以後、この新しいヘッダーが開発者ポータル内のすべてのページに表示されます。

> 管理コンソールで開発者ポータルを開くには、上部のバーにある **[開発者ポータル]** をクリックします。

## <a name="change-headers-styling"> </a>ヘッダーのスタイルを変更する

色、フォント、サイズ、文字間隔など、ポータル ページのスタイルに関連したあらゆる要素は、スタイルのルールによって定義されます。スタイルを編集するには、パブリッシャー ポータルの **[開発者ポータル]** メニューにある **[外観]** をクリックします。そこで **[カスタマイズの開始]** をクリックすると、スタイル エディターが有効になります。

ご利用のブラウザーが、開発者ポータル内に置かれている非表示のページに切り替わり、サンプル コンテンツや、サイト上のいたるところに使用されている、スタイル上のさまざまなルールが例示されます。スタイル エディターを開くには、ページの左端に設けられた縦長の薄い灰色の領域上にカーソルを移動します。エディター ツール バーが表示されます。

![Customization toolbar][Customization toolbar]

スタイル上のルールは、主に 2 つの方法で編集することができます。**[すべてのルールを編集]** を選択すると、あらゆる場所で使用されているすべてのスタイル ルールが一覧表示されます。これに対し、**[要素を選択]** を選択した場合は、表示されているページから特定の要素を選択することができます。その場合、表示されるのは、選択した要素に関するスタイルだけです。

このセクションでは、ヘッダーのスタイルのみを変更することにします。スタイル エディターのツール バーで **[要素を選択]** オプションをクリックし、続けて、**[カスタマイズする要素の選択]** をクリックします。要素にマウスを重ねると、その要素が強調表示されるようになり、クリックした場合にどの要素のスタイルが編集対象となるかを把握できます。ヘッダー内の会社名を表すテキスト (前セクションの手順でいうと "Fabrikam Developer Portal") 上にマウス カーソルを移動してクリックします。名前とカテゴリを持つ一連のスタイル ルールがスタイル エディター内に表示されます。

それぞれのルールは、選択された要素のスタイル上の特性を表します。たとえば、上で選択したヘッダー テキストの場合、テキストのサイズは @font-size-h1 で定義され、フォント (代替候補を含む) の名前は @headings-font-family で定義されています。

> [Bootstrap][Bootstrap] をご存じの方のためにいうと、実際には、開発者ポータルに使用されている Bootstrap テーマの [LESS 変数][LESS 変数]にこれらのルールが含まれています。

見出しテキストの色を変更してみましょう。**[@headings-color]** フィールドのエントリを選択し、「\#000000」と入力します。これは、黒色を表す 16 進コードです。すると、テキスト ボックスの端に正方形のカラー インジケーターが表示されます。このインジケーターをクリックするとカラー ピッカーが表示され、色を選択することができます。

![Color picker][Color picker]

選択した要素のスタイルに変更を加えたら、**[変更のプレビュー]** をクリックして画面上で結果を確認します。この時点では、管理者にしか表示されません。変更を全員が閲覧できるようにするには、スタイル エディターで **[発行]** をクリックして変更を確定してください。

![Publish menu][Publish menu]

> ページ上のヘッダー以外の要素についても、適用されるスタイル ルールを変更する手順は同じです。つまり、スタイル エディターから **[要素を選択]** をクリックし、目的の要素を選択したうえで、画面に表示されるスタイル ルールの値に変更を加えます。

## <a name="edit-page-contents"> </a>ページの内容を編集する

開発者ポータルは、自動的に生成されるページ ([API]、[成果物]、[アプリケーション]、[問題] など) と手動で作成するコンテンツとで構成されます。コンテンツ管理システムがベースとなっているため、そのようなコンテンツを必要に応じて作成することができます。

既存のコンテンツ ページをすべて一覧表示するには、管理コンソールの **[開発者ポータル]** メニューにある **[コンテンツ]** をクリックします。

![Manage content][Manage content]

開発者ポータルのホーム ページに表示される内容を編集するには、ウェルカム ページをクリックします。目的の変更を行い、必要に応じてプレビューしてから **[今すぐ発行]** をクリックすると、すべての人がその内容を閲覧できるようになります。

> ホーム ページには、最上部にバナーを表示できる特殊なレイアウトが使用されています。このバナーをコンテンツ セクションから編集することはできません。このバナーを編集するには、**[開発者ポータル]** メニューの **[ウィジェット]** をクリックし、**[現在のレイヤー]** ボックスの一覧の **[ホーム ページ]** を選択して、[おすすめ] セクションにある **[バナー]** 項目を開きます。このウィジェットの内容は、他のページとまったく同じように編集できます。

## <a name="next-steps"> </a>次のステップ

-   「[Azure API Management の詳細な構成について][Azure API Management の詳細な構成について]」チュートリアルにあるその他のトピックもチェックしてください。

  [ページ ヘッダーのテキスト/ロゴを変更する]: #change-page-headers
  [ヘッダーのスタイルを変更する]: #change-headers-styling
  [ページの内容を編集する]: #edit-page-contents
  [次のステップ]: #next-steps
  [Management console]: ./media/api-management-customize-portal/api-management-management-console.png
  [Widgets header]: ./media/api-management-customize-portal/api-management-widgets-header.png
  [Customization toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
  [Bootstrap]: http://getbootstrap.com/
  [LESS 変数]: http://getbootstrap.com/css/
  [Color picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
  [Publish menu]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
  [Manage content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png
  [Azure API Management の詳細な構成について]: ../api-management-get-started-advanced
