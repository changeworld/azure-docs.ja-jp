---
title: "CLI を使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Docs"
description: "Azure DNS のホスト レコードを作成する方法。CLI を使用したレコード セットとレコードの設定"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: e377f176fe24a8e7e42d409f86d6b0093ce5e7c4

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>CLI を使用した DNS レコード セットとレコードの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

この記事では、CLI を使用してレコードとレコード セットを作成する手順を説明します。 そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

このセクションでは、Azure DNS に DNS レコードを作成する方法について説明します。 これらの例では、[Azure CLI のインストール、サインイン、DNS ゾーンの作成](dns-getstarted-create-dnszone-cli.md)が既に完了していることを前提としています。

このページのすべての例では、DNS レコードの種類として "A" を使用します。 その他のレコードの種類の説明および DNS レコードとレコード セットの管理方法の詳細については、[Azure CLI を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-cli.md)に関する記事を参照してください。

## <a name="create-a-record-set-and-record"></a>レコード セットとレコードの作成

このセクションでは、レコード セットとレコードの作成方法を説明します。 この例では、DNS ゾーン "contoso.com" に相対名 "www" を持つレコード セットを作成します。 レコードの完全修飾名は、"www.contoso.com" になります。 レコードの種類は "A" で、Time to Live (TTL) は 60 秒です。 この手順を完了すると、空のレコード セットが作成されます。

ゾーンの頂点 (この例では "contoso.com") にレコード セットを作成するには、レコード名 "@", (引用符を含みます) を使用します。 これは、一般的な DNS の規則です。

### <a name="1-create-a-record-set"></a>1.レコード セットの作成

新しいレコードの名前と種類が既存のレコードと同じ場合は、そのレコードを既存のレコード セットに追加する必要があります。 この手順を省略して、次の「[レコードの追加](#add-records)」に進んでください。 また、新しいレコードの名前と種類が既存のすべてのレコードと異なる場合は、新しいレコード セットを作成する必要があります。

レコード セットは、`azure network dns record-set create` コマンドを使用して作成します。 `azure network dns record-set create -h` を使用すると、ヘルプが表示されます。  

レコード セットを作成するとき、レコード セット名、ゾーン、time to live (TTL)、レコードの種類を指定する必要があります。 

```azurecli
azure network dns record-set create myresourcegroup contoso.com www A 60
```

この手順を完了すると、空の "www" レコード セットが作成されます。 新しく作成した "www" レコード セットを使用するには、最初にレコードを追加する必要があります。

### <a name="2-add-records"></a>2.レコードの追加

`azure network dns record-set add-record`を使用して、レコードをレコード セットに追加します。 `azure network dns record-set add-record -h` を使用すると、ヘルプが表示されます。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。 たとえば、"A" という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、`-a <IPv4 address>` パラメーターのみです。 他のレコードの種類のパラメーターを一覧表示するには、`azure network dns record-set add-record -h` を使用します。

次のコマンドを使用して、上記の手順で作成した "www" レコード セットに A レコードを追加できます。

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 1.2.3.4
```

### <a name="verify-name-resolution"></a>名前解決の確認

Azure DNS ネーム サーバーに DNS レコードが存在することをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、[ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する](dns-getstarted-create-dnszone.md#test-name-servers)必要があります。 次のコマンドを実際のレコード ゾーンの正しい値に置き換えてください。

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>次のステップ

[ドメイン名を Azure DNS ネーム サーバーに委任する](dns-domain-delegation.md)方法を確認します。

[Azure CLI を使用して DNS ゾーンを管理する](dns-operations-dnszones-cli.md)方法を確認します。

[Azure CLI を使用して DNS レコードとレコード セットを管理する](dns-operations-recordsets-cli.md)方法を確認します。




<!--HONumber=Dec16_HO2-->


