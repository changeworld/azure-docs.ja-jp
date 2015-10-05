<properties 
   pageTitle="DNS ゾーンに対する操作 | Microsoft Azure" 
   description="Azure Powershell を使用して DNS ゾーンを管理できます。Azure DNS の DNS ゾーンを更新、削除、および作成する方法" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# PowerShell を使用して DNS ゾーンを管理する方法

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [Azure Powershell](dns-operations-dnszones.md)


このガイドでは、DNS ゾーンの管理方法を説明します。このガイドは、DNS ゾーンを管理するために実行する一連の操作を理解するのに役立ちます。

## 新しい DNS ゾーンの作成

ドメインをホストする新しい DNS ゾーンを作成するには、New-AzureDnsZone コマンドレットを使用します。

		PS C:\> $zone = New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

この操作により、Azure DNS に新しい DNS ゾーンが作成され、そのゾーンに対応するローカル オブジェクトが返されます。必要に応じて、Azure リソース マネージャーのタグの配列を指定することができます。詳細については、「[Etag とタグ](../dns-getstarted-create-dnszone.md#Etags-and-tags)」を参照してください。

ゾーンの名前がリソース グループ内で一意であること、および作成するゾーンがまだ存在していないことが必要です。そうでない場合、操作は失敗します。

同じゾーン名は、別のリソース グループまたは別の Azure サブスクリプション内で再利用できます。複数のゾーンで同じ名前を共有できますが、各インスタンスには異なるネーム サーバー アドレスが割り当てられます。また、親ドメインから委任できるインスタンスは 1 つだけです。詳細については、[Azure DNS へのドメインの委任](dns-domain-delegation.md)に関するページを参照してください。

## DNS ゾーンの取得

DNS ゾーンを取得するには、Get-AzureDnsZone コマンドレットを使用します。

		PS C:\> $zone = Get-AzureDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

この操作により、Azure DNS 内の既存のゾーンに対応する DNS ゾーン オブジェクトが返されます。このオブジェクトには、ゾーンに関するデータ (レコード セットの数など) が含まれますが、レコード セット自体は含まれません。

## DNS ゾーンの一覧表示
Get-AzureDnsZone からゾーン名を省略することで、リソース グループ内のすべてのゾーンを列挙できます。

	PS C:\> $zoneList = Get-AzureDnsZone -ResourceGroupName MyAzureResourceGroup
この操作により、ゾーン オブジェクトの配列が返されます。

## DNS ゾーンの更新
DNS ゾーンのリソースへの変更は、Set-AzureDnsZone を使用して行うことができます。これによって、ゾーン内の DNS レコード セットが更新されることはありません (「[DNS レコードの管理方法](dns-operations-recordsets.md)」を参照)。この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。現時点では、この操作は、ゾーンのリソースの Azure リソース マネージャーの "タグ" に限定されています。詳細については、「[Etag とタグ](dns-getstarted-create-dnszone.md#Etags-and-tags)」を参照してください。

DNS ゾーンを更新するには、次の 2 つの方法のいずれかを使用します。

### 方法 1
 
ゾーン名とリソース グループを使用してゾーンを指定します。

	PS C:\> Set-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### 方法 2
Get-AzureDnsZone の $zone オブジェクトを使用してゾーンを指定します。

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureDnsZone -Zone $zone [-Overwrite]

$zone オブジェクトで Set-AzureDnsZone を使用すると、同時変更が上書きされないように 'Etag' チェックが使用されます。オプションの "-Overwrite" スイッチを使用して、これらのチェックが実行されないように指定できます。詳細については、「[Etag とタグ](dns-getstarted-create-dnszone.md#Etags-and-tags)」を参照してください。

## DNS ゾーンの削除

DNS ゾーンは、Remove-AzureDnsZone コマンドレットを使用して削除できます。
 
Azure DNS の DNS ゾーンを削除する前に、ゾーンの作成時に自動的にゾーンのルートに作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があります。

DNS ゾーンを削除するには、次の 2 つの方法のいずれかを使用します。

### 方法 1

ゾーン名とリソース グループ名を使用してゾーンを指定します。

	PS C:\> Remove-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

この操作では、オプションの "-Force" スイッチが使用されます。これにより、DNS ゾーンを削除するかどうかの確認メッセージは表示されなくなります。
### 方法 2

Get-AzureDnsZone の $zone オブジェクトを使用してゾーンを指定します。

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureDnsZone -Zone $zone [-Force] [-Overwrite]

"-Force" スイッチは方法 1 と同じです。

"Set-AzureDnsZone" と同様に、$zone オブジェクトを使用してゾーンを指定すると、"etag" チェックによって同時変更の削除を防止することができます。<BR> オプションの "-Overwrite" フラグを使用すると、これらのチェックが行われなくなります。詳細については、「[Etag とタグ](dns-getstarted-create-dnszone.md#Etags-and-tags)」を参照してください。

ゾーン オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

	PS C:\> Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureDnsZone [-Force] [-Overwrite]

## 次のステップ


[DNS レコードの管理](dns-operations-recordsets.md)

[.NET SDK を使用した操作の自動化](dns-sdk.md)

<!---HONumber=Sept15_HO4-->