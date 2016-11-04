---
title: Resource Manager で PowerShell を使用してサービスの逆引き DNS レコードを管理する方法 | Microsoft Docs
description: Resource Manager で PowerShell を使用して Azure サービスの逆引き DNS レコードまたは PTR レコードを管理する方法
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/05/2016
ms.author: smalone

---
# PowerShell を使用してサービスの逆引き DNS レコードを管理する方法
[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]

<BR>
[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]
[classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

## 逆引き DNS レコードの検証
Azure では、サード パーティがお客様の DNS ドメインにマッピングされた逆引き DNS レコードを作成できないようにするために、以下のいずれかに該当する場合のみ逆引き DNS レコードを作成できるようにしています。

* "ReverseFqdn" が、逆引き DNS で指定されたパブリック IP アドレス リソースの "Fqdn"、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの "Fqdn" と同じである (例: "ReverseFqdn" が "contosoapp1.northus.cloudapp.azure.com." である)。
* "ReverseFqdn" が、逆引き DNS で指定されたパブリック IP アドレスの名前または IP か、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの "Fqdn" または IP に対して前方解決される (例: "ReverseFqdn" が、"contosoapp1.northus.cloudapp.azure.com" の CName エイリアスの "app1.contoso.com" である)。

検証チェックが実行されるのは、パブリック IP アドレスの逆引き DNS プロパティの設定時または変更時のみです。定期的な再検証は行われません。

## 既存の Public IP Addresses への逆引き DNS の追加
既存のパブリック IP アドレスに逆引き DNS を追加するには、“Set-AzureRmPublicIpAddress” コマンドレットを使用します。

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

まだ DNS 名を持っていない既存のパブリック IP アドレスに逆引き DNS を追加する場合は、DNS 名も指定する必要があります。これは、"Set-AzureRmPublicIpAddress" コマンドレットを使用して行うことができます。

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings
    PS C:\> $pip.DnsSettings.DomainNameLabel = "contosoapp1"
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

## 逆引き DNS でのパブリック IP アドレスの作成
逆引き DNS プロパティを指定して新しいパブリック IP アドレスを追加するには、“New-AzureRmPublicIpAddress” コマンドレットを使用します。

    PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."

## 既存の Public IP Addresses の逆引き DNS の表示
既存のパブリック IP アドレスで構成されている値を確認するには、“Get-AzureRmPublicIpAddress” コマンドレットを使用します。

    PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS

## 既存のパブリック IP アドレスの逆引き DNS を削除する
既存のパブリック IP アドレスから逆引き DNS プロパティを削除するには、“Set-AzureRmPublicIpAddress” コマンドレットを使用します。この操作を行うには、ReverseFqdn プロパティの値を空白にします。

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = ""
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

[!INCLUDE [FAQ](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!---HONumber=AcomDC_0907_2016-->