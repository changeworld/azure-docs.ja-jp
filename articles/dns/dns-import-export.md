<properties
   pageTitle="CLI を使用して Azure DNS にドメイン ゾーン ファイルをインポートまたはエクスポートする | Microsoft Azure"
   description="Azure CLI を使用して Azure DNS との間で DNS ゾーン ファイルをインポートおよびエクスポートする方法を説明します。"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="cherylmc"/>

# Azure CLI を使用した DNS ゾーン ファイルのインポートとエクスポート


この記事では、Azure CLI を使用し、Azure DNS の DNS ゾーン ファイルをインポートまたはエクスポートする方法について段階的に説明します。

## DNS ゾーンの移行に関する概要

DNS ゾーン ファイルは、ゾーン内のすべてのドメイン ネーム システム (DNS) レコードに関する詳細が含まれるテキスト ファイルです。このファイルは標準の形式に従っているため、DNS システム間で DNS レコードを転送するのに適しています。ゾーン ファイルを使用すれば、Azure DNS との間で DNS ゾーンを迅速に、確実に、かつ簡単に転送することができます。

Azure DNS では、Azure コマンドライン インターフェイス (CLI) を使用したゾーン ファイルのインポートおよびエクスポートをサポートしています。Azure CLI は、Azure サービスを管理するためのクロスプラットフォーム コマンド ライン ツールです。Azure CLI は Windows、Mac、Linux のプラットフォームに対応しており、[Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)から入手できます。このクロスプラットフォームのサポートは、ゾーン ファイルのインポートおよびエクスポートで特に重要となります。それは最も一般的なネーム サーバー ソフトウェアである [BIND](https://www.isc.org/downloads/bind/) が、通常、Linux 上で実行されるからです。

## 既存の DNS ゾーン ファイルの取得

Azure DNS に DNS ゾーン ファイルをインポートするには、事前にゾーン ファイルのコピーを取得しておく必要があります。このファイルのソースは、DNS ゾーンが現在ホストされている場所によって異なります。

- DNS ゾーンがパートナーのサービス (ドメイン レジストラー、専用の DNS ホスティング プロバイダー、代替クラウド プロバイダーなど) でホストされている場合、そのサービスには、DNS ゾーン ファイルをダウンロードする機能があるはずです。

- DNS ゾーンが Windows DNS にホストされている場合、ゾーン ファイルは既定のフォルダー **%systemroot%\\system32\\dns** にあります。各ゾーン ファイルへの完全パスは、DNS サービスの管理コンソールの **[全般]** タブにも表示されます。

- DNS ゾーンが BIND を使用してホストされている場合、各ゾーンのゾーン ファイルの場所は、**named.conf** という名前の BIND 構成ファイルに指定されています。

**GoDaddy のゾーン ファイルを使用する**<BR> GoDaddy からダウンロードされるゾーン ファイルは標準形式と若干異なります。これらのゾーン ファイルを Azure DNS にインポートする前に修正する必要があります。各 DNS レコードの RDATA にある DNS 名は、完全修飾名として指定されますが、接尾辞 "." がありません。 このため、このような DNS 名は他の DNS システムで相対名として解釈されます。Azure DNS にインポートする前に、ゾーン ファイルを編集してその名前に接尾辞 "." を付加する必要があります。

## Azure DNS への DNS ゾーン ファイルのインポート


ゾーン ファイルのインポートを実行すると、Azure DNS にゾーンが存在しない場合、新しいゾーンが作成されます。ゾーンが既に存在する場合は、ゾーン ファイル内のレコード セットを既存のレコード セットにマージする必要があります。

### マージ動作

- 既定では、既存のレコード セットと新規のレコード セットがマージされます。マージされたレコード セット内の一致するレコードは重複除去されます。

- あるいは、`--force` オプションを指定すると、インポート プロセスによって既存のレコード セットが新しいレコード セットに置き換えられます。既存のレコード セットのうち、インポートされたゾーン ファイル内に対応するレコード セットが存在しないものは、削除されません。

- レコード セットをマージした場合、既存のレコード セットの TTL (Time to Live) が使用されます。`--force` を使用した場合、新しいレコード セットの TTL が使用されます。

- `--force` を使用してもしなくても、SOA (Start of Authority) パラメーター (`host` を除く) は常に、インポートされたゾーン ファイルから取得されます。同様に、ゾーンの頂点にあるネーム サーバー レコード セットの場合も、TTL は常に、インポートされたゾーン ファイルから取得されます。

- `--force` パラメーターを指定しなければ、インポートされた CNAME レコードが同じ名前を持つ既存の CNAME レコードに取って代わることはありません。

- CNAME レコードと、名前は同じでも形式が異なる別のレコードとの間で競合が生じた場合 (どちらが既存のレコードでどちらが新規のレコードかに関係なく)、既存のレコードが保持されます。この動作は、`--force` の使用とは関係ありません。

### インポートに関する追加情報

次に、ゾーンのインポート プロセスに関するその他の技術的詳細を説明します。

- `$TTL` ディレクティブは省略可能であり、サポートされています。`$TTL` ディレクティブを指定しなかった場合、明示的な TTL を持たないレコードは、既定の TTL である 3600 秒が設定されてインポートされます。同じレコード セット内の 2 つのレコードで別々の TTL を指定した場合は、小さい方の値が使用されます。

- `$ORIGIN` ディレクティブは省略可能であり、サポートされています。`$ORIGIN` を設定しないと、コマンドラインで指定したゾーン名 (に加えて接尾辞 ".") が既定値として使用されます。

- `$INCLUDE` と `$GENERATE` ディレクティブはサポートされていません。

- A、AAAA、CNAME、MX、NS、SOA、SRV、および TXT のレコード タイプはサポートされています。

- ゾーンの作成時には、Azure DNS によって SOA レコードが自動的に作成されます。ゾーン ファイルのインポート時には、`host` パラメーターを*除き*、すべての SOA パラメーターがゾーン ファイルから取得されます。このパラメーターは Azure DNS から提供された値を使用します。‘host’ パラメーターは、Azure DNS で指定されたプライマリ ネーム サーバーを参照する必要があるからです。

- ゾーンの作成時には、ゾーンの頂点のネーム サーバー レコード セットも Azure DNS によって自動的に作成されます。このレコード セットの TTL のみがインポートされます。これらのレコードには、Azure DNS によって提供されたネーム サーバー名が格納されています。レコード データは、インポートされたゾーン ファイルに含まれる値によって上書きされることはありません。

- パブリック プレビュー時、Azure DNS では単一行文字列の TXT レコードしかサポートしません。複数行文字列の TXT レコードは連結され、255 文字に切り捨てられます。

### CLI 形式と値


DNS ゾーンをインポートする Azure CLI コマンドの形式は次のとおりです。<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

値:

- `<resource group>`: Azure DNS のゾーンのリソース グループの名前です。
- `<zone name>`: ゾーンの名前です。
- `<zone file name>`: インポートするゾーン ファイルのパス/名前です。

リソース グループ内にこの名前のゾーンが存在しない場合は、自動的に作成されます。ゾーンが既に存在する場合、インポートされたレコード セットは既存のレコード セットとマージされます。既存のレコード セットを上書きするには、`--force` オプションを使用します。

実際にインポートしないでゾーン ファイルの形式を確認するには、`--parse-only` オプションを使用します。

### 手順 1.ゾーン ファイルをインポートする

ゾーン **contoso.com** のゾーン ファイルをインポートするには。

1. Azure CLI を使用して Azure サブスクリプションにサインインします。

		azure login

2. 新しい DNS ゾーンを作成するサブスクリプションを選択します。

		azure account set <subscription name>

3. Azure DNS は Azure リソース マネージャー限定のサービスです。Azure CLI をリソース マネージャー モードに切り替える必要があります。

		azure config mode arm

4. Azure DNS サービスを使用する前に、Microsoft.Network リソース プロバイダーを使用するサブスクリプションを登録する必要があります。(この操作は、各サブスクリプションで 1 回だけ行うことができます)。

		azure provider register Microsoft.Network

5. まだ存在しない場合は、リソース マネージャー リソース グループを作成する必要があります。

		azure group create myresourcegroup westeurope

6. ファイル **contoso.com.txt** のゾーン **contoso.com** を、リソース グループ **myresourcegroup** 内の新しい DNS ゾーンにインポートするには、コマンド `azure network dns zone import` を実行します。<BR>このコマンドはゾーン ファイルを読み込み、解析します。このコマンドは、Azure DNS サービスに対して一連のコマンドを実行することで、ゾーンとゾーン内のすべてのレコード セットを作成します。このコマンドでは、コンソール ウィンドウに進捗状況もレポートします。発生している場合、エラーまたは警告もレポートします。レコード セットは順番に作成されるので、大規模なゾーン ファイルをインポートするのに数分かかる場合があります。

		azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### 手順 2.ゾーンを確認する

次のいずれかの方法で、ファイルをインポートした後に DNS ゾーンを確認できます。

- 次の Azure CLI コマンドを使用してレコードを一覧表示できます。

		azure network dns record-set list myresourcegroup contoso.com

- PowerShell コマンドレット `Get-AzureRmDnsRecordSet` でレコードを一覧表示できます。

- `nslookup` を使用し、レコードの名前解決を確認できます。ゾーンはまだ委任されていないので、適切な Azure DNS ネーム サーバーを明示的に指定する必要があります。次の例では、ゾーンに割り当てられているネーム サーバー名を取得する方法を示します。`nslookup` を使用し、"www" レコードを照会する方法も示します。

    	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
    	info:Executing command network dns record-set show
    	+ Looking up the DNS Record Set "@" of type "NS"
    	data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    	data:Name: @
    	data:Type: Microsoft.Network/dnszones/NS
    	data:Location: global
    	data:TTL : 3600
    	data:NS records
    	data:Name server domain name : ns1-01.azure-dns.com
    	data:Name server domain name : ns2-01.azure-dns.net
    	data:Name server domain name : ns3-01.azure-dns.org
    	data:Name server domain name : ns4-01.azure-dns.info
    	data:
    	info:network dns record-set show command OK
    
    	C:\> nslookup www.contoso.com ns1-01.azure-dns.com
    
    	Server: ns1-01.azure-dns.com
    	Address:  40.90.4.1
    
    	Name:www.contoso.com
    	Addresses:  134.170.185.46
    	134.170.188.221

### 手順 3.DNS 委任を更新する

ゾーンが正しくインポートされたことを確認したら、Azure DNS ネーム サーバーを指すように DNS 委任を更新します。詳細については、[DNS 委任の更新に関する記事](dns-domain-delegation.md)を参照してください。

## Azure DNS からの DNS ゾーン ファイルのエクスポート

DNS ゾーンをインポートする Azure CLI コマンドの形式は次のとおりです。<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

値:

- `<resource group>`: Azure DNS のゾーンのリソース グループの名前です。
- `<zone name>`: ゾーンの名前です。
- `<zone file name>`: エクスポートするゾーン ファイルのパス/名前です。

ゾーンをインポートする場合、まずサインインし、サブスクリプションを選択し、リソース マネージャー モードを使用するために Azure CLI を構成します。

### ゾーン ファイルをエクスポートするには


1. Azure CLI を使用して Azure サブスクリプションにサインインします。

		azure login

2. 新しい DNS ゾーンを作成するサブスクリプションを選択します。

		azure account set <subscription name>

3. Azure DNS は、Azure リソース マネージャー専用のサービスです。Azure CLI は、リソース マネージャー モードに切り替える必要があります。

		azure config mode arm

4. リソース グループ **myresourcegroup** 内の既存の Azure DNS ゾーン **contoso.com** をファイル **contoso.com.txt** (現在のフォルダーにある) にエクスポートするには、`azure network dns zone export` を実行します。このコマンドは、Azure DNS サービスを呼び出すことで、ゾーン内のレコード セットを列挙し、その結果を BIND と互換性のあるゾーン ファイルにエクスポートします。

		azure network dns zone export myresourcegroup contoso.com contoso.com.txt

<!---HONumber=AcomDC_0518_2016-->