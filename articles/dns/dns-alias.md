---
title: Azure DNS エイリアス レコードの概要
description: Microsoft Azure DNS のエイリアス レコードのサポートの概要について説明します。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957574"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS エイリアス レコードの概要

Azure DNS エイリアス レコードとは、DNS ゾーン内から別の Azure リソースを参照することを可能にする DNS レコード セットの修飾子です。 たとえば、A レコードではなく Azure のパブリック IP アドレスを参照するエイリアス レコードセットを作成できます。 エイリアス レコード セットは、Azure のパブリック IP アドレスのサービス インスタンスを動的にポイントするため、エイリアス レコード セットは DNS の解決時にシームレスに更新されます。

Azure DNS ゾーンでは、エイリアスのレコードセットとして、A、AAAA、および CNAME の種類のレコードがサポートされています。 

> [!NOTE]
> Traffic Manager の A または AAAA のレコードの種類のエイリアス レコードは、外部エンドポイントの種類でのみサポートされています。 Traffic Manager の外部エンドポイントには、適切な IPv4 または IPv6 アドレス (理想的には静的な IP) を指定する必要があります。

## <a name="capabilities"></a>機能

- **DNS の A または AAAA レコード セットからパブリック IP リソースにポイントします**。 A または AAAA レコード セットを作成し、パブリック IP リソースをポイントする、エイリアスのレコード セットにします。
- **DNS の A、AAAA または CNAME レコード セットから Traffic Manager プロファイルをポイントします**。 現在は、DNS の CNAME レコードセットから Traffic Manager プロファイル (例: contoso.trafficmanager.net) の CNAME をポイントできるのに加え、DNS ゾーンの A または AAAA レコードセットから、外部エンドポイントを持つ Traffic Manager のプロファイルにポイントできるようにもなりました。
   > [!NOTE]
   > Traffic Manager の A または AAAA のレコードの種類のエイリアス レコードは、外部エンドポイントの種類でのみサポートされています。 Traffic Manager の外部エンドポイントには、適切な IPv4 または IPv6 アドレス (理想的には静的な IP) を指定する必要があります。
- **同じゾーン内に別の DNS レコード セットをポイントします**。 エイリアス レコードでは、同じ種類の別のレコード セットを参照することができます。 たとえば、DNS の CNAME レコードセットを同じ種類の別の CNAME レコードセットのエイリアスにできます。 これは、動作の観点から、一部のレコードセットをエイリアスにして、一部はエイリアスにしない場合に便利です。

## <a name="scenarios"></a>シナリオ
エイリアス レコードによくあるいくつかのシナリオを次に示します。

### <a name="prevent-dangling-dns-records"></a>未解決の DNS レコードを防ぐ
エイリアス レコードを使用すると、パブリック IP および Traffic Manager プロファイルなどの Azure のリソースのライフサイクルを Azure DNS ゾーン内で入念に追跡することができます。 従来の DNS レコードでよく発生する問題の 1 つに、特に A、AAAA または CNAME レコード タイプでの “未解決のレコード” があります。 

従来の DNS ゾーン レコードでは、ターゲットの IP または CNAME が存在しなくなった場合、DNS ゾーン レコードはその事実を知ることができず、手動で更新しなければなりませんでした。 一部の組織では、この手動での更新が間に合わない場合があることや、ロールと関連付けられているアクセス許可のレベルの分離していることが問題となっていました。

たとえば、あるアプリケーションの CNAME または IP アドレスを削除する権限があるロールに、それらのターゲットをポイントする DNS レコードを更新する権限が十分ではない場合があります。 その結果、IP または CNAME が削除されるタイミングと、それをポイントする DNS レコードが削除されるタイミングとの間に遅延が発生してしまい、エンド ユーザーに対して障害が発生してしまう場合があります。

エイリアス レコードは、このシナリオにおける複雑さを排除し、このような未解決の参照を防ぎます。 DNS レコードにパブリック IP または Traffic Manager プロファイルをポイントするエイリアス レコードとしての資格がある場合に、それらの基になるリソースが削除されると、DNS エイリアス レコードも同時に削除されます。 これにより、エンド ユーザーが障害に悩まされることは確実になくなります。

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>アプリケーションの IP の変更時に自動的に DNS ゾーンを更新する

前のシナリオと同様に、アプリケーションの移動や、基になる仮想マシンの再起動によって、基になるパブリック IP リソースの IP アドレスが変わった場合、エイリアス レコードが自動的に更新されます。 古い IP アドレスを持つ別のアプリケーションにエンド ユーザーが誘導された場合も、潜在的なセキュリティ リスクを回避できます。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>zone apex で負荷分散されたアプリケーションをホストする

DNS プロトコルは、zone apex (例: contoso.com) に A または AAAA レコード以外が割り当てられないようにします。 zone apex のレコードから Traffic Manager のプロファイルをポイントすることはできなかったので、これはアプリケーション所有者のアプリケーションが Traffic Manager の背後で負荷が分散されるものである場合、問題になります。 そのため、アプリケーション所有者は解決策を使用しなければなりませんでした。 たとえば、zone apex からの別のドメインへ (contoso.com から www.contoso.com へ) リダイレクトするアプリケーション層でのリダイレクトです。 これは、リダイレクト機能の観点からの単一障害点があることを表します。

エイリアス レコードを使用すると、この問題がなくなります。 アプリケーションの所有者は、zone apex のレコードを、外部エンドポイントを持つ Traffic Manager プロファイルにポイントさせることができます。 この機能により、DNS ゾーン内のその他の任意のドメインで使用されているのと同じ Traffic Manager プロファイルをアプリケーションの所有者はポイントできるようになります。 たとえば、Traffic Manager プロファイルへの構成が外部エンドポイントのみである場合、contoso.com と www.contoso.com はいずれも同じ Traffic Manager プロファイルをポイントできます。

## <a name="next-steps"></a>次の手順

エイリアス レコードの詳細については、次の記事を参照してください。

- [チュートリアル: Azure パブリック IP アドレスを参照するエイリアス レコードを構成する](tutorial-alias-pip.md)
- [チュートリアル: Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する](tutorial-alias-tm.md)
- [DNS に関する FAQ](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
