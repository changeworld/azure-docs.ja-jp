---
title: サービス構成ファイルでの DNS 設定の指定 | Microsoft Docs
description: 仮想ネットワークのサービス構成ファイルを使用してカスタム DNS 設定を指定する
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247179"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>サービス構成ファイルでの DNS 設定の指定
## <a name="dns-elements"></a>DNS 要素
サービス構成ファイルでは、サービスが使用するドメイン ネーム システム (DNS) サーバーの IPv4 アドレスのリストである DnsServers 要素を指定できます。 サービス構成ファイルの設定は、ネットワーク構成ファイルの設定より優先されます。 詳細については、「 [Microsoft Azure サービスの構成スキーマ (.cscfg ファイル)](https://msdn.microsoft.com/library/azure/ee758710.aspx)」を参照してください。

**NetworkConfiguration 要素**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> **DnsServer** 要素の **name** 属性は、参照名としてのみ使用されます。 DNS サーバーのホスト名を表してはいません。 各 **DnsServer** 属性の値は、Microsoft Azure サブスクリプション全体で一意である必要があります。
> 
> 

## <a name="see-also"></a>関連項目
[Azure サービス構成スキーマ (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure Virtual Network の構成スキーマ](https://go.microsoft.com/fwlink/?LinkId=248093)

[ネットワーク構成ファイルを使用した Virtual Network の構成](https://go.microsoft.com/fwlink/?LinkId=248094)

[管理ポータルでの Virtual Network の設定について](https://go.microsoft.com/fwlink/?LinkId=248092)

