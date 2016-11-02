<properties
   pageTitle="Azure ポータルで DNS ゾーンを作成および管理する方法 | Microsoft Azure"
   description="Azure DNS の DNS ゾーンを作成する方法について説明します。Azure ポータルを使用して、最初の DNS を作成して管理し、DNS ドメインのホスティングを開始するためのステップ バイ ステップ ガイドです。"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Azure ポータルで DNS ゾーンを作成する


> [AZURE.SELECTOR]
- [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)



この記事では、Azure ポータルを使用して DNS ゾーンを作成する手順を説明します。DNS ゾーンは、PowerShell または CLI を使用して作成することもできます。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


### Azure DNS のタグについて


タグは名前と値のペアのリストであり、Azure Resource Manager では、課金またはグループ化のためのリソースのラベル付けに使用されます。タグの詳細については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」を参照してください。

Azure ポータルでタグを追加するには、DNS ゾーンの **[設定]** ブレードを使用します。


## DNS ゾーンの作成

1. Azure ポータルにサインインします。

2. ハブ メニューで **[新規]、[ネットワーク]** をクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーン] ブレードを開きます。
 
	![DNS ゾーン](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. **[DNS ゾーン]** ブレードの下部にある **[作成]** をクリックします。これにより、**[DNS ゾーンの作成]** ブレードが開きます。

	![ゾーンの作成](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. **[DNS ゾーンの作成]** ブレードで、DNS ゾーンの名前を入力します。たとえば、*contoso.com* などのドメインです。前のセクション「[DNS ゾーンの名前について](#names)」を参照してください。

5. 次に、使用するリソース グループを指定します。新しいリソース グループを作成することも、既存のリソース グループを選択することもできます。

6. **[場所]** ドロップダウン リストで、リソース グループの場所を指定します。ここでは DNS ゾーンの場所ではなく、リソース グループの場所を指定することに注意してください。実際の DNS ゾーンのリソースは自動的に "グローバル" となるため、ポータルで指定することはできません (その必要もありません)。

7. ダッシュボードで新しいゾーンをすぐに見つけられるようにするには、**[ダッシュボードにピン留めする]** チェックボックスをオンのままにします。**[Create]** をクリックします。

	![ダッシュボードにピン留めする](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. [作成] をクリックした後、ダッシュボードを見ると、新しいゾーンが構成されていることがわかります。

	![作成](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. 新しいゾーンが作成されると、ダッシュボードに新しいゾーンのブレードが開きます。


## レコードの表示

DNS ゾーンを作成すると、次のレコードも作成されます。

- "Start of Authority" (SOA) レコード。SOA レコードは、すべての DNS ゾーンのルートに存在します。
- 権威ネーム サーバー (NS) レコード。このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

Azure ポータルからレコードを表示することができます。

1. **[DNS ゾーン]** ブレードで、**[すべての設定]** をクリックして DNS ゾーンの **[設定] ブレード**を開きます。

	![ゾーン](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. [要点] ウィンドウの下部に、DNS ゾーンのレコード セットが表示されます。


	![ゾーン](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## テスト

DNS ゾーンをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。ゾーンのネーム サーバーは、上の `Get-AzureRmDnsRecordSet` で一覧表示されているように、NS レコードで与えられます。次のコマンドを実際のゾーンの正しい値に置き換えてください。

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)



## DNS ゾーンの削除

ポータルから直接、DNS ゾーンを削除することができます。Azure DNS の DNS ゾーンを削除する前に、ゾーンの作成時に自動的にゾーンのルートに作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があります。

1. 削除するゾーンの **[DNS ゾーン]** ブレードを探して、ブレードの上部にある **[削除]** をクリックします。
 
2. 自動的に作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があることを通知するメッセージが表示されます。レコード セットを削除した場合は、**[はい]** をクリックします。ポータルから DNS ゾーンを削除した場合、その DNS ゾーンが関連付けられているリソース グループは削除されないことに注意してください。


## 次のステップ

DNS ゾーンを作成したら、[レコード セットとレコード](dns-getstarted-create-recordset-portal.md)を作成し、インターネット ドメインの名前解決を開始します。

<!---HONumber=AcomDC_0817_2016-->