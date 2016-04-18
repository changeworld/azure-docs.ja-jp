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
   ms.date="03/29/2016"
   ms.author="cherylmc"/>

# Azure ポータルでの DNS ゾーンの作成と管理


> [AZURE.SELECTOR]
- [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)



この記事では、Azure ポータルを使用して DNS ゾーンを作成する手順を説明します。DNS ゾーンは、PowerShell または CLI を使用して作成することもできます。

ドメイン "contoso.com" には、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、多数の DNS レコードが含まれている場合があります。DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。ドメインのホストを開始するには、まず DNS ゾーンを作成します。特定のドメイン用に作成された DNS レコードは、そのドメインの DNS ゾーン内に含まれます。

### <a name="names"></a>DNS ゾーンの名前について
 
- ゾーンの名前がリソース グループ内で一意であること、および作成するゾーンがまだ存在していないことが必要です。そうでない場合、操作は失敗します。

- 同じゾーン名は、別のリソース グループまたは別の Azure サブスクリプション内で再利用できます。複数のゾーンで同じ名前を共有できますが、各インスタンスには異なるネーム サーバー アドレスが割り当てられます。また、親ドメインから委任できるインスタンスは 1 つだけです。詳細については、[Azure DNS へのドメインの委任](#delegate)に関するページを参照してください。

### Azure DNS のタグについて


タグは名前と値のペアのリストであり、Azure Resource Manager では、課金またはグループ化のためのリソースのラベル付けに使用されます。タグの詳細については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」を参照してください。

Azure ポータルでタグを追加するには、DNS ゾーンの **[設定]** ブレードを使用します。


## DNS ゾーンの作成

1. Azure ポータルにサインインします。

2. ハブ メニューで **[新規]、[ネットワーク]** をクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーン] ブレードを開きます。
 
	![DNS ゾーン](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. **[DNS ゾーン]** ブレードの下部にある **[作成]** をクリックします。これにより、**[Create DNS zone (DNS ゾーンの作成)]** ブレードが開きます。

	![ゾーンの作成](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. **[Create DNS zone (DNS ゾーンの作成)]** ブレードで、DNS ゾーンの名前を入力します。たとえば、*contoso.com* などのドメインです。前のセクション「[DNS ゾーンの名前について](#names)」を参照してください。

5. 次に、使用するリソース グループを指定します。新しいリソース グループを作成することも、既存のリソース グループを選択することもできます。

6. **[場所]** ドロップダウン リストで、リソース グループの場所を指定します。ここでは DNS ゾーンの場所ではなく、リソース グループの場所を指定することに注意してください。実際の DNS ゾーンのリソースは自動的に "グローバル" となるため、ポータルで指定することはできません (その必要もありません)。

7. ダッシュボードで新しいゾーンをすぐに見つけられるようにするには、**[ダッシュボードにピン留めする]** チェックボックスをオンのままにします。**[Create]** をクリックします。

	![ダッシュボードにピン留めする](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. [作成] をクリックした後、ダッシュボードを見ると、新しいゾーンが構成されていることがわかります。

	![作成](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. 新しいゾーンが作成されると、ダッシュボードに新しいゾーンのブレードが開きます。


## DNS ゾーン レコードの表示

DNS ゾーンを作成すると、次のレコードも作成されます。

- "Start of Authority" (SOA) レコード。SOA レコードは、すべての DNS ゾーンのルートに存在します。
- 権威ネーム サーバー (NS) レコード。このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

Azure ポータルからレコードを表示することができます。

1. **[DNS ゾーン]** ブレードで、**[すべての設定]** をクリックして DNS ゾーンの **[設定] ブレード**を開きます。 

	![ゾーン](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. [要点] ウィンドウの下部に、DNS ゾーンのレコード セットが表示されます。


	![ゾーン](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)



## DNS ゾーンの削除

ポータルから直接、DNS ゾーンを削除することができます。Azure DNS の DNS ゾーンを削除する前に、ゾーンの作成時に自動的にゾーンのルートに作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があります。

1. 削除するゾーンの **[DNS ゾーン]** ブレードを探して、ブレードの上部にある **[削除]** をクリックします。
 
2. 自動的に作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があることを通知するメッセージが表示されます。レコード セットを削除した場合は、**[はい]** をクリックします。ポータルから DNS ゾーンを削除した場合、その DNS ゾーンが関連付けられているリソース グループは削除されないことに注意してください。


## 次のステップ

DNS ゾーンを作成した後は、「[Get started creating record sets and records (レコード セットとレコードの作成の概要)](dns-getstarted-create-recordset-portal.md)」、「[How to manage DNS zones (DNS ゾーンの管理方法)](dns-operations-dnszones.md)」、「[How to manage DNS records (DNS レコードの管理方法)](dns-operations-recordsets-portal.md)」を参照してください。

<!---HONumber=AcomDC_0406_2016-->