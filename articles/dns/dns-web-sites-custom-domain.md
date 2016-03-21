<properties 
   pageTitle="Web アプリのカスタム DNS レコードの作成 | Microsoft Azure" 
   description="Azure DNS を使用して Web アプリのカスタム ドメイン DNS レコードを作成する方法。CNAME または A レコードを使用して、ドメインの所有権を確認する手順" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# カスタム ドメインにおける Web アプリの DNS レコードの作成

Azure DNS を使用すると、Web アプリ用にカスタム ドメインをホストすることができます。たとえば、Azure Web アプリを作成し、ユーザーが FQDN として contoso.com または www.contoso.com を使用してそのアプリにアクセスできるようにするとします。このシナリオでは、contoso.com を指すルートの A レコードと、この A レコードを指す www 名の CNAME レコードの 2 つのレコードを作成する必要があります。

> [AZURE.NOTE]Azure の Web アプリ用に A レコードを作成する場合、Web アプリの基になる IP アドレスが変更されると、A レコードを手動で更新する必要があることに注意してください。

カスタム ドメインのレコードを作成するには、事前に Azure DNS に DNS ゾーンを作成して、レジストラーのゾーンを Azure DNS に委任する必要があります。DNS ゾーンを作成するには、「[Azure DNS の概要](../dns-getstarted-create-dnszone/#Create-a-DNS-zone)」の手順に従います。DNS を Azure DNS に委任するには、「[Azure DNS へのドメインの委任](../dns-domain-delegation)」の手順に従います。
 
## カスタム ドメインの A レコードの作成

A レコードは、名前をその IP アドレスに対応付けるために使用されます。次の例では、A レコードとして @ を IPv4 アドレスに割り当てます。

### 手順 1.
 
A レコードを作成して $rs 変数に割り当てます。
	
	PS C:\>$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### 手順 2.

割り当てられた $rs 変数を使用して、前に作成したレコード セット "@" に IPv4 の値を追加します。割り当てられた IPv4 の値は、Web アプリの IP アドレスになります。

> [AZURE.NOTE]Web アプリの IP アドレスを確認するには、「[Azure App Service のカスタム ドメイン名の構成](../web-sites-custom-domain-name/#Find-the-virtual-IP-address)」の手順に従います。

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### 手順 3.

レコード セットへの変更をコミットします。レコード セットへの変更を Azure DNS にアップロードするには、次のように Set-AzureRMDnsRecordSet を使用します。

	Set-AzureRMDnsRecordSet -RecordSet $rs

## カスタム ドメインの CNAME レコードの作成

ドメインが既に Azure DNS で管理されていると想定すると ([DNS ドメインの委任](../dns-domain-delegation)に関するページを参照)、次の例を使用して contoso.azurewebsites.net の CNAME レコードを作成できます。

### 手順 1.

PowerShell を開き、新しい CNAME レコード セットを作成して $rs 変数に割り当てます。

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

これによって、"Time To Live" が 600 秒で、CNAME という種類のレコード セットが "contoso.com" という名前の DNS ゾーンに作成されます。

### 手順 2.

CNAME レコード セットが作成されたら、Web アプリを指すエイリアス値を作成する必要があります。

既に割り当て済みの "$rs" 変数を使用すると、次の PowerShell コマンドを使用して、Web アプリ contoso.azurewebsites.net のエイリアスを作成できます。

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### 手順 3.

Set-AzureRMDnsRecordSet コマンドレットを使用して変更をコミットします。

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

次のように nslookup を使用して "www.contoso.com" にクエリを実行することによって、レコードが正しく作成されたかどうかを検証できます。

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## Web アプリの awverify レコードの作成 (A レコードのみ)

Web アプリの A レコードを使用する場合は、カスタム ドメインを所有していることを確認できるように、確認プロセスを実行する必要があります。この確認手順は、"awverify" という名前の特別な CNAME レコードを作成することで実行されます。

次の例では、カスタム ドメインの所有権を確認するために、contoso.com の "awverify" レコードが作成されます。

### 手順 1.

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### 手順 2.

awverify レコード セットが作成されると、次のコマンドに示すように、CNAME レコード セットのエイリアスを awverify.contoso.azurewebsites.net に割り当てる必要があります。

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### 手順 3.

次のコマンドに示すように、Set-AzureRMDnsRecordSet コマンドレットを使用して変更をコミットします。

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

これで、[App Service のカスタム ドメイン名の構成](../web-sites-custom-domain-name)に関するページの手順に進んで、カスタム ドメインを使用するよう Web アプリを構成できるようになりました。

## 関連項目

[DNS ゾーンの管理](../dns-operations-dnszones)

[DNS レコードの管理](../dns-operations-recordsets)

[Traffic Manager の概要](../traffic-manager-overview)

[.NET SDK を使用した Azure の操作の自動化](../dns-sdk)


 

<!---HONumber=AcomDC_1203_2015-->