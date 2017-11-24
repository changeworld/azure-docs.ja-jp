---
title: "Azure App Service Environment の強制トンネリングを構成する"
description: "送信トラフィックが強制トンネリングされている場合に ASE が機能するように設定します"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>強制トンネリングを使用した App Service Environment の構成

App Service Environment (ASE) は、ユーザーの Azure 仮想ネットワーク (VNet) 内に Azure App Service をデプロイしたものです。 多くのユーザーは、VPN または ExpressRoute 接続を使用して、VNet をオンプレミス ネットワークの拡張網として構成しています。 これらのユーザーは、企業のポリシーやその他のセキュリティ上の制約から、すべての送信トラフィックを、インターネットに向かう前にオンプレミスに送るようにルートを構成します。 VNet から外に出て行くトラフィックが、VPN 接続または ExpressRoute 接続を介してオンプレミスに向かうように VNet のルーティングを変更することを強制トンネリングと言います。  

ASE では、強制トンネリングが問題の原因となる場合があります。 ASE には外部の依存関係が多数あります。これらは、こちらの [ASE のネットワーク アーキテクチャ][network]に関するドキュメントに列挙されています。 既定の ASE では、外に向かうすべての通信が、その ASE と共にプロビジョニングされた VIP を経由する必要があります。

強制トンネリングとは何か、またどのように扱えばよいかを考えるうえで欠かすことのできない視点はルートです。 Azure 仮想ネットワークでは、最長プレフィックス一致 (LPM) に基づいてルーティングが実行されます。  同じ LPM マッチの複数のルートが存在する場合は、そのルートが検出された経緯に応じて次の順序でルートが選択されます。

1. ユーザーの定義ルート
1. BGP のルート (ExpressRoute を使用している場合)
1. システム ルート

VNet におけるルーティングの詳細については、[ユーザー定義ルートと IP 転送][routes]に関するページを参照してください。 

強制トンネリングの対象となる VNet 内で ASE を運用する必要がある場合、2 つの選択肢があります。

1. ASE で直接インターネットにアクセスできるようにする
1. ASE のエグレス エンドポイントを変更する

## <a name="enable-your-ase-to-have-direct-internet-access"></a>ASE で直接インターネットにアクセスできるようにする

ExpressRoute を使って構成されている VNet で ASE が機能するには、次の方法があります。

* 0.0.0.0/0 をアドバタイズするように ExpressRoute を構成します。 既定では、すべての送信トラフィックをオンプレミスに強制的にトンネリングします。
* UDR を作成します。 0.0.0.0/0 のアドレス プレフィックスとインターネットの次ホップの種類を指定して、それを ASE が含まれるサブネットに適用します。

これらの 2 つの変更を行った場合、ASE サブネットから発信されたインターネット宛てのトラフィックは ExpressRoute を強制的に経由せず、ASE は機能します。

> [!IMPORTANT]
> UDR に定義されているルートは、ExpressRoute 構成でアドバタイズされたどのルートよりも優先されるように、詳細にする必要があります。 前の例では、0.0.0.0/0 という広いアドレス範囲を使用しています。 これは、より具体的なアドレス範囲を使用するルート アドバタイズによって誤って上書きされる可能性があります。
>
> ASE は、ルートをパブリックピアリング パスからプライベートピアリング パスにクロスアドバタイズする ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、Microsoft からルート アドバタイズを受信します。 これらのアドバタイズには、Microsoft Azure の一連の広い IP アドレス範囲が含まれています。 これらのアドレス範囲がプライベートピアリング パスでクロスアドバタイズされた場合、ASE のサブネットからの送信ネットワーク パケットはすべて、顧客のオンプレミスのネットワーク インフラストラクチャに強制的にトンネリングされます。 既定では現在、このネットワーク フローは ASE でサポートされていません。 この問題の 1 つの解決策として、パブリックピアリング パスからプライベートピアリング パスへのクロスアドバタイズ ルートの停止があります。  もう 1 つの解決策は、強制トンネリング構成で機能するように ASE を設定することです。

