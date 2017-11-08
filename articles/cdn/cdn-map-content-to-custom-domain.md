---
title: "カスタム ドメインを CDN エンドポイントに追加する | Microsoft Docs"
description: "Azure CDN コンテンツをカスタム ドメインにマッピングする方法について説明します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: 8c5dd3ddd03b3531e4ffb7b622110a2ea997f9ae
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>カスタム ドメインを CDN エンドポイントに追加する
プロファイルを作成したら、HTTP または HTTPS を使ってコンテンツを配信するために、通常 1 つ以上の CDN エンドポイント (azureedge.net のサブドメイン) も作成します。 既定では、このエンドポイントはすべての URL (たとえば、`http(s)://contoso.azureedge.net/photo.png`) に含まれます。 便宜上、Azure CDN はカスタム ドメイン (たとえば、`www.contoso.com`) をエンドポイントと関連付けるためのオプションを提供します。 このオプションでは、コンテンツを配信するためにエンドポイントではなくカスタム ドメインを使用します。 このオプションは、たとえば、ブランド化のために独自のドメイン名を顧客に表示する必要がある場合などに便利です。

カスタム ドメインがまだない場合は、最初にカスタム ドメインをドメイン プロバイダーから購入する必要があります。 カスタム ドメインを取得した後は、次の手順に従います。
1. [ドメイン プロバイダーの DNS レコードにアクセスする](#step-1-access-dns-records-by-using-your-domain-provider)
2. [CNAME DNS レコードを作成する](#step-2-create-the-cname-dns-records)
    - オプション 1: カスタム ドメインを CDN エンドポイントに直接マップする
    - オプション 2: cdnverify を使用してカスタム ドメインを CDN エンドポイントにマップする 
3. [Azure で CNAME レコード マッピングを有効にする](#step-3-enable-the-cname-record-mapping-in-azure)
4. [カスタム サブドメインが CDN エンドポイントを参照することを確認する](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(依存的な手順) 永続的なカスタム ドメインを CDN エンドポイントにマップする](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>手順 1: ドメイン プロバイダーを使用して DNS レコードにアクセスする

[DNS ドメイン](https://docs.microsoft.com/en-us/azure/dns/dns-overview)をホストするために Azure を使用している場合、ドメイン プロバイダーの DNS を Azure DNS に委任する必要があります。 詳細については、「[Azure DNS へのドメインの委任](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)」を参照してください。

または、ドメイン プロバイダーを使用して DNS ドメインを処理する場合、ドメイン プロバイダーの Web サイトにサインインしてください。 プロバイダーの資料を調べるか、**[ドメイン名]**、**[DNS]**、または**[ネームサーバー管理]** という名前のつけられた Web サイトの領域を探して、DNS レコードを管理するためのページを見つけます。 通常、DNS レコードのページを見つけるには、アカウント情報を表示し、**[ドメイン]** などのリンクを探します。 プロバイダーによっては、追加するレコードのタイプごとに異なるリンクが用意されています。

> [!NOTE]
> 一部のプロバイダー (GoDaddy など) では、別の **[変更を保存]** リンクを選択するまで DNS レコードの変更が反映されません。 


## <a name="step-2-create-the-cname-dns-records"></a>手順 2: CNAME DNS レコードを作成する

カスタム ドメインを Azure CDN エンドポイントと共に使用するためには、まずドメイン プロバイダーで Canonical Name (CNAME) レコードを作成する必要があります。 CNAME レコードは、本物の ("canonical"な) ドメイン名に対して別名を指定することによってソース ドメインを宛先ドメインにマップする、ドメイン ネーム システム (DNS) 内のレコードの種類です。 Azure CDN では、ソース ドメインはカスタム ドメイン (およびサブドメイン) で、宛先ドメインは CDN エンドポイントです。 ポータルまたは API からエンドポイントにカスタム ドメインを追加すると、Azure CDN は CNAME DNS レコードを確認します。 

CNAME レコードは、`www.contoso.com` や `cdn.contoso.com` のような特定のドメインやサブドメインをマップします。CNAME レコードを `contoso.com` などのルート ドメインにマップすることはできません。 サブドメインはただ 1 つの CDN エンドポイントに関連付けることができます。CNAME レコードを作成することで、サブドメイン宛てのすべてのトラフィックが指定されたエンドポイントにルーティングされます。 たとえば、`www.contoso.com` を CDN エンドポイントに関連付けた場合、ストレージ アカウント エンドポイントやクラウド サービス エンドポイントなど、他の Azure エンドポイントに関連付けることはできません。 ただし、同じドメインの別のサブドメインを別のサービス エンドポイントに使用することはできます。 また、複数のサブドメインを同じ CDN エンドポイントにマップすることもできます。

次の方法のいずれかを使用して、カスタム ドメインを CDN エンドポイントにマップします。

- 方法 1: 直接マップする。 カスタム ドメインで運用環境のトラフィックが実行されていない場合は、カスタム ドメインを直接 CDN エンドポイントにマップすることができます。 カスタム ドメインを CDN エンドポイントにマップする処理を行うと、Azure Portal でのドメインの登録中にドメインが短時間ダウンする場合があります。 CNAME マッピング エントリは、次の形式である必要があります。 
 
  | 名前             | タイプ  | 値                  |
  |------------------|-------|------------------------|
  | www\.consoto.com | CNAME | consoto\.azureedge.net |


- 方法 2: **cdnverify** サブドメインでマップする。 カスタム ドメインで中断することのできない運用環境のトラフィックが実行されている場合は、CDN エンドポイントに一時的な CNAME マッピングを作成することができます。 この方法では、Azure **cdnverify** サブドメインを使用して中間的な登録手順を指定し、DNS マッピング中にも中断することなくユーザーがドメインにアクセスできるようにします。

   1. 新しい CNAME レコードを作成し、**cdnverify** サブドメインを含むサブドメインの別名を指定します。 たとえば、**cdnverify.www** または **cdnverify.cdn** です。 
   2. 次の形式で、CDN エンドポイントであるホスト名を指定します: `cdnverify.<EndpointName>.azureedge.net`。 CNAME マッピング エントリは、次の形式である必要があります。 

   | 名前                       | タイプ  | 値                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.consoto.com | CNAME | cdnverify.consoto\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>手順 3: Azure で CNAME レコード マッピングを有効にする

前の手順のいずれかを使用してカスタム ドメインを登録した後、Azure CDN でカスタム ドメインの機能を有効にすることができます。 

1. [Azure Portal](https://portal.azure.com/) にログインして、カスタム ドメインにマップするエンドポイントを含む CDN プロファイルを参照します。  
2. **[CDN プロファイル]** ブレードで、サブドメインを関連付ける CDN エンドポイントを選択します。
3. エンドポイント ブレードの左上にある**[カスタム ドメイン]** をクリックします。 

   ![[カスタム ドメイン] ボタン](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. **[カスタム ホスト名]** テキスト ボックスで、サブドメインを含むカスタム ドメインを入力します。 たとえば、`www.contoso.com` または `cdn.contoso.com` です。

   ![[カスタム ドメイン] ダイアログの追加](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. **[追加]**をクリックします。

   入力したドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。 CNAME レコードがネーム サーバーに伝播するまでしばらく時間がかかる場合があります。 ドメインがすぐに認証されない場合は、CNAME レコードが正しいことを確認して、数分間待ってからやり直してください。 **Azure CDN from Verizon** (Standard と Premium) エンドポイントの場合、カスタム ドメインの設定がすべての CDN エッジ ノードに反映されるまでに最大 90 分かかることがあります。  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>手順 4: カスタム サブドメインが CDN エンドポイントを参照することを確認する

カスタム ドメインの登録を完了した後は、カスタム サブドメインが CDN エンドポイントを参照することを確認してください。
 
1. エンドポイントにキャッシュされているパブリック コンテンツがあることを確認します。 たとえば、CDN エンドポイントがストレージ アカウントに関連付けられている場合は、CDN はパブリック BLOB コンテナーにコンテンツをキャッシュします。 カスタム ドメインをテストするには、コンテナーでパブリック アクセスが許可され、少なくとも 1 つの BLOB が格納されていることを確認します。

2. ブラウザーで、カスタム ドメインを使用して BLOB のアドレスに移動します。 たとえば、カスタム ドメインが `cdn.contoso.com` である場合、キャッシュされた BLOB の URL は次のようになります: `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`。


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>手順 5 (依存的な手順): 永続的なカスタム ドメインを CDN エンドポイントにマップする

この手順は手順 2 の方法 2 (**cdnverify** サブドメインでマップする) に依存します。 一時的な **cdnverify** サブドメインを使用していてその動作を確認済みである場合、永続的なカスタム ドメインを CDN エンドポイントにマップすることができます。

1. ドメイン プロバイダーの Web サイトで、永続的なカスタム ドメインを CDN エンドポイントにマップする CNAME DNS レコードを作成します。 CNAME マッピング エントリは、次の形式である必要があります。 
 
   | 名前             | タイプ  | 値                  |
   |------------------|-------|------------------------|
   | www\.consoto.com | CNAME | consoto\.azureedge.net |
2. 以前に作成した **cdnverify** サブドメインの CNAME レコードを削除します。

## <a name="see-also"></a>関連項目
[Azure の Content Delivery Network (CDN) を有効にする方法](cdn-create-new-endpoint.md)  
[Azure DNS へのドメインの委任](../dns/dns-domain-delegation.md)