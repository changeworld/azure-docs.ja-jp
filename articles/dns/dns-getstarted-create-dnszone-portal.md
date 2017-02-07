---
title: "Azure Portal で DNS ゾーンを作成および管理する方法 | Microsoft Docs"
description: "Azure DNS に DNS ゾーンを作成する方法について説明します。 Azure Portal を使用して最初の DNS ゾーンを作成し管理するためのステップ バイ ステップ ガイドです。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: c9bebc8f54d3f732b3014f6885ee65a067e9d1d8

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Azure ポータルで DNS ゾーンを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

この記事では、Azure Portal を使用して DNS ゾーンを作成する手順を説明します。 DNS ゾーンは、PowerShell または CLI を使用して作成することもできます。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure ポータルにサインインします。
2. ハブ メニューの **[新規]、[ネットワーク]** を順にクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーンの作成] ブレードを開きます。

    ![[DNS ゾーン]](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. **[DNS ゾーンの作成]** ブレードで、DNS ゾーンの名前を入力します。 たとえば、 *contoso.com*などのドメインです。
 
    ![ゾーンの作成](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. 次に、使用するリソース グループを指定します。 新しいリソース グループを作成することも、既存のリソース グループを選択することもできます。 新しいリソース グループを作成する場合は、**[場所]** ドロップダウンを使用して、リソース グループの場所を指定します。 これはリソース グループの場所を指定する設定であり、DNS ゾーンには影響しないことにご注意ください。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

6. ダッシュボードで新しいゾーンをすぐに見つけられるようにするには、 **[ダッシュボードにピン留めする]** チェックボックスをオンのままにします。 **[Create]**をクリックします。

    ![[ダッシュボードにピン留めする]](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. [作成] をクリックした後、ダッシュボードを見ると、新しいゾーンが構成されていることがわかります。

    ![作成](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. 新しいゾーンが作成されると、ダッシュボードに新しいゾーンのブレードが開きます。

## <a name="view-records"></a>レコードの表示

DNS ゾーンを作成すると、次のレコードも作成されます。

* "Start of Authority" (SOA) レコード。 SOA レコードは、すべての DNS ゾーンのルートに存在します。
* 権威ネーム サーバー (NS) レコード。 このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。 Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。 詳細については、「 [Azure DNS へのドメインの委任](dns-domain-delegation.md) 」を参照してください。

[DNS ゾーン] ブレードの下部に、DNS ゾーンのレコード セットが表示されます。

![ゾーン](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>ネーム サーバーのテスト

Azure DNS ネーム サーバーに DNS ゾーンが存在することをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの&1; つに DNS クエリを直接送信する必要があります。 ゾーンのネーム サーバーは、Azure Portal で次のように示されます。
    
![ゾーン](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

次のコマンドを実際のゾーンの正しいネーム サーバーに置き換えてください。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>次のステップ

DNS ゾーンを作成したら、[レコード セットとレコードを作成](dns-getstarted-create-recordset-portal.md)し、インターネット ドメインの DNS レコードを作成します。




<!--HONumber=Dec16_HO2-->


