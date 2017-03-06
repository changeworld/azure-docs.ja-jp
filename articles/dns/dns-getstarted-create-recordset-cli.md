---
title: "Azure CLI 2.0 を使用した DNS レコードの作成 | Microsoft Docs"
description: "Azure DNS のホスト レコードを作成する方法。Azure CLI 2.0 を使用したレコード セットとレコードの設定"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: ca713647a9f795d7803c32eac7fba9b5b6ac95cf
ms.lasthandoff: 03/01/2017

---

# <a name="how-to-create-dns-records-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用して DNS レコードを作成する方法

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

この記事では、Azure CLI を使用してレコードとレコード セットを作成する手順を説明します。

## <a name="introduction"></a>はじめに

Azure DNS で DNS レコードを作成する前に、まず、Azure DNS における DNS レコード セットでの DNS レコードの編成方法を理解する必要があります。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS における DNS レコードの詳細については、「[DNS ゾーンとレコード](dns-zones-records.md)」を参照してください。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) - クラシック デプロイ モデルと Resource Manager デプロイ モデル用の CLI。
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) - Resource Manager デプロイ モデル用の次世代 CLI。

## <a name="create-a-record-set-and-record"></a>レコード セットとレコードの作成

このセクションでは、Azure DNS に DNS レコードを作成する方法について説明します。 ここで示す例では、[Azure CLI 2.0 のインストール、サインイン、DNS ゾーンの作成](dns-getstarted-create-dnszone-cli.md)が既に完了していることを前提としています。

このページのすべての例では、DNS レコードの種類として "A" を使用します。 その他のレコードの種類の説明および DNS レコードとレコード セットの管理方法の詳細については、[Azure CLI 2.0 を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-cli.md)に関するページをご覧ください。

## <a name="create-a-dns-record"></a>DNS レコードの作成

DNS レコードを作成するには、`az network dns record-set ? add` コマンドを使用します (?  はレコードの種類です)。 `az network dns record-set --help` を使用すると、ヘルプが表示されます。

レコードの作成時に、リソース グループ名、ゾーン名、レコード セット名、レコードの種類、および作成するレコードの詳細を指定する必要があります。

レコード セットがまだ存在していない場合は、このコマンドによって作成されます。 レコード セットが既に存在する場合、このコマンドは、指定されたレコードを既存のレコード セットに追加します。 

新しいレコード セットが作成される場合は、既定の Time-to-Live (TTL) である 3600 が使用されます。 別の TTL を使用するための手順については、[Azure CLI 2.0 を使用した Azure DNS の DNS レコードの管理](dns-operations-recordsets-cli.md)に関するページをご覧ください。

次の例では、リソース グループ *MyResourceGroup* のゾーン *contoso.com* に *www* という A レコードを作成します。 A レコードの IP アドレスは、*1.2.3.4* です。

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

ゾーンの頂点 (この場合は "contoso.com") にレコード セットを作成するには、レコード名 "@" (引用符を含みます) を使用します。

```azurecli
az network dns record-set a --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

レコード データを指定するためのパラメーターは、レコードの種類によって異なります。 たとえば、種類 "A" のレコードの場合は、パラメーター `--ipv4-address <IPv4 address>` に IPv4 アドレスを指定します。 他のレコードの種類のパラメーターを一覧表示するには、`az network dns record --help` を使用します。 レコードの種類ごとの例については、[Azure CLI 2.0 を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-cli.md)に関するページをご覧ください。


## <a name="verify-name-resolution"></a>名前解決の確認

Azure DNS ネーム サーバーに DNS レコードが存在することをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、[ゾーンのネーム サーバーの&1; つに DNS クエリを直接送信する](dns-getstarted-create-dnszone.md#test-name-servers)必要があります。 次のコマンドを実際のレコード ゾーンの正しい値に置き換えてください。

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>次のステップ

[ドメイン名を Azure DNS ネーム サーバーに委任する](dns-domain-delegation.md)方法を確認します。

[Azure CLI 2.0 を使用して DNS ゾーンを管理する](dns-operations-dnszones-cli.md)方法を確認します。

[Azure CLI 2.0 を使用して DNS レコードとレコード セットを管理する](dns-operations-recordsets-cli.md)方法を確認します。


