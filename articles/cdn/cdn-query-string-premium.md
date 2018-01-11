---
title: "クエリ文字列による Azure CDN キャッシュ動作の制御 - Premium | Microsoft Docs"
description: "Azure CDN クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 2021b5b7602605a7c264e9cd575399077691da34
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>クエリ文字列による Azure コンテンツ配信ネットワーク キャッシュ動作の制御 - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium from Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>概要
Azure コンテンツ配信ネットワーク (CDN) を使用すると、クエリ文字列を含む Web 要求のためにファイルをキャッシュする方法を制御できます。 クエリ文字列を含む Web 要求で、クエリ文字列は要求の疑問符 (?) の後に指定されます。 クエリ文字列には、フィールド名とその値を等号 (=) で区切って指定される、キーと値のペア (複数可) を含めることができます。 キーと値のペアはそれぞれ、アンパサンド (&) で区切られます。 たとえば、「 `http://www.contoso.com/content.mov?field1=value1&field2=value2`」のように指定します。 要求のクエリ文字列にキーと値のペアを複数指定する場合、どのような順序で指定してもかまいません。 

> [!IMPORTANT]
> Standard および Premium CDN 製品では同じクエリ文字列キャッシュ機能が提供されますが、ユーザー インターフェイスは異なります。  ここでは、**Azure CDN Premium from Verizon** のインターフェイスについて説明します。 **Azure CDN Standard from Akamai** と **Azure CDN Standard from Verizon** のクエリ文字列のキャッシュについては、「[クエリ文字列による CDN 要求のキャッシュ動作の制御](cdn-query-string.md)」を参照してください。
>

次の 3 つのクエリ文字列モードを使用できます。

- **standard-cache**: 既定のモードです。 このモードでは、CDN エッジ ノードは、クエリ文字列を要求元から最初の要求の配信元に渡して、資産をキャッシュします。 エッジ ノードから提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。
- **no-cache**: このモードでは、クエリ文字列のある要求は CDN エッジ ノードでキャッシュされません。 エッジ ノードは配信元から直接資産を取得し、それを各要求により要求元に渡します。
- **unique-cache**: このモードでは、クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。 たとえば、`example.ashx?q=test1` の要求の配信元からの応答はエッジ ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。 `example.ashx?q=test2` の要求は、独自の有効期限設定を持つ個別の資産としてキャッシュされます。

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Premium CDN プロファイル用にクエリ文字列のキャッシュ設定を変更する
1. CDN プロファイルを開き、**[管理]** をクリックします。
   
    ![[CDN プロファイル] の [管理] ボタン](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN 管理ポータルが開きます。
2. **[HTTP Large]\(HTTP ラージ\)** タブ、**[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。 **[クエリ文字列のキャッシュ]** をクリックします。
   
    クエリ文字列のキャッシュ オプションが表示されます。
   
    ![CDN クエリ文字列のキャッシュ オプション](./media/cdn-query-string-premium/cdn-query-string.png)
3. クエリ文字列モードを選択し、**[更新]** をクリックします。

> [!IMPORTANT]
> 設定の変更が CDN に反映されるまでに時間がかかるので、変更がすぐに表示されないことがあります。 **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 90 分以内に完了しますが、もっと時間がかかる場合もあります。
 

