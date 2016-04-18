<properties
   pageTitle="Azure ポータルを使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Azure"
   description="Azure DNS のホスト レコードを作成する方法と、Azure ポータルを使用してレコード セットとレコードを作成する方法について説明します。"
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


# Azure ポータルを使用した DNS レコードとレコード セットの作成


> [AZURE.SELECTOR]
- [Azure ポータル](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


DNS ゾーンを作成した後は、ドメインの DNS レコードを追加する必要があります。そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

## レコード セットとレコードについて

### レコードについて

各 DNS レコードには名前と種類があります。

"完全修飾" ドメイン名 (FQDN) にはゾーン名が含まれますが、"相対" 名には含まれません。たとえば、"contoso.com" ゾーン内の相対レコード名 "www" によって、完全修飾レコード名 "www.contoso.com" が示されます。

>[AZURE.NOTE] Azure DNS では、相対名を使用してレコードを指定します。

レコードは、含まれるデータによってさまざまな種類があります。最も一般的な種類は "**A**" レコードで、名前が IPv4 アドレスにマップされます。また、別の種類の "**MX**" レコードでは、名前がメール サーバーにマップされます。

Azure DNS では、一般的な DNS レコードの種類である A、AAAA、CNAME、MX、NS、SOA、SRV、TXT をすべてサポートしています。SPF レコードは、レコードの種類として TXT を使用して作成する必要があります。詳細については、[このページ](http://tools.ietf.org/html/rfc7208#section-3.1)を参照してください。


### レコード セットについて

場合によっては、特定の名前と種類の DNS レコードを複数作成する必要があることもあります。たとえば、www.contoso.com という Web サイトが 2 つの異なる IP アドレスでホストされているとします。この場合は、IP アドレスごとに異なる A レコードが、合計 2 つ必要になります。これはレコード セットの例です。

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Azure DNS は、レコード セットを使用して DNS レコードを管理します。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類の DNS レコードのコレクションです。ほとんどのレコード セットには 1 つのレコードが含まれていますが、上の例のように複数のレコードが含まれているレコード セットも珍しくはありません

SOA および CNAME という種類のレコード セットは例外で、DNS 標準により、これらの種類では同じ名前を持つ複数のレコードが許可されません。

Time-to-Live (TTL) は、各レコードが再度照会されるまでクライアントによってキャッシュされる期間を指定します。上の例では、TTL は 3600 秒、つまり 1 時間です。TTL は、各レコードではなく、レコード セットに対して指定されるため、そのレコード セット内のすべてのレコードに同じ値が使用されます。

#### ワイルドカード レコード セット

Azure DNS は、[ワイルドカード レコード](https://en.wikipedia.org/wiki/Wildcard_DNS_record)をサポートしています。ワイルドカード レコードは、一致する名前を含むクエリに対してに返されます (非ワイルドカード レコード セットに、より近い一致がない場合)。ワイルドカード レコード セットは、NS と SOA を除くすべてのレコードの種類でサポートされています。

ワイルドカード レコード セットを作成するには、レコード セット名 "*" を使用するか、最初のラベルが "*" の名前 ("*.foo" など) を使用します。

#### CNAME レコード セット

CNAME レコード セットは、同じ名前を持つ他のレコード セットとは共存できません。たとえば、相対名 "www" を持つ CNAME と相対名 "www" を持つ A レコードを同時に作成することはできません。ゾーンの頂点 (名前は '@') には、ゾーンの作成時に作成された NS および SOA レコード セットが必ず含まれるため、ゾーンの頂点で CNAME レコード セットを作成することはできません。これらの制約は、DNS 標準から生じるものであり、Azure DNS の制限事項ではありません。


## 新しいレコード セットとレコードを作成するには

次の例では、Azure ポータルを使用して、レコード セットとレコードを作成する方法を説明します。ここでは DNS 'A' レコードを使用します。

1. Azure ポータルにログインします。

2. レコード セットを作成する **DNS ゾーン**のブレードに移動します。

3. DNS ゾーンのブレードの上部にある **[レコード セット]** をクリックして **[Add record set (レコード セットの追加)]** ブレードを開きます。
 
	![新しいレコード セット](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. **[Add record set (レコード セットの追加)]** ブレードで、レコード セットの名前を入力します(例: "**www**")。
  
	![レコード セットの追加](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 作成するレコードの種類を選択します(例: **A**)。

6. **TTL** を設定します。ポータルの既定値は 1 時間です。

7. IP アドレスを 1 行に 1 つずつ追加します。上記のレコード セット名とレコードの種類を使用することで、www レコード セットの A レコードに IPv4 IP アドレスが追加されます。

8. IP アドレスの追加が完了したら、ブレードの下部にある **[OK]** をクリックします。これで、DNS レコード セットが作成されます。

## DNS ツールを使用して DNS ゾーンをテストする


Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。ゾーンのネーム サーバーは、DNS ゾーンのブレードの [要点] ウィンドウに表示されています。詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

DNS ゾーンをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。


## 次のステップ

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。DNS 作成の自動化については、「[.NET SDK を使用した DNS ゾーンとレコード セットの作成](dns-sdk.md)」を参照してください。

<!---HONumber=AcomDC_0406_2016-->