---
title: Azure API Management での開発者ポータルのページ コンテンツの変更 | Microsoft Docs
description: Azure API Management での開発者ポータルのページ コンテンツの編集方法について説明します。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: 4a372b54ace87816b9e7cccbd38466331b5ebfc7
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437598"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Azure API Management で開発者ポータルのページのコンテンツとレイアウトを変更する
Azure API Management で開発者ポータルをカスタマイズする基本的な方法は 3 つあります。

* [静的なページとページ レイアウト要素の内容を編集する][modify-content-layout] (このガイドで説明します)
* [開発者ポータル全体のページ要素で使用されるスタイルを更新する][customize-styles]
* [ポータルで生成されたページで使用されるテンプレートを変更する][portal-templates] (例: API ドキュメント、製品、ユーザー認証など)

## <a name="page-structure"> </a>開発者ポータルのページの構造

開発者ポータルは、コンテンツ管理システムがベースとなっています。 すべてのページのレイアウトは、ウィジェットと呼ばれる小さなページ要素のセットを基にして構築されています。

![開発者ポータル ページの構造][api-management-customization-widget-structure]

ウィジェットはすべて編集できます。
* 各ページ固有の中心的なコンテンツは、"コンテンツ" ウィジェットに存在します。 ページを編集することは、このウィジェットの内容を編集することを意味します。
* すべてのページ レイアウト要素は、残りのウィジェットに含まれます。 これらのウィジェットに加えられた変更は、すべてのページに適用されます。 これらは、"レイアウト ウィジェット" と呼ばれます。

日常的なページの編集では、通常、ページごとに異なる内容が含まれるコンテンツ ウィジェットのみが変更されます。

## <a name="modify-layout-widget"> </a>レイアウト ウィジェットの内容の変更

開発者ポータルには、Azure Portal からアクセスすることができます。

1. API Management インスタンスのツール バーの **[開発者ポータル]** をクリックします。
2. ウィジェットの内容を編集するには、左側の **[開発者ポータル]** メニューにある 2 つのペイント ブラシのアイコンをクリックします。
3. ヘッダーの内容を変更するには、左側の一覧で **[ヘッダー]** セクションまでスクロールします。

    ウィジェットは、フィールド内から編集することができます。
4. 変更を発行する準備ができたら、ページの下部の **[発行]** をクリックします。

以後、この新しいヘッダーが開発者ポータル内のすべてのページに表示されます。

## <a name="next-steps"> </a>次のステップ
* [開発者ポータル全体のページ要素で使用されるスタイルを更新する][customize-styles]
* [ポータルで生成されたページで使用されるテンプレートを変更する][portal-templates] (例: API ドキュメント、製品、ユーザー認証など)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
