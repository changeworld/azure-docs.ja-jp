---
title: "Azure CLI を使用して DNS レコードを作成する | Microsoft Docs"
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>Azure CLI を使用して DNS レコードを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

この記事では、Azure CLI を使用してレコードとレコード セットを作成する手順を説明します。

## <a name="introduction"></a>はじめに

Azure DNS で DNS レコードを作成する前に、まず、Azure DNS における DNS レコード セットでの DNS レコードの編成方法を理解する必要があります。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS における DNS レコードの詳細については、「[DNS ゾーンとレコード](dns-zones-records.md)」を参照してください。

## <a name="create-a-record-set-and-record"></a>レコード セットとレコードの作成

このセクションでは、Azure DNS に DNS レコードを作成する方法について説明します。 これらの例では、[Azure CLI のインストール、サインイン、DNS ゾーンの作成](dns-getstarted-create-dnszone-cli.md)が既に完了していることを前提としています。

このページのすべての例では、DNS レコードの種類として "A" を使用します。 その他のレコードの種類の説明および DNS レコードとレコード セットの管理方法の詳細については、[Azure CLI を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-cli.md)に関する記事を参照してください。

## <a name="create-a-dns-record"></a>DNS レコードの作成

DNS レコードを作成するには、`azure network dns record-set add-record` コマンドを使用します。 `azure network dns record-set add-record -h` を使用すると、ヘルプが表示されます。

レコードの作成時に、リソース グループ名、ゾーン名、レコード セット名、レコードの種類、および作成するレコードの詳細を指定する必要があります。

レコード セットがまだ存在していない場合は、このコマンドによって作成されます。 レコード セットが既に存在する場合、このコマンドは、指定されたレコードを既存のレコード セットに追加します。 

新しいレコード セットが作成される場合は、既定の Time-to-Live (TTL) である 3600 が使用されます。 別の TTL を使用するための手順については、[Azure CLI を使用した Azure DNS の DNS レコードの管理](dns-operations-recordsets-cli.md)に関するページを参照してください。

次の例では、リソース グループ *MyResourceGroup* のゾーン *contoso.com* に *www* という A レコードを作成します。 A レコードの IP アドレスは、*1.2.3.4* です。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

ゾーンの頂点 (この例では "contoso.com") にレコード セットを作成するには、レコード名 "@", (引用符を含みます) を使用します。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

レコード データを指定するためのパラメーターは、レコードの種類によって異なります。 たとえば、種類 "A" のレコードの場合は、パラメーター `-a <IPv4 address>` に IPv4 アドレスを指定します。 他のレコードの種類のパラメーターを一覧表示するには、`azure network dns record-set add-record -h` を使用します。 レコードの種類ごとの例については、[Azure CLI を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-cli.md)に関するページを参照してください。


## <a name="verify-name-resolution"></a>名前解決の確認

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




<!--HONumber=Dec16_HO3-->


