---
title: ドメイン ゾーン ファイルをインポートおよびエクスポートする - Azure CLI
titleSuffix: Azure DNS
description: Azure CLI を使用して Azure DNS との間で DNS (ドメイン ネーム システム) ゾーン ファイルをインポートおよびエクスポートする方法を説明します
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/29/2021
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: 949e497057646507fcaa04907d032beb5b8ef47c
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316895"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Azure CLI を使用した DNS ゾーン ファイルのインポートとエクスポート

この記事では、Azure CLI を使用して Azure DNS で DNS ゾーン ファイルをインポートおよびエクスポートする方法について説明します。

## <a name="introduction-to-dns-zone-migration"></a>DNS ゾーンの移行に関する概要

DNS ゾーン ファイルとは、ゾーン内のすべてのドメイン ネーム システム (DNS) レコードに関する詳細が含まれるテキスト ファイルです。 このファイルは標準の形式に従っているため、DNS システム間で DNS レコードを転送するのに適しています。 ゾーン ファイルの使用は、DNS ゾーンを Azure DNS にインポートするための高速で便利な方法です。 また、Azure DNS からゾーン ファイルをエクスポートして、他の DNS システムと共に使用することもできます。

Azure DNS では、Azure コマンドライン インターフェイス (CLI) を使用したゾーン ファイルのインポートおよびエクスポートをサポートしています。 Azure PowerShell または Azure portal を使用したゾーン ファイルのインポートは、現時点では **サポートされていません**。

Azure CLI は、Azure サービスを管理するためのクロスプラットフォーム コマンドライン ツールです。 これは Windows、Mac、Linux に対応しており、[Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)から入手できます。

## <a name="obtain-your-existing-dns-zone-file"></a>既存の DNS ゾーン ファイルの取得

Azure DNS に DNS ゾーン ファイルをインポートするには、事前にゾーン ファイルのコピーを取得しておく必要があります。 このファイルのソースは、DNS ゾーンが現在ホストされている場所によって異なります。

* ご利用の DNS ゾーンがパートナーのサービスによって現在ホストされている場合、DNS ゾーン ファイルをダウンロードするための方法が用意されています。 パートナーのサービスには、ドメイン レジストラー、専用の DNS ホスティング プロバイダー、または代替クラウド プロバイダーが含まれます。
* DNS ゾーンが Windows DNS にホストされている場合、ゾーン ファイルは既定のフォルダー **%systemroot%\system32\dns** にあります。 各ゾーン ファイルへの完全なパスは、DNS コンソールの **[全般]** タブにも表示されます。
* DNS ゾーンが BIND を使用してホストされている場合、各ゾーンのゾーン ファイルの場所は、**named.conf** という名前の BIND 構成ファイルに指定されています。

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Azure DNS への DNS ゾーン ファイルのインポート

ゾーン ファイルのインポートを実行すると、Azure DNS 内にまだゾーンが存在しない場合、新しいゾーンが作成されます。 ゾーンが存在する場合は、ゾーン ファイル内のレコード セットが既存のレコード セットにマージされます。

### <a name="merge-behavior"></a>マージ動作

* 既定では、新しいレコード セットは既存のレコード セットとマージされます。 マージされたレコード セット内の一致するレコードは、複製されません。
* レコード セットをマージした場合、既存のレコード セットの有効期限 (TTL) が使用されます。
* SOA (Start of Authority) パラメーター (`host` を除く) は常に、インポートされたゾーン ファイルから取得されます。 ゾーンの頂点にあるネーム サーバー レコード セットでも、インポートされたゾーン ファイルからの TTL トークンが常に使用されます。
* インポートされた CNAME レコードが同じ名前を持つ既存の CNAME レコードに取って代わることはありません。  
* CNAME レコードと、名前が同じで種類が異なるレコードとの間で競合が発生した場合は、既存のレコードが使用されます。

### <a name="additional-information-about-importing"></a>インポートに関する追加情報

次に、ゾーンのインポート プロセスに関するその他の技術的詳細を説明します。

* `$TTL` ディレクティブは省略可能であり、サポートされています。 `$TTL` ディレクティブを指定しなかった場合、明示的な TTL を持たないレコードは、既定の TTL である 3600 秒が設定されてインポートされます。 同じレコード セット内の 2 つのレコードで別々の TTL を指定した場合は、小さい方の値が使用されます。
* `$ORIGIN` ディレクティブは省略可能であり、サポートされています。 `$ORIGIN` を設定しない場合、使用される既定値は、コマンドラインで指定したゾーン名 (末尾に "." を含む) となります。
* `$INCLUDE` と `$GENERATE` ディレクティブはサポートされていません。
* A、AAAA、CAA、CNAME、MX、NS、SOA、SRV、および TXT のレコード タイプはサポートされています。
* ゾーンの作成時には、Azure DNS によって SOA レコードが自動的に作成されます。 ゾーン ファイルのインポート時には、`host` パラメーターを "*除き*"、すべての SOA パラメーターがゾーン ファイルから取得されます。 このパラメーターには、Azure DNS によって提供される値を使用します。Azure DNS によって提供されたプライマリ ネーム サーバーが参照される必要があるからです。
* ゾーンの作成時には、ゾーンの頂点のネーム サーバー レコード セットも Azure DNS によって自動的に作成されます。 このレコード セットの TTL のみがインポートされます。 これらのレコードには、Azure DNS によって提供されたネーム サーバー名が格納されています。 レコード データは、インポートされたゾーン ファイルに含まれる値によって上書きされることはありません。
* パブリック プレビュー時、Azure DNS では単一行文字列の TXT レコードしかサポートしません。 複数行文字列の TXT レコードは連結され、255 文字に切り捨てられます。

