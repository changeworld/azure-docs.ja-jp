---
title: クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium レベル | Microsoft Docs
description: Azure CDN クエリ文字列のキャッシュにより、Web 要求にクエリ文字列が含まれる場合のファイルのキャッシュ方法を制御します。 この記事では、Azure CDN Premium from Verizon 製品でのクエリ文字列のキャッシュについて説明します。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mazha
ms.openlocfilehash: 87845df92c77ace484a7afdde3ee20b570cf9cbb
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2018
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium レベル
> [!div class="op_single_selector"]
> * [Standard レベル](cdn-query-string.md)
> * [Premium レベル](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概要
クエリ文字列のキャッシュでは、Azure Content Delivery Network (CDN) が、クエリ文字列を含む Web 要求に対するファイルのキャッシュ方法を制御します。 クエリ文字列を含む Web 要求で、クエリ文字列は要求の疑問符 (?) の後に指定されます。 クエリ文字列には、フィールド名とその値を等号 (=) で区切って指定される、キーと値のペア (複数可) を含めることができます。 キーと値のペアはそれぞれ、アンパサンド (&) で区切られます。 たとえば、http:\//www.contoso.com/content.mov?field1=value1&field2=value2 のようになります。 要求のクエリ文字列にキーと値のペアを複数指定する場合、どのような順序で指定してもかまいません。 

> [!NOTE]
> Azure CDN の Standard および Premium 製品では同じクエリ文字列キャッシュ機能が提供されますが、ユーザー インターフェイスは異なります。  ここでは、**Azure CDN Premium from Verizon** のインターフェイスについて説明します。 **Azure CDN Standard from Akamai** および **Azure CDN Standard from Verizon** でのクエリ文字列のキャッシュについては、「[クエリ文字列による Azure CDN キャッシュ動作の制御 - Standard レベル](cdn-query-string.md)」をご覧ください。
>


次の 3 つのクエリ文字列モードを使用できます。

- **standard-cache**: 既定のモードです。 このモードでは、CDN Point-of-Presence (POP) ノードは、クエリ文字列を要求元から最初の要求の配信元サーバーに渡して、資産をキャッシュします。 POP サーバーから提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。

    >[!IMPORTANT] 
    > このアカウントのいずれかのパスでトークン認証が有効になっている場合は、standard-cache モードだけを使うことができます。 

- **no-cache**: このモードでは、クエリ文字列のある要求は CDN POP ノードでキャッシュされません。 POP ノードは配信元サーバーから直接資産を取得し、それを各要求により要求元に渡します。

- **unique-cache**: このモードでは、クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。 たとえば、`example.ashx?q=test1` の要求の配信元サーバーからの応答は POP ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。 `example.ashx?q=test2` の要求は、独自の有効期限設定を持つ個別の資産としてキャッシュされます。
   
    セッション ID やユーザー名など、要求ごとに変わるパラメーターがクエリ文字列に含まれる場合は、キャッシュ ヒット率が低くなるので、このモードを使わないでください。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Premium CDN プロファイル用にクエリ文字列のキャッシュ設定を変更する
1. CDN プロファイルを開き、**[管理]** をクリックします。
   
    ![[CDN プロファイル] の [管理] ボタン](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN 管理ポータルが開きます。
2. **[HTTP Large]\(HTTP ラージ\)** タブ、**[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。 **[クエリ文字列のキャッシュ]** をクリックします。
   
    クエリ文字列のキャッシュ オプションが表示されます。
   
    ![CDN クエリ文字列のキャッシュ オプション](./media/cdn-query-string-premium/cdn-query-string.png)
3. クエリ文字列モードを選択し、**[更新]** をクリックします。

> [!IMPORTANT]
> 設定の変更が CDN に反映されるまでに時間がかかるので、変更がすぐに表示されないことがあります。 **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 90 分以内で完了します。
 

