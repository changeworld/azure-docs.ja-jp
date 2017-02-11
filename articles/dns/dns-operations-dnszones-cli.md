---
title: "CLI を使用した DNS ゾーンの管理 | Microsoft Docs"
description: "Azure CLI を使用して DNS ゾーンを管理できます。 Azure DNS の DNS ゾーンを更新、削除、および作成する方法"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 927503bb18a63db1da2c92e034dbccb7707afab4

---

# <a name="how-to-manage-dns-zones-using-cli"></a>CLI を使用して DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

このガイドでは、クロス プラットフォームの Azure CLI を使用して、DNS ゾーンのリソースを管理する方法を説明します。

以下の手順では、Microsoft Azure CLI を使用します。 Azure DNS コマンドを使用するために、必ず最新の Azure CLI に更新してください。 Windows 用、Linux 用、MAC 用の Azure CLI をインストールできます。 詳しくは、「 [Azure CLI のインストール](../xplat-cli-install.md)」をご覧ください。

Azure DNS は、Azure リソース マネージャー専用のサービスです。 「クラシック」デプロイ モデルはありません。 リソース マネージャー モードを使用するように Azure CLI が構成されていることを確認する必要があります。 このような場合には、`azure config mode arm` コマンドを使用します。

" *エラー: 'dns' は、Azure のコマンドではありません*" というメッセージが表示される場合は、多くの場合、リソース マネージャー モードではなく、Azure Service Management モードで Azure CLI を使用していることが原因です。

Azure DNS に関連するすべての CLI コマンドは `azure network dns` で始まります。 `--help` オプション (短縮形 `-h`) を使用すると、各コマンドのヘルプを利用できます。  For example:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、`azure network dns zone create` コマンドを使用して作成します。 `azure network dns zone create -h` を使用すると、ヘルプが表示されます。

次の例は、[Azure Resource Manager タグ](dns-zones-records.md#tags) の有無にかかわらず DNS ゾーンを作成する方法です。

### <a name="to-create-a-dns-zone"></a>DNS ゾーンを作成するには

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>タグのある DNS ゾーンを作成するには

次の例では、`--tags` パラメーター (短縮形 `-t`) を使用して、*project = demo* と *env = test* の&2; つのタグを含む DNS ゾーンを作成する方法を示します。

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>DNS ゾーンの取得

DNS ゾーンを取得するには、 `azure network dns zone show`を使用します。 `azure network dns zone show -h` を使用すると、ヘルプが表示されます。

次の例では、リソース グループ *MyResourceGroup* から DNS ゾーン *contoso.com* とその関連データを返します。 

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

`azure network dns zone show` では DNS レコードが返されないことにご注意ください。 DNS レコードを一覧表示するには、`azure network dns record-set list` を使用します。


## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

DNS ゾーンを列挙するには、`azure network dns zone list` を使用します。 `azure network dns zone list -h` を使用すると、ヘルプが表示されます。

リソース グループを指定すると、リソース グループ内のゾーンのみが一覧表示されます。

```azurecli
azure network dns zone list MyResourceGroup
```

リソース グループを省略すると、サブスクリプション内のすべてのゾーンが一覧表示されます。

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、 `azure network dns zone set`を使用して行うことができます。 `azure network dns zone set -h` を使用すると、ヘルプが表示されます。

このコマンドによって、ゾーン内の DNS レコード セットが更新されることはありません (「 [DNS レコードの管理方法](dns-operations-recordsets.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現時点では、これらのプロパティは、ゾーンのリソースの [Azure Resource Manager の"タグ"](dns-zones-records.md#tags) に限定されています。

次の例では、DNS ゾーンのタグを更新する方法を示します。 既存のタグは、指定された値に置き換えられます。

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

DNS ゾーンは、`azure network dns zone delete` を使用して削除できます。 `azure network dns zone delete -h` を使用すると、ヘルプが表示されます。

> [!NOTE]
> DNS ゾーンを削除すると、ゾーン内のすべての DNS レコードも削除されます。 削除操作は元に戻すことができません。 DNS ゾーンを使用している場合、ゾーンを削除すると、ゾーンを使用するサービスは機能しなくなります。
>
>ゾーンを誤って削除しないようにするには、「[DNS ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)」をご覧ください。

このコマンドは、確認を求めるプロンプトを表示します。 オプションの `--quiet` スイッチ (短縮形 `-q`) はこのプロンプトを表示しません。

次の例は、リソース グループ *MyResourceGroup* から *contoso.com* ゾーンを削除する方法を示します。

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>次のステップ

DNS ゾーンでレコード セットとレコードを管理する方法については[こちら](dns-getstarted-create-recordset-cli.md)をご覧ください。
<br>
Azure DNS にドメインを委任する方法については[こちら](dns-domain-delegation.md)をご覧ください。




<!--HONumber=Dec16_HO2-->