### <a name="cli-format-and-values"></a>CLI 形式と値

DNS ゾーンをインポートする Azure CLI コマンドの形式は次のとおりです。

```azurecli-interactive-interactive
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

値:

* `<resource group>` : Azure DNS のゾーンのリソース グループの名前です。
* `<zone name>` : ゾーンの名前です。
* `<zone file name>` : インポートするゾーン ファイルのパス/名前です。

リソース グループ内にこの名前を持つゾーンがまだ存在していない場合は、自動的に作成されます。 既存のゾーンの場合、インポートされたレコード セットは既存のレコード セットにマージされます。 

### <a name="import-a-zone-file"></a>ゾーン ファイルをインポートする

ゾーン **contoso.com** のゾーン ファイルをインポートするには。

1. リソース グループがない場合は作成します。

    ```azurecli-interactive
    az group create --resource-group myresourcegroup -l westeurope
    ```

1. ファイル **contoso.com.txt** のゾーン **contoso.com** を、リソース グループ **myresourcegroup** 内の新しい DNS ゾーンにインポートするには、コマンド `az network dns zone import` を実行します。

    このコマンドは、ゾーン ファイルを読み込み、解析します。 このコマンドを実行すると、Azure DNS サービスに対して一連の操作が実行されて、ゾーンとゾーン内のすべてのレコード セットが作成されます。 このコマンドからは、進捗状況と共に発生したエラーまたは警告もコンソール ウィンドウにレポートされます。 レコード セットは順番に作成されるので、大規模なゾーン ファイルをインポートするのに数分かかる場合があります。

    ```azurecli-interactive
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>ゾーンを確認する

次のいずれかの方法を使用すれば、ファイルをインポートした後で DNS ゾーンを確認できます。

* レコードを一覧表示するには、次の Azure CLI コマンドを使用します。

    ```azurecli-interactive
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Azure CLI コマンド `az network dns record-set ns list` を使用してレコードを一覧表示することもできます。
* `nslookup` を使用して、レコードの名前解決を確認します。 ゾーンがまだ委任されていない場合は、適切な Azure DNS ネーム サーバーを明示的に指定する必要があります。 次の例では、ゾーンに割り当てられているネーム サーバー名を取得する方法を示します。

    ```azurecli-interactive
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    `nslookup` コマンドを使用して "www" レコードに対してクエリを実行するには、Windows コマンド プロンプトを使用します。

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="update-dns-delegation"></a>DNS 委任を更新する

ゾーンが正しくインポートされたことを確認したら、Azure DNS ネーム サーバーを指すように DNS 委任を更新する必要があります。 詳細については、[DNS 委任の更新](dns-domain-delegation.md)に関する記事を参照してください。

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Azure DNS からの DNS ゾーン ファイルのエクスポート

DNS ゾーンをエクスポートするには、次の Azure CLI コマンドを使用します。

```azurecli-interactive
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

値:

* `<resource group>` : Azure DNS のゾーンのリソース グループの名前です。
* `<zone name>` : ゾーンの名前です。
* `<zone file name>` : エクスポートするゾーン ファイルのパス/名前です。

ゾーンのインポートと同様に、まずサインインし、サブスクリプションを選択して、Resource Manager モードを使用するように Azure CLI を構成します。

### <a name="to-export-a-zone-file"></a>ゾーン ファイルをエクスポートするには

リソース グループ **myresourcegroup** 内の既存の Azure DNS ゾーン **contoso.com** をファイル **contoso.com.txt** (現在のフォルダーにある) にエクスポートするには、`azure network dns zone export` を実行します。 このコマンドは、Azure DNS サービスを呼び出すことで、ゾーン内のレコード セットを列挙し、その結果を BIND と互換性のあるゾーン ファイルにエクスポートします。

```azurecli-interactive
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>次のステップ

* DNS ゾーンでレコード セットとレコードを管理する方法については[こちら](./dns-getstarted-cli.md)をご覧ください。

* Azure DNS にドメインを委任する方法については[こちら](dns-domain-delegation.md)をご覧ください。
