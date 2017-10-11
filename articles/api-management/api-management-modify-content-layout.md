---
title: "Azure API Management で開発者ポータルでページの内容の変更 |Microsoft ドキュメント"
description: "Azure API Management で開発者ポータルでのページの内容を編集する方法を説明します。"
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Azure API Management で開発者ポータルのページのレイアウトと内容を変更します。
これには Azure API Management で開発者ポータルをカスタマイズする 3 つの基本的な方法があります。

* [静的なページとページ レイアウト要素の内容を編集][ modify-content-layout] (このガイドで説明されている)
* [開発者ポータル全体のページ要素で使用されるスタイルを更新します。][customize-styles]
* [ポータルで生成されるページを使用するテンプレートを変更][ portal-templates] (例: API ドキュメント、製品、ユーザーの認証など)

## <a name="page-structure"></a>開発者ポータル ページの構造

開発者ポータルは、コンテンツ管理システムに基づいています。 ウィジェットと呼ばれる小さなページ要素のセットに基づいて、すべてのページのレイアウトがビルドします。

![開発者ポータル ページの構造体][api-management-customization-widget-structure]

すべてのウィジェットは編集可能です。 
* ページごとに固有のコア コンテンツは、「コンテンツ」ウィジェット内に存在します。 ページを編集には、このウィジェットの内容を編集することを意味します。
* 残りのウィジェットでは、すべてのページ レイアウト要素が含まれています。 このウィジェットに加えられた変更は、すべてのページに適用されます。 これらが参照されます「レイアウト ウィジェット」として。

日常的なページで通常 1 つを編集のみを変更し、コンテンツ ウィジェット ページごとに異なるコンテンツが表示されます。

## <a name="modify-layout-widget"></a>レイアウト ウィジェットの内容を変更します。

これは、Azure ポータルからアクセス可能なパブリッシャー ポータルを使用して、開発者ポータル内のコンテンツが変更されます。 到達できないように、をクリックして**パブリッシャー ポータル**API Management インスタンスのサービスのツールバーからです。

![パブリッシャー ポータル][api-management-management-console]

そのウィジェットの内容を編集する をクリックして**ウィジェット**から、**開発者ポータル** をクリックします。 この例では、ヘッダー ウィジェットの内容を変更することができます。 選択、**ヘッダー**一覧からウィジェット。

![ウィジェットのヘッダー][api-management-widgets-header]

ヘッダーの内容は内から編集、**本文**フィールドです。 必要に応じてテキストを変更し、クリックして**保存**ページの下部にあります。

これで、開発者ポータル内の各ページで、新しいヘッダーを表示することができます。

> パブリッシャー ポータルで、開発者ポータルを開くにはクリックして**開発者ポータル**上部のバーにします。
> 
> 

## <a name="edit-page-contents"></a>ページの内容を編集

コンテンツのページで、すべての既存の一覧を表示する**コンテンツ**から、**開発者ポータル**パブリッシャー ポータルのメニュー。

![コンテンツを管理します。][api-management-customization-manage-content]

クリックして、**ようこそ**開発者ポータルのホーム ページに表示される内容を編集するページ。 必要に応じて、それらをプレビューしてクリックし、、変更を加える**今すぐ発行**すべてのユーザーに表示されるようにします。

> ホーム ページでは、上部にバナーを表示することを許可する特殊なレイアウトを使用します。 このバナーはから編集できません、**コンテンツ**セクションです。 このバナーを編集する] をクリックして**ウィジェット**から、**開発者ポータル**メニューの [**ホーム ページ**から、**現在レイヤー**ドロップダウン リストを開き、**バナー**項目の下にある、**セクションの機能を備えた**です。 このウィジェットの内容は、他のページと同じように編集できます。
> 
> 

## <a name="next-steps"></a>次の手順
* [開発者ポータル全体のページ要素で使用されるスタイルを更新します。][customize-styles]
* [ポータルで生成されるページを使用するテンプレートを変更][ portal-templates] (例: API ドキュメント、製品、ユーザーの認証など)

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
