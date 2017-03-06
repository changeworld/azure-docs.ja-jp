---
title: "Azure CLI 2.0 を使用した DNS ゾーンの作成 | Microsoft Docs"
description: "Azure DNS に DNS ゾーンを作成する方法について説明します。 Azure CLI 2.0 を使用して最初の DNS ゾーンを作成して管理するためのステップ バイ ステップ ガイドです。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Azure CLI 2.0 を使用した Azure DNS ゾーンの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

この記事では、Windows、Mac、Linux で使用できるクロスプラットフォーム Azure CLI を使用して DNS ゾーンを作成する手順について説明します。 DNS ゾーンは、[Azure PowerShell](dns-getstarted-create-dnszone.md) または [Azure Portal](dns-getstarted-create-dnszone-portal.md) を使用して作成することもできます。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) - クラシック デプロイ モデルと Resource Manager デプロイ モデル用の CLI。
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) - Resource Manager デプロイ モデル用の次世代 CLI。

## <a name="introduction"></a>はじめに

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Azure DNS 向け Azure CLI 2.0 の設定

### <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下がそろっていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

* Windows、Linux、または MAC 用の最新バージョンの Azure CLI をインストールしてください。 詳細については、[Azure CLI 2.0 のインストール](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)に関するページをご覧ください。

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

コンソール ウィンドウを開き、資格情報を使用して認証を行います。 詳細については、「Azure CLI から Azure へのログイン」を参照してください

```azurecli
az login
```

### <a name="select-the-subscription"></a>サブスクリプションの選択

アカウントのサブスクリプションを確認します。

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>使用する Azure サブスクリプションを選択します。

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、`az network dns zone create` コマンドを使用して作成します。 このコマンドのヘルプを表示するには、「`az network dns zone create --help`」と入力します。

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>DNS ゾーンの確認

### <a name="view-records"></a>レコードの表示

DNS ゾーンを作成すると、次の DNS レコードも作成されます。

* *Start of Authority* (SOA) レコード。 このレコードは、すべての DNS ゾーンのルートに存在します。
* 権威ネーム サーバー (NS) レコード。 これらのレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。 Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

このレコードを表示するには、 `az network dns record-set list`を使用します。

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

`az network dns record-set list` の応答を次に示します。

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> DNS ゾーンのルート (または " *頂点*") にあるレコード セットは、レコード セット名として **@** を使用します。

### <a name="test-name-servers"></a>ネーム サーバーのテスト

Azure DNS ネーム サーバーに DNS ゾーンが存在することをテストするには、nslookup、dig、`Resolve-DnsName` PowerShell コマンドレットなどの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの&1; つに DNS クエリを直接送信する必要があります。 ゾーンのネーム サーバーは、`az network dns record-set list` で一覧表示されるように、NS レコードで与えられます。

次の例では、dig を使用し、DNS ゾーンに割り当てられたネーム サーバーを使用して、ドメイン contoso.com をクエリします。 実際のゾーンの正しい値に置き換えてください。

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>次のステップ

DNS ゾーンを作成した後は、ゾーンで [DNS レコード セットとレコードの作成](dns-getstarted-create-recordset-cli.md)を行います。


