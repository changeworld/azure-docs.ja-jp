<properties 
   pageTitle="Resource Manager で PowerShell を使用してサービスの逆引き DNS レコードを管理する方法 |Microsoft Azure"
   description="Resource Manager で PowerShell を使用して Azure サービスの逆引き DNS レコードまたは PTR レコードを管理する方法"
   services="DNS"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/16/2016"
   ms.author="joaoma" />

# PowerShell を使用してサービスの逆引き DNS レコードを管理する方法

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).


## 既存のパブリック IP アドレスに逆引き DNS レコードを追加する
既存のパブリック IP アドレスに逆引き DNS を追加するには、“Set-AzureRmPublicIpAddress” コマンドレットを使用します。

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip 

## 逆引き DNS レコードを使用して新しいパブリック IP アドレスを作成する
逆引き DNS プロパティを指定して新しいパブリック IP アドレスを追加するには、“New-AzureRmPublicIpAddress” コマンドレットを使用します。

	PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
 
## 既存のパブリック IP アドレスの逆引き DNS レコードを確認する
既存のパブリック IP アドレスで構成されている値を確認するには、“Get-AzureRmPublicIpAddress” コマンドレットを使用します。

	PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS 

## 既存のパブリック IP アドレスから逆引き DNS レコードを削除する
既存のパブリック IP アドレスから逆引き DNS プロパティを削除するには、“Set-AzureRmPublicIpAddress” コマンドレットを使用します。この操作を行うには、ReverseFqdn プロパティの値を空白にします。

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = ""
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip 

## 逆引き DNS レコードの検証 
Azure では、サード パーティがお客様の DNS ドメインにマッピングされた逆引き DNS レコードを作成できないようにするために、以下のいずれかに該当する場合のみ逆引き DNS レコードを作成できるようにしています。

- “ReverseFqdn” が、逆引き DNS で指定されたパブリック IP アドレス リソースの “Fqdn”、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの “Fqdn” と同じである (例: “ReverseFqdn” が “contosoapp1.northus.cloudapp.azure.com.”)。

- “ReverseFqdn” が、逆引き DNS で指定されたパブリック IP アドレスの名前または IP か、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの “Fqdn” または IP に対して前方解決される (例: “ReverseFqdn” が、“contosoapp1.northus.cloudapp.azure.com” の CName エイリアスの “app1.contoso.com” である)。 検証チェックが実行されるのは、パブリック IP アドレスの逆引き DNS プロパティの設定時または変更時のみです。定期的な再検証は行われません。

[AZURE.INCLUDE [FAQ](../../includes/dns-reverse-dns-record-operations-faq-include.md)]

<!---HONumber=AcomDC_0309_2016-->