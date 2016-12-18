---
title: "PowerShell を使用して Azure (クラシック) サービスの逆引き DNS レコードを管理する | Microsoft Docs"
description: "クラシック デプロイ モデルで PowerShell を使用して Azure サービスの逆引き DNS レコードまたは PTR レコードを管理する方法。 "
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 7d640b2ffe600ce84e2ec867cd29930b26dab908
ms.openlocfilehash: 1b5f6fa50a0a5a98d8eb5ecc7aeaa632e1183c38


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Azure PowerShell を使用して Azure サービス (クラシック) の逆引き DNS レコードを管理する方法

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)][Resource Manager モデルを使用してこれらの手順を実行する](dns-reverse-dns-record-operations-ps.md)方法について説明します。

## <a name="validation-of-reverse-dns-records"></a>逆引き DNS レコードの検証
Azure では、サード パーティがお客様の DNS ドメインにマッピングされた逆引き DNS レコードを作成できないようにするために、以下のいずれかに該当する場合のみ逆引き DNS レコードを作成できるようにしています。

* 逆引き DNS の FQDN が、その逆引き DNS で指定されたクラウド サービスの名前か、または同じサブスクリプションに含まれるいずれかのクラウド サービスの名前である (例: 逆引き DNS が "contosoapp1.cloudapp.net" である)。
* 逆引き DNS の FQDN が、その逆引き DNS で指定されたクラウド サービスの名前または IP か、同じサブスクリプションに含まれるいずれかのクラウド サービスの名前または IP に対して前方解決される (例: 逆引き DNS が、contosoapp1.cloudapp.net の CName エイリアスの "app1.contoso.com" である )。

検証チェックが実行されるのは、クラウド サービスの逆引き DNS プロパティの設定時または変更時のみです。 定期的な再検証は行われません。

## <a name="add-reverse-dns-to-existing-cloud-services"></a>既存の Cloud Services への逆引き DNS の追加
既存のクラウド サービスに逆引き DNS レコードを追加するには、“Set-AzureService” コマンドレットを使用します。

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>逆引き DNS によるクラウド サービスの作成
逆引き DNS プロパティを指定して新しいクラウド サービスを追加するには、“Set-AzureService” コマンドレットを使用します。

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>既存の Cloud Services の逆引き DNS の表示
既存のクラウド サービスで構成されている値を確認するには、“Get-AzureService” コマンドレットを使用します。

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>既存の Cloud Services からの逆引き DNS の削除
既存のクラウド サービスから逆引き DNS プロパティを削除するには、“Set-AzureService” コマンドレットを使用します。 この操作を行うには、逆引き DNS プロパティの値を空白にします。

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]




<!--HONumber=Nov16_HO3-->


