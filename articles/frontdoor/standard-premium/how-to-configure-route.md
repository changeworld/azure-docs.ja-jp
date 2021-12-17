---
title: Azure Front Door のルートを構成する
description: この記事では、ドメインと配信元グループ間のルートを構成する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 05/17/2021
ms.author: qixwang
ms.openlocfilehash: a9f0095ebd82ab82003c03c1ca9d59f70d908c9f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006111"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Azure Front Door Standard/Premium のルートを構成する

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

この記事では、既存のエンドポイントでの Azure Front Door (AFD) のルートの作成に使用される各設定について説明します。 カスタム ドメインと配信元を既存の Azure Front Door エンドポイントに追加したら、ルートを構成して、ドメインと配信元間でトラフィックをルーティングするための、それらの間の関連付けを定義する必要があります。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

Azure Front Door のルートを構成するには、事前に、現在のエンドポイント内に少なくとも 1 つの配信元グループと 1 つのカスタム ドメインを作成しておく必要があります。 

配信元グループを設定するには、[新しい Azure Front Door Standard/Premium 配信元グループの作成](how-to-create-origin.md)に関するページを参照してください。 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Azure Front Door Standard/Premium の新しいルートを作成する

1. [Azure portal](https://portal.azure.com) サインインし、Azure Front Door Standard/Premium のプロファイルに移動します。

1. **[設定]** で **[エンドポイント マネージャー]** を選択します。
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Front Door エンドポイント マネージャーの設定のスクリーンショット。" lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. 次に、ルートを構成するエンドポイントで **[エンドポイントの編集]** を選択します。
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="[エンドポイントの編集] を選択したスクリーンショット。":::

1. **[エンドポイントの編集]** ページが表示されます。 ルートで **[+ 追加]** を選択します。
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="[エンドポイントの編集] ページのルートの追加のスクリーンショット。":::    
    
1. **[ルートの追加]** ページで次の情報を入力または選択します。

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="[ルートの追加] ページのスクリーンショット。" lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | 設定 | 説明 |
    | --- | --- |
    | 名前 | 新しいルートの一意の名前を入力します。 |   
    | Domain| 検証済みで、別のルートに関連付けられていないドメインを 1 つ以上選択します。 |
    | 一致するパターン  | このルートで受け入れるすべての URL パスのパターンを構成します。 たとえば、これを `/images/*` に設定すると、URL `www.contoso.com/images/*` ですべての要求を受け入れることができます。 AFD では、まず完全一致に基づいてトラフィックの特定を試みます。完全一致のパスがない場合は、一致するワイルドカード パスを探します。 Path が一致するルーティング規則が存在しない場合、要求を拒否し、400: Bad Request エラー HTTP 応答を返します。 パスに一致するパターンでは大文字と小文字が区別されません。つまり、大文字と小文字が異なるパスは重複として扱われます。 たとえば、パスが `/FOO` および `/foo` で同じプロトコルを使用する同じホストがあるとします。 これらのパスは重複と見なされ、[一致するパターン] 設定では許可されません。 |
    | 受け入れ済みのプロトコル | クライアントが要求を行う際に、Azure Front Door で受け入れるプロトコルを指定します。 |
    | リダイレクト | HTTP 要求で受信要求に HTTPS を適用するかどうかを指定します。 |
    | 配信元グループ | 配信元に戻る要求が発生したときに、どの配信元グループに転送するかを選択します。 |
    | 配信元のパス | このパスは、配信元に転送される要求を構築する際に Azure Front Door で使用される URL を書き換えるために使用されます。 既定では、このパスは指定されていません。 そのため、Azure Front Door から配信元への要求内では、受信 URL パスが使用されます。 また、ワイルドカード パスを指定することもできます。これにより、受信パスの一致する部分が配信元への要求パスにコピーされます。 </br></br>一致させるパターン: `/foo/*`</br>配信元のパス: `/fwd/`</br></br>受信 URL パス: `/foo/a/b/c/`</br>Azure Front Door から配信元への URL: `fwd/a/b/c`。 |
    | 転送プロトコル | 要求の転送に使用するプロトコルを選択します。 |
    | キャッシュ | Azure Front Door で静的コンテンツのキャッシュを有効にするには、このオプションを選択します。 |
    | ルール | このルートに適用されるルール セットを選択します。 ルールを構成する方法の詳細については、[Azure Front Door のルールセットの構成](how-to-configure-rule-set.md)に関する記事を参照してください。 | 

1. **[追加]** を選択して、新しいルートを作成します。 ルートは、エンドポイントのルートの一覧に表示されます。
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="ルートの一覧のスクリーンショット。":::  
    
## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったルートを削除するには、ルートを選択してから、 **[削除]** を選択します。 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="ルートを削除する方法のスクリーンショット。":::  

## <a name="next-steps"></a>次のステップ
カスタム ドメインについて学習するには、Azure Front Door エンドポイントにカスタム ドメインを追加するためのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインの追加]()
