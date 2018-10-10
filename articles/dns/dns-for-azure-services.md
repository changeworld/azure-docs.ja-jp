---
title: 他の Azure サービスと共に Azure DNS を使用する | Microsoft Docs
description: Azure DNS を使用して 他の Azure サービスの名前を解決する方法を理解します
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: 5d9886e16aa1921963f3d91d0fbd4da9287f7e54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989144"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Azure DNS を他の Azure サービスと使用する方法

Azure DNS は、ホストされる DNS 管理および名前解決サービスです。 このサービスによって、Azure にデプロイしている他のアプリケーションやサービスのパブリック DNS 名を作成できます。 カスタム ドメイン内の Azure サービスの名前の作成は、サービスに適した種類のレコードを追加するだけの単純な作業です。

* IP アドレスが動的に割り当てられる場合は、Azure でサービスに対して作成された DNS 名にマップされる DNS CNAME レコードを作成できます。 DNS の標準によりゾーンの頂点で CNAME レコードを使用することはできませんが、代わりにエイリアス レコードを使用できます。 詳細については、「[チュートリアル: Azure パブリック IP アドレスを参照するエイリアス レコードを構成する](tutorial-alias-pip.md)」を参照してください。
* IP アドレスが静的に割り当てられる場合は、任意の名前を使用して DNS A レコードを作成でき、ゾーンの頂点で *ネイキッド ドメイン* を作成することもできます。

次の表は、さまざまな Azure サービスで使用できる、サポートされているレコードの種類の要点を示しています。 この表からわかるように、Azure DNS は、インターネットに接続するネットワーク リソースの DNS レコードのみをサポートします。 Azure DNS は、内部アドレスやプライベート アドレスの名前解決のために使用することはできません。

| Azure サービス | ネットワーク インターフェイス | 説明 |
| --- | --- | --- |
| Application Gateway |[フロントエンド パブリック IP](dns-custom-domain.md#public-ip-address) |DNS A または CNAME レコードを作成できます。 |
| Load Balancer |[フロントエンド パブリック IP](dns-custom-domain.md#public-ip-address)  |DNS A または CNAME レコードを作成できます。 Load Balancer には、動的に割り当てられる IPv6 パブリック IP アドレスを設定できます。 したがって、IPv6 アドレス用の CNAME レコードを作成する必要があります。 |
| Traffic Manager |パブリック名 |Traffic Manager プロファイルに割り当てられている trafficmanager.net 名にマップされるエイリアス レコードのみを作成できます。 詳細については、「[Tutorial: Configure an alias record to support apex domain names with Traffic Manager (チュートリアル: Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する)](tutorial-alias-tm.md)」を参照してください。 |
| クラウド サービス |[パブリック IP](dns-custom-domain.md#public-ip-address) |静的に割り当てられる IP アドレスには、DNS A レコードを作成できます。 動的に割り当てられる IP アドレスには、 *cloudapp.net* 名にマップする CNAME レコードを作成する必要があります。|
| App Service | [外部 IP](dns-custom-domain.md#app-service-web-apps) |外部 IP アドレスには、DNS A レコードを作成できます。 それ以外の場合は、azurewebsites.net 名にマップする CNAME レコードを作成する必要があります。 詳細については、「 [Azure アプリへのカスタム ドメイン名のマッピング](../app-service/app-service-web-tutorial-custom-domain.md) |
| Resource Manager VM |[パブリック IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM はパブリック IP アドレスを持つことができます。 パブリック IP アドレスを持つ VM は、ロード バランサーの背後に配置されることもあります。 パブリック アドレスの DNS A、CNAME、またはエイリアス レコードを作成できます。 このカスタム名を使用して、ロード バランサーの VIP をバイパスできます。 |
| クラシック VM |[パブリック IP](dns-custom-domain.md#public-ip-address) |PowerShell または CLI は使用して作成したクラシック VM には、動的または静的 (予約済み) 仮想アドレスを構成できます。 それぞれに DNS CNAME または A レコードを作成できます。 |
