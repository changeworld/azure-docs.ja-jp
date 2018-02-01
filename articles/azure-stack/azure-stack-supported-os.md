---
title: "Azure Stack でサポートされているゲスト オペレーティング システム | Microsoft Docs"
description: "これらのゲスト オペレーティング システムを Azure Stack で使用できます。"
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: c9f5bee38772623fb79fa081be8eaece981cc8ab
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack でサポートされているゲスト オペレーティング システム

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

## <a name="windows"></a>Windows
Azure Stack は、次の Windows ゲスト オペレーティング システムをサポートしています。 Marketplace のイメージは、Azure Stack にダウンロードできます。 Marketplace では、Windows クライアント イメージを利用できません。

デプロイ時に、Azure Stack により、適切なバージョンのゲスト エージェントがイメージに挿入されます。

| オペレーティング システム | [説明] | 発行元 | [OS Type]\(OS の種類\) | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 ビット | Microsoft | Windows | データセンター |
| Windows Server 2012 | 64 ビット | Microsoft | Windows | データセンター |
| Windows Server 2012 R2 | 64 ビット | Microsoft | Windows | データセンター |
| Windows Server 2016 | 64 ビット | Microsoft | Windows | Datacenter、Datacenter Core、Datacenter with Containers |
| Windows 7 | 64 ビット、Pro および Enterprise | Microsoft | Windows | いいえ  |
| Windows 8.1 | 64 ビット、Pro および Enterprise | Microsoft | Windows | いいえ  |
| Windows 10 *(注 1 をご覧ください)* | 64 ビット、Pro および Enterprise | Microsoft | Windows | いいえ  |

***注 1:***  *Azure Stack で Windows 10 クライアント オペレーティング システムを展開するには、[Windows per User Licensing](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) を所持しているか、Qualified Multitenant Hoster ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)) から購入する必要があります。*


## <a name="linux"></a>Linux

ここに示す Linux ディストリビューションには、必要な Windows Azure Linux エージェント (WALA) が含まれます。

> [!NOTE]   
> 2.2.3 よりも古いバージョンの WALA でビルドされたイメージはサポートされて "*おらず*"、デプロイできる可能性は低いです。 バージョン 2.2.12 や 2.2.13 など、一部の WALA エージェント バージョンは、Azure Stack VM 上では動作しないことがわかっています。


| ディストリビューション | [説明] | 発行元 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Container Linux |  64 ビット | CoreOS | 安定版 |
| CentOS-based 6.9 | 64 ビット | Rogue Wave | [はい] |
| CentOS-based 7.3 | 64 ビット | Rogue Wave | [はい] |
| CentOS-based 7.4 | 64 ビット | Rogue Wave | [はい] |
| Debian 8 "Jessie" | 64 ビット | credativ |  [はい] |
| Debian 9 "Stretch" | 64 ビット | credativ | [はい] |
| Oracle Linux | 64 ビット | Oracle | いいえ  |
| Red Hat Enterprise Linux 7.x | 64 ビット | Red Hat | いいえ  |
| SLES 11SP4 | 64 ビット | SUSE | [はい] |
| SLES 12SP3 | 64 ビット | SUSE | [はい] |
| Ubuntu 14.04-LTS | 64 ビット | Canonical | [はい] |
| Ubuntu 16.04-LTS | 64 ビット | Canonical | [はい] |

他の Linux ディストリビューションは、今後サポートされる可能性があります。
