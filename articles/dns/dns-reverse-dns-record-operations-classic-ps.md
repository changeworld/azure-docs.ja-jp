<properties 
   pageTitle="クラシック デプロイ モデルで PowerShell を使用してサービスの逆引き DNS レコードを管理する方法 | Microsoft Azure"
   description="クラシック デプロイ モデルで PowerShell を使用して Azure サービスの逆引き DNS レコードまたは PTR レコードを管理する方法。"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# PowerShell を使用してサービス (クラシック) の逆引き DNS レコードを管理する方法

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](dns-reverse-dns-record-operations-ps.md).

## 逆引き DNS レコードの検証
Azure では、サード パーティがお客様の DNS ドメインにマッピングされた逆引き DNS レコードを作成できないようにするために、以下のいずれかに該当する場合のみ逆引き DNS レコードを作成できるようにしています。

- 逆引き DNS の FQDN が、その逆引き DNS で指定されたクラウド サービスの名前か、または同じサブスクリプションに含まれるいずれかのクラウド サービスの名前である (例: 逆引き DNS が “contosoapp1.cloudapp.net” である)。
- 逆引き DNS の FQDN が、その逆引き DNS で指定されたクラウド サービスの名前または IP か、同じサブスクリプションに含まれるいずれかのクラウド サービスの名前または IP に対して前方解決される (例: 逆引き DNS が、contosoapp1.cloudapp.net の CName エイリアスの “app1.contoso.com” である)。

検証チェックが実行されるのは、クラウド サービスの逆引き DNS プロパティの設定時または変更時のみです。定期的な再検証は行われません。

## 既存の Cloud Services への逆引き DNS の追加
既存のクラウド サービスに逆引き DNS レコードを追加するには、“Set-AzureService” コマンドレットを使用します。

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## 逆引き DNS によるクラウド サービスの作成  
逆引き DNS プロパティを指定して新しいクラウド サービスを追加するには、“Set-AzureService” コマンドレットを使用します。

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## 既存の Cloud Services の逆引き DNS の表示
既存のクラウド サービスで構成されている値を確認するには、“Get-AzureService” コマンドレットを使用します。

	PS C:\> Get-AzureService "contosoapp1"

## 既存の Cloud Services からの逆引き DNS の削除
既存のクラウド サービスから逆引き DNS プロパティを削除するには、“Set-AzureService” コマンドレットを使用します。この操作を行うには、逆引き DNS プロパティの値を空白にします。

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!---HONumber=AcomDC_0330_2016-->