---
title: App Service Environment のネットワーク
description: App Service Environment のネットワークの詳細
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 06/30/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89a14dc204e10231a134477650081396fc8e433f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723038"
---
# <a name="app-service-environment-networking"></a>App Service Environment のネットワーク

> [!NOTE]
> これは、Isolated v2 App Service プランで使用される App Service Environment v3 に関する記事です
> 


App Service Environment (ASE) は、Web アプリ、API アプリ、および関数アプリがホストされる Azure App Service のシングル テナント デプロイです。 ASE をインストールするときに、デプロイする Azure Virtual Network (VNet) を選択します。 すべての受信および送信トラフィック アプリケーションは、指定した VNet 内に配置されます。 ASE は、VNet 内の 1 つのサブネットにデプロイされます。 それと同じサブネットには、他の何もデプロイできません。 このサブネットは Microsoft.Web/HostingEnvironments に委任する必要があります

## <a name="addresses"></a>アドレス 

ASE は作成時に以下の情報を持つことになります。

| アドレスの種類 | description |
|--------------|-------------|
| ASE 仮想ネットワーク | ASE のデプロイ先 VNet |
| ASE サブネット | ASE のデプロイ先サブネット |
| ［ドメイン サフィックス］ | この ASE 内に作成されたアプリによって使用されるドメイン サフィックス |
| 仮想 IP | これは、ASE によって使用される VIP の種類です。 有効な値は、内部と外部の 2 つです。 |
| 受信アドレス | 受信アドレスは、この ASE 上のアプリに到達する場所のアドレスです。 内部 VIP がある場合は、ASE サブネット内の 1 つのアドレスです。 アドレスが外部である場合は、パブリックに公開されるアドレスとなります |
| 既定の送信アドレス | この ASE 内のアプリでは、インターネットへの送信呼び出しを行うときに、既定でこのアドレスが使用されます。 |

ASEv3 では、ASE ポータルの **[IP アドレス]** 部分に ASE によって使用されるアドレスに関する詳細が含まれています。

![ASE アドレスの UI](./media/networking/networking-ip-addresses.png)

ASE で App Service プランをスケーリングする際には、ASE サブネットのアドレスをより多く使用することになります。 使用されるアドレスの数は、使用している App Service プランのインスタンス数と、ASE でどれだけのトラフィックを受信しているかに基づいて変動します。 ASE 内のアプリには、ASE サブネット内での専用アドレスはありません。 ASE サブネット内でアプリによって使用される実際のアドレスは、時間の経過と共に変化します。

## <a name="ports"></a>ポート

ASE では、ポート 80 と 443 でアプリケーション トラフィックが受信されます。 それらのポートがブロックされている場合は、アプリに到達できません。 

> [!NOTE]
> ロード バランサーと ASE インフラストラクチャとの間でキープ アライブ トラフィックを維持するには、AzureLoadBalancer から ASE サブネットに至るポート 80 を許可する必要があります。 ASE 仮想 IP へのポート 80 トラフィックは引き続き制御可能です。
> 

## <a name="extra-configurations"></a>追加の構成

ネットワーク セキュリティ グループ (NSG) とルート テーブル (UDR) を制限なしに設定できます。 ASE から、Azure Firewall などのエグレス ファイアウォール デバイスへのすべての送信トラフィックを強制的にトンネリングすることがで、アプリケーションの依存関係以外のことを心配する必要がありません。 ASE への受信トラフィックの前に、Application Gateway などの WAF デバイスを配置し、その ASE 上の特定のアプリを公開できます。 インターネットへの異なる専用送信アドレスについては、ASE と共に NAT Gateway を使用できます。 ASE と共に NAT Gateway を使用するには、ASE サブネットに対する NAT Gateway を構成します。 

## <a name="dns"></a>DNS

### <a name="dns-configuration-to-your-ase"></a>ASE に対する DNS 構成

外部 VIP のある ASE が作成された場合、アプリは自動的にパブリック DNS に登録されます。 内部 VIP のある ASE が作成された場合は、それのために DNS を構成する必要が生じることがあります。 ASE の作成中に Azure DNS プライベート ゾーンが自動的に構成されるようにすることを選択した場合は、ASE VNet 内に DNS が構成されます。 DNS を手動で構成することを選択した場合は、独自の DNS サーバーを使用するか、Azure DNS プライベート ゾーンを構成する必要があります。 ASE の受信アドレスを見つけるには、 **[ASE ポータル] > [IP アドレス]** UI に移動します。 

独自の DNS サーバーを使用する場合は、以下のレコードを追加する必要があります。

1. &lt;ASE 名&gt;.appserviceenvironment.net 用のゾーンを作成する
1. そのゾーン内で、* に ASE で使用される受信 IP アドレスを指し示す A レコードを作成する
1. そのゾーン内で、@ に ASE で使用される受信 IP アドレスを指し示す A レコードを作成する
1. &lt;ASE 名&gt;.appserviceenvironment.net に scm という名前のゾーンを作成する
1. scm ゾーン内で、* に ASE プライベート エンドポイントで使用される IP アドレスを指し示す A レコードを作成する

Azure DNS プライベート ゾーンで DNS を構成するには、次の操作を行ってください。

1. `<ASE-name>.appserviceenvironment.net` という名前の Azure DNS プライベート ゾーンを作成する
1. そのゾーン内で、* に受信 IP アドレスを指し示す A レコードを作成する
1. そのゾーン内で、@ に受信 IP アドレスを指し示す A レコードを作成する
1. そのゾーン内で、*.scm に受信 IP アドレスを指し示す A レコードを作成する

ASE の既定のドメイン サフィックスの DNS 設定では、それらの名前によってのみアプリにアクセスできるよう制限されません。 ASE では、アプリの検証なしでカスタム ドメイン名を設定できます。 その後、*contoso.net* という名前のゾーンを作成する場合はそれを行って、受信 IP アドレスを指すようにすることができます。 カスタム ドメイン名はアプリ要求に対して機能しますが、scm サイトでは使用できません。 scm サイトは、 *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* でのみ使用できます。 

#### <a name="dns-configuration-from-your-ase"></a>ASE からの DNS の構成

ASE 内のアプリでは、VNet の構成で使用されている DNS が使用されます。 一部のアプリで、VNet が構成されているものとは異なる DNS サーバーを使用するようにしたい場合は、アプリ設定 WEBSITE_DNS_SERVER と WEBSITE_DNS_ALT_SERVER を使用して、アプリごとに手動でそれを設定できます。 アプリの設定 WEBSITE_DNS_ALT_SERVER により、セカンダリ DNS サーバーが構成されます。 セカンダリ DNS サーバーは、プライマリ DNS サーバーからの応答がない場合にのみ使用されます。 

## <a name="limitations"></a>制限事項

ASE は確かに顧客の VNet にデプロイされますが、ASE では使用できないネットワーク機能がいくつか存在します。 

* SMTP トラフィックの送信。 メールでトリガーされるアラートは引き続き使用できますが、アプリからはポート 25 で送信トラフィックを送信できません
* 送信トラフィックを監視するための Network Watcher または NSG フローの使用

## <a name="more-resources"></a>その他のリソース

- [環境変数とアプリ設定のリファレンス](../reference-app-settings.md)