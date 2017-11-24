---
title: "クエリ文字列による Azure CDN キャッシュ動作の制御 | Microsoft Docs"
description: "Azure CDN クエリ文字列のキャッシュにより、ファイルにクエリ文字列が含まれている場合のファイルのキャッシュ方法を制御します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 28e724f34c32edb0d5641b24f9ffedb7dc5f9680
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>クエリ文字列による Azure コンテンツ配信ネットワーク キャッシュ動作の制御
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Azure CDN Premium from Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>概要
Azure コンテンツ配信ネットワーク (CDN) を使用すると、クエリ文字列を含む Web 要求のためにファイルをキャッシュする方法を制御できます。 クエリ文字列を含む Web 要求で、クエリ文字列は要求の `?` 文字の後に指定されます。 クエリ文字列には 1 つ以上のパラメーターを含むことができます (複数のパラメーターは `&` 文字で区切られます)。 たとえば、「 `http://www.domain.com/content.mov?data1=true&data2=false`」のように入力します。 1 つの要求に複数のクエリ文字列パラメーターがある場合、パラメーターの順序は関係がありません。 

> [!IMPORTANT]
> Standard および Premium CDN 製品では同じクエリ文字列キャッシュ機能が提供されますが、ユーザー インターフェイスは異なります。  ここでは、**Azure CDN Standard from Akamai** と **Azure CDN Standard from Verizon** のインターフェイスについて説明します。 **Azure CDN Premium from Verizon**でのクエリ文字列のキャッシュについては、「 [クエリ文字列による CDN 要求のキャッシュ動作の制御 - Premium](cdn-query-string-premium.md)」を参照してください。

次の 3 つのクエリ文字列モードを使用できます。

- **クエリ文字列を無視する**: 既定のモードです。 このモードでは、CDN エッジ ノードは、クエリ文字列を要求元から最初の要求の配信元に渡して、資産をキャッシュします。 エッジ ノードから提供される資産の後続の要求はすべて、キャッシュされた資産の有効期限が切れるまで、クエリ文字列を無視します。
- **クエリ文字列のキャッシュをバイパス**: このモードでは、クエリ文字列のある要求は CDN エッジ ノードでキャッシュされません。 エッジ ノードは配信元から直接資産を取得し、それを各要求により要求元に渡します。
- **一意の URL をすべてキャッシュ**: このモードでは、クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。 たとえば、`example.ashx?q=test1` の要求の配信元からの応答はエッジ ノードでキャッシュされ、後続のキャッシュではその同じクエリ文字列により返されます。 `example.ashx?q=test2` の要求は、独自の有効期限設定を持つ個別の資産としてキャッシュされます。

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Standard CDN プロファイル用にクエリ文字列のキャッシュ設定を変更する
1. CDN プロファイルを開き、管理する CDN エンドポイントを選択します。
   
   ![CDN プロファイル エンドポイント](./media/cdn-query-string/cdn-endpoints.png)
   
2. [設定] の **[キャッシュ]** をクリックします。
   
    ![CDN プロファイルの [キャッシュ] ボタン](./media/cdn-query-string/cdn-cache-btn.png)
   
3. **[クエリ文字列のキャッシュ動作]** 一覧でクエリ文字列モードを選択し、**[保存]** をクリックします。
   
  <!--- Replace screen shot after general caching goes live ![CDN query string caching options](./media/cdn-query-string/cdn-query-string.png) --->

> [!IMPORTANT]
> 設定の変更が CDN に反映されるまでに時間がかかるので、変更がすぐに表示されないことがあります。 **Azure CDN from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 **Azure CDN from Verizon** プロファイルの場合、通常、反映は 90 分以内に完了しますが、もっと時間がかかる場合もあります。


