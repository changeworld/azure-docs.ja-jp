---
title: Azure プライベート DNS のドメイン ゾーン ファイルをインポートおよびエクスポートする - Azure CLI
titleSuffix: Azure DNS
description: Azure CLI を使用して Azure プライベート DNS との間で DNS ゾーン ファイルをインポートおよびエクスポートする方法を説明します
services: dns
author: duongau
ms.service: dns
ms.date: 03/16/2021
ms.author: duau
ms.topic: how-to
ms.openlocfilehash: 2e5babb85dbf4aa7da707e22be5eeaf3ae89972d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450756"
---
# <a name="import-and-export-a-private-dns-zone-file-for-azure-private-dns"></a>Azure プライベート DNS のプライベート DNS ゾーンファイルをインポートおよびエクスポートする

この記事では、Azure CLI を使用し、Azure DNS の DNS ゾーン ファイルをインポートまたはエクスポートする方法について段階的に説明します。

## <a name="introduction-to-dns-zone-migration"></a>DNS ゾーンの移行に関する概要

DNS ゾーン ファイルは、ゾーン内のすべてのドメイン ネーム システム (DNS) レコードに関する詳細が含まれるテキスト ファイルです。 このファイルは標準の形式に従っているため、DNS システム間で DNS レコードを転送するのに適しています。 ゾーン ファイルを使用すれば、Azure DNS との間で DNS ゾーンを迅速に、確実に、かつ簡単に転送することができます。

Azure プライベート DNS では、Azure コマンドライン インターフェイス (CLI) を使用したゾーン ファイルのインポートおよびエクスポートがサポートされています。 Azure PowerShell または Azure Portal を使用したゾーン ファイルのインポートは、現時点では **サポートされていません**。

