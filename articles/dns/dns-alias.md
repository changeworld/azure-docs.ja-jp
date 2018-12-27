---
title: Azure DNS エイリアス レコードの概要
description: Microsoft Azure DNS のエイリアス レコードのサポートの概要について説明します。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092844"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS エイリアス レコードの概要

Azure DNS エイリアス レコードは、DNS レコード セットを修飾するものです。 それらは、DNS ゾーン内で他の Azure リソースを参照できます。 たとえば、A レコードではなく Azure のパブリック IP アドレスを参照するエイリアス レコード セットを作成できます。 エイリアス レコード セットは、Azure のパブリック IP アドレスのサービス インスタンスを動的にポイントします。 その結果、エイリアス レコード セットは、DNS の解決時にシームレスに更新されます。

Azure DNS ゾーンでは、エイリアス レコード セットとして、次の種類のレコードがサポートされます。 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Azure Traffic Manager での A または AAAA のレコードの種類に対するエイリアス レコードは、外部エンドポイントの種類でのみサポートされます。 Traffic Manager の外部エンドポイントには、必要に応じて IPv4 または IPv6 アドレスを指定する必要があります。 理想的なのは、アドレスの静的 IP アドレスを使用することです。

## <a name="capabilities"></a>機能

- **DNS の A または AAAA レコード セットからパブリック IP リソースにポイントする**。 A または AAAA レコード セットを作成し、パブリック IP リソースをポイントするエイリアス レコード セットにすることができます。

- **DNS の A、AAAA または CNAME レコード セットから Traffic Manager プロファイルをポイントする**。 DNS の CNAME レコード セットから Traffic Manager プロファイルの CNAME をポイントできます。 一例は contoso.trafficmanager.net です。 DNS ゾーン内の A または AAAA レコード セットから、外部エンドポイントがある Traffic Manager プロファイルをポイントすることもできます。

   > [!NOTE]
   > Traffic Manager での A または AAAA のレコードの種類に対するエイリアス レコードは、外部エンドポイントの種類でのみサポートされます。 Traffic Manager の外部エンドポイントには、必要に応じて IPv4 または IPv6 アドレスを指定する必要があります。 理想的なのは、アドレスの静的 IP アドレスを使用することです。
   
- **同じゾーン内の別の DNS レコード セットをポイントする**。 エイリアス レコードでは、同じ種類の別のレコード セットを参照できます。 たとえば、DNS の CNAME レコード セットは、同じ種類の別の CNAME レコード セットのエイリアスになることができます。 この配置は、一部のレコード セットをエイリアスにしたり、一部をエイリアスにしたくない場合に便利です。

## <a name="scenarios"></a>シナリオ
エイリアス レコードが一般的に使用されるいくつかのシナリオを次に示します。

### <a name="prevent-dangling-dns-records"></a>未解決の DNS レコードを防ぐ
 エイリアス レコードを使用すると、Azure リソースのライフサイクルを Azure DNS ゾーン内で詳細に追跡できます。 リソースには、パブリック IP や Traffic Manager プロファイルが含まれます。 従来の DNS レコードでよくある問題は、未解決のレコードです。 この問題は、A、AAAA、または CNAME のレコードの種類で特に発生します。 

従来の DNS ゾーン レコードでは、ターゲットの IP または CNAME が存在しなくなっても、DNS ゾーン レコードはそのことを認識できません。 その結果、レコードを手動で更新する必要があります。 組織によっては、この手動の更新が適切なタイミングで実行されないことがあります。 ロールとそれに関連付けられるアクセス許可レベルが分離していることも、問題になる可能性があります。

たとえば、ロールには、アプリケーションに属する CNAME または IP アドレスを削除する権限があるとします。 ただし、それらのターゲットをポイントする DNS レコードを更新するための十分な権限はありません。 IP または CNAME の削除と、それをポイントしている DNS レコードの削除の間に時間遅延が発生します。 この時間遅延によって、ユーザーに対して障害が発生する可能性があります。

エイリアス レコードは、このシナリオに関連する複雑さを排除します。 それらは、未解決の参照を防ぐのに役立ちます。 たとえば、パブリック IP または Traffic Manager プロファイルをポイントするエイリアス レコードとして修飾されている DNS レコードを考えてみましょう。 これらの基になるリソースが削除されると、DNS のエイリアス レコードも同時に削除されます。 このプロセスにより、ユーザーが障害を経験することがなくなります。

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>アプリケーションの IP の変更時に自動的に DNS ゾーンを更新する

このシナリオは、前のシナリオに似ています。 アプリケーションが移動されたか、基になる仮想マシンが再起動されたとします。 基になるパブリック IP リソースの IP アドレスが変更されると、エイリアス レコードは自動的に更新されます。 潜在的なセキュリティ リスクを回避するために、古い IP アドレスを持つ別のアプリケーションにユーザーを誘導します。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Zone Apex で負荷分散されたアプリケーションをホストする

DNS プロトコルでは、Zone Apex に A または AAAA レコード以外を割り当てることはできません。 一例として contoso.com があります。 Traffic Manager の背後でアプリケーションの負荷分散を行っているアプリケーションの所有者にとっては、この制限によって問題が生じます。 Zone Apex レコードから Traffic Manager プロファイルをポイントすることができません。 そのため、アプリケーションの所有者は回避策を使用する必要があります。 アプリケーション層でのリダイレクトで、Zone Apex から別のドメインにリダイレクトする必要があります。 一例が、contoso.com から www.contoso.com へのリダイレクトです。 この配置は、リダイレクト機能の単一障害点を示します。

エイリアス レコードを使用すると、この問題がなくなります。 アプリケーションの所有者は、Zone Apex のレコードで、外部エンドポイントを持つ Traffic Manager プロファイルをポイントできます。 アプリケーションの所有者は、DNS ゾーン内の他のドメインで使用されているのと同じ Traffic Manager プロファイルをポイントできます。 たとえば、contoso.com と www.contoso.com で、同じ Traffic Manager プロファイルをポイントできます。 これは、Traffic Manager に外部エンドポイントのみが構成されている場合に限ります。

## <a name="next-steps"></a>次の手順

エイリアス レコードの詳細については、次の記事を参照してください。

- [チュートリアル: Azure パブリック IP アドレスを参照するエイリアス レコードを構成する](tutorial-alias-pip.md)
- [チュートリアル: Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する](tutorial-alias-tm.md)
- [DNS に関する FAQ](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
