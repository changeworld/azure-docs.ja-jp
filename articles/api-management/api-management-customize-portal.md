---
title: "Azure API Management での開発者ポータルのカスタマイズ | Microsoft Docs"
description: "Azure API Management での開発者ポータルのカスタマイズ方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 13431648e87d085161ad278dc991d49f7872be34
ms.openlocfilehash: 60213f885020a5ba36d6ada0812f755e06b3c48b


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>Azure API Management での開発者ポータルのカスタマイズ
このガイドでは、Azure API Management における開発者ポータルのルック アンド フィールをお客様のブランドに合わせてカスタマイズする方法について説明します。

## <a name="change-page-headers"> </a>ページ ヘッダーのテキストまたはロゴを変更する
ポータルの代表的なカスタマイズとして、すべてのページの一番上に表示されるテキストを貴社の会社名またはロゴに置き換えることが挙げられます。

開発者ポータル内のコンテンツは、Azure Portal からアクセスできるパブリッシャー ポータルを使用して変更します。 パブリッシャー ポータルにアクセスするには、API Management インスタンスのサービス ツール バーの **[パブリッシャー ポータル]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

開発者ポータルは、コンテンツ管理システム (CMS) がベースとなっています。 すべてのページに表示されるヘッダーは、"ウィジェット" と呼ばれる特殊なタイプのコンテンツです。 そのウィジェットの内容を編集するには、左側の **[開発者ポータル]** メニューで **[ウィジェット]** をクリックし、一覧から **[ヘッダー]** ウィジェットを選択します。

![Widgets header][api-management-widgets-header]

ヘッダーの内容は、 **[本文]** フィールドで編集することができます。 このテキストを "Fabrikam Developer Portal" に変更し、ページ下部の **[保存]** をクリックします。

以後、この新しいヘッダーが開発者ポータル内のすべてのページに表示されます。

> パブリッシャー ポータルで開発者ポータルを開くには、上部のバーにある **[開発者ポータル]** をクリックします。
> 
> 

## <a name="change-headers-styling"> </a>ヘッダーのスタイルを変更する
色、フォント、サイズ、文字間隔など、ポータル ページのスタイルに関連したあらゆる要素は、スタイルのルールによって定義されます。 スタイルを編集するには、**開発者ポータル**でカスタマイズ アイコンをポイントして、左側のカスタマイズ ツール バーを開き、ツール バーで [スタイル] を選択します。

![カスタマイズ ツール バー ボタン][api-management-customization-toolbar-button]

スタイルのルールを編集する方法は、主に&2; つあります。1 つ目は、既定で表示される、いずれかの場所で使用されているすべてのスタイル ルールの一覧でスタイルを探し、必要に応じて変更する方法です。もう&1; つは、**[Select an element on the page (ページの要素を選択)]** を選択し、ページの任意の場所をクリックして、その要素のスタイルだけを表示する方法です。

このセクションでは、ヘッダーのスタイルのみを変更することにします。 スタイル エディター ツール バーの **[Select an element on the page (ページの要素を選択)]** オプションをクリックします。 

![Customization toolbar][api-management-customization-toolbar]

要素にマウスを重ねると、その要素が強調表示されるようになり、クリックした場合にどの要素のスタイルが編集対象となるかを把握できます。 ヘッダー内の会社名を表すテキスト (前セクションの手順でいうと "Fabrikam Developer Portal") 上にマウス カーソルを移動してクリックします。 名前とカテゴリを持つ一連のスタイル ルールがスタイル エディター内に表示されます。 それぞれのルールは、選択された要素のスタイルに関するプロパティを表します。 たとえば、上で選択したヘッダー テキストの場合、テキストのサイズは @font-size-h1 で定義され、フォント (代替候補を含む) の名前は @headings-font-family で定義されています。

> [Bootstrap][bootstrap] をご存じの方のためにいうと、実際には、開発者ポータルに使用されている Bootstrap テーマの [LESS 変数][LESS variables]にこれらのルールが含まれています。
> 
> 

見出しテキストの色を変更してみましょう。 **@headings-color** ボックスの入力内容を選択し、「**#000000**」と入力します。 これは、黒色を表す&16; 進コードです。 すると、テキスト ボックスの端に正方形のカラー インジケーターが表示されます。 このインジケーターをクリックするとカラー ピッカーが表示され、色を選択することができます。

![Color picker][api-management-customization-toolbar-color-picker]

変更は操作時にリアルタイムでプレビューされますが、管理者だけに表示されます。 変更を全員が閲覧できるようにするには、スタイル エディターで **[発行]** をクリックして変更を確定してください。

![[発行] メニュー][api-management-customization-toolbar-publish-form]

> ページの他の要素に適用されるスタイル ルールを変更するには、ヘッダーの場合と同じ手順を実行します。 スタイル エディターで **[要素を選択]** をクリックし、対象の要素を選択して、画面に表示されるスタイル ルールの値の変更を開始します。
> 
> 

## <a name="edit-page-contents"> </a>ページの内容を編集する
開発者ポータルは、自動的に生成されるページ ([API]、[成果物]、[アプリケーション]、[問題] など) と手動で作成するコンテンツとで構成されます。 コンテンツ管理システムがベースとなっているため、そのようなコンテンツを必要に応じて作成することができます。

既存のコンテンツ ページをすべて一覧表示するには、パブリッシャー ポータルの **[開発者ポータル]** メニューにある **[コンテンツ]** をクリックします。

![Manage content][api-management-customization-manage-content]

開発者ポータルのホーム ページに表示される内容を編集するには、 **ウェルカム** ページをクリックします。 目的の変更を行い、必要に応じてプレビューしてから **[今すぐ発行]** をクリックすると、すべての人がその内容を閲覧できるようになります。

> ホーム ページには、最上部にバナーを表示できる特殊なレイアウトが使用されています。 このバナーを **[コンテンツ]** セクションから編集することはできません。 このバナーを編集するには、**[開発者ポータル]** メニューの **[ウィジェット]** をクリックし、**[現在のレイヤー]** ボックスの一覧の **[ホーム ページ]** を選択して、**[おすすめ]** セクションにある **[バナー]** 項目を開きます。 このウィジェットの内容は、他のページとまったく同じように編集できます。
> 
> 

## <a name="next-steps"> </a>次のステップ
* [開発者ポータル テンプレート](api-management-developer-portal-templates.md)を使用して開発者ポータル ページの内容をカスタマイズする方法について説明します。

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-portal/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/



<!--HONumber=Feb17_HO1-->