Azure CLI は、Azure サービスを管理するためのクロスプラットフォーム コマンド ライン ツールです。 Azure CLI は Windows、Mac、Linux のプラットフォームに対応しており、[Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)から入手できます。 このクロスプラットフォームのサポートは、ゾーン ファイルのインポートおよびエクスポートで重要となります。それは最も一般的なネーム サーバー ソフトウェアである [BIND](https://www.isc.org/downloads/bind/) が、通常、Linux 上で実行されるからです。

## <a name="obtain-your-existing-dns-zone-file"></a>既存の DNS ゾーン ファイルの取得

Azure DNS に DNS ゾーン ファイルをインポートするには、事前にゾーン ファイルのコピーを取得しておく必要があります。 このファイルのソースは、DNS ゾーンが現在ホストされている場所によって異なります。

* DNS ゾーンがパートナーのサービス (ドメイン レジストラー、専用の DNS ホスティング プロバイダー、代替クラウド プロバイダーなど) でホストされている場合、そのサービスには、DNS ゾーン ファイルをダウンロードする機能があるはずです。
* DNS ゾーンが Windows DNS にホストされている場合、ゾーン ファイルは既定のフォルダー **%systemroot%\system32\dns** にあります。 各ゾーン ファイルへの完全なパスは、DNS コンソールの **[全般]** タブにも表示されます。
* DNS ゾーンが BIND を使用してホストされている場合、各ゾーンのゾーン ファイルの場所は、 **named.conf** という名前の BIND 構成ファイルに指定されています。

## <a name="import-a-dns-zone-file-into-azure-private-dns"></a>Azure プライベート DNS に DNS ゾーン ファイルをインポートする

ゾーン ファイルのインポートを実行すると、Azure プライベート DNS にゾーンが存在しない場合、新しいゾーンが作成されます。 ゾーンが既に存在する場合は、ゾーン ファイル内のレコード セットを既存のレコード セットにマージする必要があります。

### <a name="merge-behavior"></a>マージ動作

* 既定では、既存のレコード セットと新規のレコード セットがマージされます。 マージされたレコード セット内の一致するレコードは重複除去されます。
* レコード セットをマージした場合、既存のレコード セットの TTL (Time to Live) が使用されます。
* SOA (Start of Authority) パラメーター (`host` を除く) は常に、インポートされたゾーン ファイルから取得されます。 同様に、ゾーンの頂点にあるネーム サーバー レコード セットの場合も、TTL は常に、インポートされたゾーン ファイルから取得されます。
* インポートされた CNAME レコードが同じ名前を持つ既存の CNAME レコードに取って代わることはありません。  
* CNAME レコードと、名前は同じでも形式が異なる別のレコードとの間で競合が生じた場合 (どちらが既存のレコードでどちらが新規のレコードかに関係なく)、既存のレコードが保持されます。 

### <a name="additional-information-about-importing"></a>インポートに関する追加情報

次に、ゾーンのインポート プロセスに関するその他の技術的詳細を説明します。

* `$TTL` ディレクティブは省略可能であり、サポートされています。 `$TTL` ディレクティブを指定しなかった場合、明示的な TTL を持たないレコードは、既定の TTL である 3600 秒が設定されてインポートされます。 同じレコード セット内の 2 つのレコードで別々の TTL を指定した場合は、小さい方の値が使用されます。
* `$ORIGIN` ディレクティブは省略可能であり、サポートされています。 `$ORIGIN` を設定しないと、コマンドラインで指定したゾーン名 (に加えて接尾辞 ".") が既定値として使用されます。
* `$INCLUDE` と `$GENERATE` ディレクティブはサポートされていません。
* A、AAAA、CAA、CNAME、MX、NS、SOA、SRV、および TXT のレコード タイプはサポートされています。
* ゾーンの作成時には、Azure DNS によって SOA レコードが自動的に作成されます。 ゾーン ファイルのインポート時には、`host` パラメーターを "*除き*"、すべての SOA パラメーターがゾーン ファイルから取得されます。 このパラメーターは Azure DNS から提供された値を使用します。 ‘host’ パラメーターは、Azure DNS で指定されたプライマリ ネーム サーバーを参照する必要があるからです。
* ゾーンの作成時には、ゾーンの頂点のネーム サーバー レコード セットも Azure DNS によって自動的に作成されます。 このレコード セットの TTL のみがインポートされます。 これらのレコードには、Azure DNS によって提供されたネーム サーバー名が格納されています。 レコード データは、インポートされたゾーン ファイルに含まれる値によって上書きされることはありません。
* パブリック プレビュー時、Azure DNS では単一行文字列の TXT レコードしかサポートしません。 複数行文字列の TXT レコードは連結され、255 文字に切り捨てられます。

### <a name="cli-format-and-values"></a>CLI 形式と値

DNS ゾーンをインポートする Azure CLI コマンドの形式は次のとおりです。

```azurecli
az network private-dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

値:

* `<resource group>` : Azure DNS のゾーンのリソース グループの名前です。
* `<zone name>` : ゾーンの名前です。
* `<zone file name>` : インポートするゾーン ファイルのパス/名前です。

リソース グループ内にこの名前のゾーンが存在しない場合は、自動的に作成されます。 ゾーンが既に存在する場合、インポートされたレコード セットは既存のレコード セットとマージされます。

### <a name="import-a-zone-file"></a>ゾーン ファイルをインポートする

ゾーン **contoso.com** のゾーン ファイルをインポートするには。

1. まだ存在しない場合は、リソース マネージャー リソース グループを作成する必要があります。

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. ファイル **contoso.com.txt** のゾーン **contoso.com** を、リソース グループ **myresourcegroup** 内の新しい DNS ゾーンにインポートするには、コマンド `az network private-dns zone import` を実行します。<BR>このコマンドは、ゾーン ファイルを読み込み、解析します。 このコマンドは、Azure DNS サービスに対して一連のコマンドを実行することで、ゾーンとゾーン内のすべてのレコード セットを作成します。 このコマンドでは、コンソール ウィンドウに進捗状況をレポートします。発生している場合、エラーまたは警告もレポートします。 レコード セットは順番に作成されるので、大規模なゾーン ファイルをインポートするのに数分かかる場合があります。

    ```azurecli
    az network private-dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>ゾーンを確認する

次のいずれかの方法で、ファイルをインポートした後に DNS ゾーンを確認できます。

* 次の Azure CLI コマンドを使用してレコードを一覧表示できます。

    ```azurecli
    az network private-dns record-set list -g myresourcegroup -z contoso.com
    ```

* `nslookup` を使用し、レコードの名前解決を確認できます。 ゾーンはまだ委任されていないので、適切な Azure DNS ネーム サーバーを明示的に指定する必要があります。 次の例では、ゾーンに割り当てられているネーム サーバー名を取得する方法を示します。 `nslookup` を使用して "www" レコードを照会する方法も示します。

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Azure DNS からの DNS ゾーン ファイルのエクスポート

DNS ゾーンをエクスポートする Azure CLI コマンドの形式は次のとおりです。

```azurecli
az network private-dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

値:

* `<resource group>` : Azure DNS のゾーンのリソース グループの名前です。
* `<zone name>` : ゾーンの名前です。
* `<zone file name>` : エクスポートするゾーン ファイルのパス/名前です。

ゾーンのインポートと同様に、まずサインインし、サブスクリプションを選択して、Resource Manager モードを使用するように Azure CLI を構成します。

### <a name="to-export-a-zone-file"></a>ゾーン ファイルをエクスポートするには

リソース グループ **myresourcegroup** 内の既存の Azure DNS ゾーン **contoso.com** をファイル **contoso.com.txt** (現在のフォルダーにある) にエクスポートするには、`azure network private-dns zone export` を実行します。 このコマンドは、Azure DNS サービスを呼び出すことで、ゾーン内のレコード セットを列挙し、その結果を BIND と互換性のあるゾーン ファイルにエクスポートします。

```azurecli
az network private-dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>次のステップ

* DNS ゾーンでレコード セットとレコードを管理する方法については[こちら](./private-dns-getstarted-cli.md)をご覧ください。