## <a name="change-the-egress-endpoint-for-your-ase"></a>ASE のエグレス エンドポイントを変更する ##

このセクションでは、強制トンネリング構成で ASE が機能するように、ASE で使用されるエグレス エンドポイントを変更する方法について説明します。 ASE からの送信トラフィックがオンプレミス ネットワークへと強制トンネリングされている場合、そのトラフィックが、ASE の VIP アドレスではない IP アドレスから出て行くことを許可する必要があります。

ASE は、外部の依存関係があることに加え、ASE を管理するための受信トラフィックをリッスンする必要があります。 ASE は、そのようなトラフィックに対して応答できることが必要です。また、その応答を別のアドレスから返すことはできません。そのような応答は TCP に違反します。  そのため、ASE のエグレス エンドポイントを変更するために必要なステップが 3 つあります。

1. 受信管理トラフィックが同じ IP アドレスから出て行くことができるようにルート テーブルを設定する
1. エグレス用の IP アドレスを ASE のファイアウォールに追加する
1. ASE から外に出て行くトラフィックのルートをトンネリングする

![強制トンネリングのネットワーク フロー][1]

ASE の稼働後、異なるエグレス アドレスで ASE を構成できるほか、ASE のデプロイ中にエグレス アドレスを設定することもできます。  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>ASE の稼働後にエグレス アドレスを変更する ###
1. ASE のエグレス IP として使用する IP アドレスを取得します。 強制トンネリングを実施している場合、これはご使用の NAT またはゲートウェイ IP になります。  NVA を経由するよう ASE の送信トラフィックをルーティングする場合は、その NVA のパブリック IP がエグレス アドレスになります。
2. そのエグレス アドレスを ASE の構成情報で設定します。 resource.azure.com にアクセスして Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name> に移動すると、ご利用の ASE を表す json を確認できます。  一番上に読み取り/書き込みの表示があることを確認してください。  [編集] をクリックし、一番下までスクロールして、userWhitelistedIpRanges を変更します。  

       "userWhitelistedIpRanges": null 
      
  次のように変更します。 エグレス アドレス範囲として設定するアドレスを使用してください。 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  上部にある [PUT] をクリックします。 これにより、ASE に対するスケーリング操作がトリガーされ、ファイアウォールが調整されます。
   
3. ルート テーブルを作成するか編集し、ASE の場所に対応する管理アドレスへのアクセスとそれらのアドレスからのアクセスを許可するルールを設定します。  管理アドレスは、「[App Service Environment の管理アドレス][management]」に記載されています。 

4. ルート テーブルまたは BGP ルートを使用して、ASE サブネットに適用されているルートを調整します。  

ASE がポータルからの操作に反応しない場合、変更に問題があります。  エグレス アドレスの一覧が不完全であったり、トラフィックが失われていたり、トラフィックがブロックされていたりする可能性があります。  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>異なるエグレス アドレスで新しい ASE を作成する  ###

VNet が既にすべてのトラフィックを強制トンネリングするように構成されている場合、ASE が正常に起動するよう、さらにいくつかのステップで ASE を作成する必要があります。 つまり ASE を作成する過程で、もう 1 つのエグレス エンドポイントの使用を有効にする必要があります。  そのためには、許可するエグレス アドレスが指定されたテンプレートで ASE を作成する必要があります。

1. ASE のエグレス アドレスとして使用する IP アドレスを取得します。
1. ASE で使用するサブネットをあらかじめ作成しておきます。 これはルートを設定するために必要となるほか、テンプレートでも必要となります。  
1. ASE の場所に対応した管理 IP を使用してルート テーブルを作成し、ASE に割り当てます。
1. [テンプレートを使用した ASE の作成][template]に関するページの説明に従い、適切なテンプレートを入手してください。
1. azuredeploy.json ファイルの "resources" セクションを編集します。 次のように、値が含まれた **userWhitelistedIpRanges** 行を追加します。

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

これが適切に構成されると、ASE は問題なく起動します。  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
