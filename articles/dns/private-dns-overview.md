---
title: "プライベート ドメインに Azure DNS を使用する |Microsoft ドキュメント"
description: "Microsoft Azure のプライベート DNS ホスティング サービスの概要です。"
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>プライベート ドメインに Azure DNS を使用する
ドメイン ネーム システム (DNS) は、サービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。  Azure DNS では、インターネットに接続する DNS ドメインだけでなく、プライベート DNS ドメインもプレビュー機能としてサポートするようになりました。  
 
Azure DNS は、信頼性が高くセキュリティで保護された DNS サービスを提供し、カスタムの DNS ソリューションを追加する必要なしで、仮想ネットワークでドメイン名を管理および解決します。 プライベート DNS ゾーンを使用すると、現在利用可能な Azure で提供される名前ではなく、独自のカスタム ドメイン名を使用できます。  カスタム ドメイン名の使用は、組織のニーズに最適な仮想ネットワーク アーキテクチャを調整するのに役立ちます。 仮想ネットワーク内、および仮想ネットワーク間の、VM の名前解決を提供します。 さらに、水平分割ビューでゾーン名を構成できるので、プライベート DNS ゾーンとパブリック DNS ゾーンで同じ名前を共有することもできます。

![DNS の概要](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>メリット

* **カスタムの DNS ソリューションの必要がなくなります。** 以前は、多くのお客様は、仮想ネットワーク内の DNS ゾーンを管理するためにカスタムの DNS ソリューションを作成していました。  DNS ゾーンの管理は、Azure のネイティブ インフラストラクチャを使用してできるようになりました。これにより、カスタムの DNS ソリューションの作成と管理の負担がなくなります。

* **すべての一般的な DNS レコードの種類を使用します。**  Azure DNS では、A、AAAA、CNAME、MX、NS、PTR、SOA、SRV、TXT のレコードをサポートしています。

* **自動のホスト名レコード管理。** Azure では、カスタムの DNS レコードをホストすると共に、指定された仮想ネットワーク内の VM のホスト名レコードを自動的に維持します。  これにより、カスタムの DNS ソリューションの作成またはアプリケーションの変更の必要なしで、使用するドメイン名を最適化することができます。

* **仮想ネットワーク間のホスト名解決。** Azure 提供のホスト名とは異なり、プライベート DNS ゾーンは仮想ネットワーク間で共有できます。  この機能により、仮想ネットワークのピアリングなどのネットワーク間とサービス検索のシナリオが簡略化されます。

* **使い慣れたツールとユーザー エクスペリエンス。** 習得に要する時間を短縮するため、この新しい機能では既に確立された Azure DNS ツール (PowerShell、Resource Manager テンプレート、REST API) が使われています。

* **水平分割 DNS サポート。** Azure DNS では、同じ名前でゾーンを作成し、それが仮想ネットワーク内からとパブリック インターネットからとで異なる回答に解決されるようにすることができます。  水平分割 DNS の一般的なシナリオでは、仮想ネットワーク内で使用する専用のバージョンのサービスを提供します。


## <a name="pricing"></a>価格

プライベート DNS ゾーンは、管理プレビュー期間中は無料です。 一般提供期間中、この機能は既存の Azure DNS オファリングに類似した、使用に基づく料金モデルを使います。 


## <a name="next-steps"></a>次のステップ

Azure DNS で[プライベート DNS ゾーンを作成する](./private-dns-getstarted-powershell.md)方法を学びます。

「[DNS ゾーンとレコードの概要](dns-zones-records.md)」でDNS ゾーンとレコードについて学びます。

Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。

