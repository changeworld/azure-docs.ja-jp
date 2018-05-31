---
title: Azure Stack の iDNS について | Microsoft Docs
description: Azure Stack の iDNS の機能について
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: df9c22877eeac381d936f2fb86f5720c9cc9c930
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196697"
---
# <a name="introducing-idns-for-azure-stack"></a>Azure Stack 用の iDNS の概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

iDNS は、外部 DNS 名 (例: http://www.bing.com.) を解決することができる、Azure Stack のネットワーク機能です。内部仮想ネットワークの名前を登録することもできます。 これを行うことで、同じ仮想ネットワーク内の VM を IP アドレスではなく名前で解決することができます。 このアプローチでは、カスタム DNS サーバー エントリを用意する必要がありません。 DNS の詳細については、「[Azure DNS の概要](https://docs.microsoft.com/en-us/azure/dns/dns-overview)」を参照してください。

## <a name="what-does-idns-do"></a>iDNS が実行すること

Azure Stack の iDNS を使用して、カスタム DNS サーバー エントリの指定なしで次の機能を取得できます。

* テナントのワークロードに対する共有 DNS 名前解決サービス。
* テナントの仮想ネットワーク内で名前解決と DNS 登録を行う権限がある DNS サービス。
* テナントの VM からインターネット名を解決する再帰的な DNS サービス。 テナントは、インターネット名 (例: www.bing.com) を解決するためにカスタム DNS エントリを指定する必要はもうありません。

独自の DNS とカスタム DNS サーバーを使用することもできます。 しかし iDNS を使用すれば、インターネットの DNS 名を解決することに加え、同じ仮想ネットワーク内の他の VM に接続することもでき、カスタム DNS エントリを作成する必要がありません。

## <a name="what-doesnt-idns-do"></a>iDNS が実行しないこと

iDNS で実行できないことは、仮想ネットワークの外部から解決できる名前の DNS レコードを作成することです。

Azure には、パブリック IP アドレスに関連付けられる DNS 名ラベルを指定するオプションがあります。 ラベル (プレフィックス) は自分で選択できますが、サフィックスは、パブリック IP アドレスを作成するリージョンに基づいて Azure が選択します。

![DNS 名ラベルの例](media/azure-stack-understanding-dns-in-tp2/image3.png)

前の画像が示すように、Azure は、指定された DNS の名前ラベルを持つ **westus.cloudapp.azure.com** ゾーンの DNS に "A" レコードを作成します。プレフィックスとサフィックスが組み合わされて、パブリック インターネットの任意の場所で解決できる[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) を構成します。

Azure Stack では、内部名の登録でのみ iDNS をサポートするため、以下を行うことはできません。

* 既存のホストされている DNS ゾーン (例: local.azurestack.external) に DNS レコードを作成する。
* DNS ゾーン (Contoso.com など) を作成する。
* 独自のカスタム DNS ゾーンにレコードを作成する。
* ドメイン名の購入をサポートする。

## <a name="next-steps"></a>次の手順

[Azure Stack の DNS](azure-stack-dns.md)