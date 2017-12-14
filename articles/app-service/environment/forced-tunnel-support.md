---
title: "Azure App Service Environment の強制トンネリングを構成する"
description: "送信トラフィックが強制トンネリングされている場合に App Service Environment が機能するように設定します"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>強制トンネリングを使用した App Service Environment の構成

App Service Environment は、ユーザーの Azure 仮想ネットワーク内のインスタンスに Azure App Service をデプロイしたものです。 多くのユーザーは、VPN または Azure ExpressRoute 接続を使用して、仮想ネットワークをオンプレミス ネットワークの拡張網として構成しています。 これらのユーザーは、企業のポリシーやその他のセキュリティ上の制約から、すべての送信トラフィックを、インターネットに向かう前にオンプレミスに送るようにルートを構成します。 仮想ネットワークから外に出て行くトラフィックが、VPN 接続または ExpressRoute 接続を介してオンプレミスに向かうように仮想ネットワークのルーティングを変更することを、強制トンネリングと言います。 

App Service Environment では、強制トンネリングが問題の原因となる場合があります。 App Service Environment には外部の依存関係が多数あります。これらは、[App Service Environment のネットワーク アーキテクチャ][network]に関するドキュメントに列挙されています。 既定の App Service Environment では、外に向かうすべての通信が、その App Service Environment と共にプロビジョニングされた VIP を経由する必要があります。

強制トンネリングとは何か、またどのように扱えばよいかを考えるうえで、ルートは重要な要素です。 Azure 仮想ネットワークでは、最長プレフィックス一致 (LPM) に基づいてルーティングが実行されます。 同じ LPM マッチの複数のルートが存在する場合は、そのルートが検出された経緯に応じて次の順序でルートが選択されます。

* ユーザー定義のルート (UDR)
* BGP のルート (ExpressRoute を使用している場合)
* システム ルート

仮想ネットワークにおけるルーティングの詳細については、[ユーザー定義ルートと IP 転送][routes]に関するページをご覧ください。 

強制トンネリングの仮想ネットワークで App Service Environment を機能させるには、次の 2 つの選択肢があります。

* インターネットに直接アクセスするように App Service Environment を設定します。
* App Service Environment のエグレス エンドポイントを変更します。

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>インターネットに直接アクセスするように App Service Environment を設定する

ExpressRoute 接続を使用して仮想ネットワークを構成しつつ、App Service Environment を機能させるには、次の手順を実行します。

* 0.0.0.0/0 をアドバタイズするように ExpressRoute を構成します。 既定では、すべての送信トラフィックをオンプレミスに強制的にトンネリングします。
* UDR を作成します。 アドレス プレフィックスに 0.0.0.0/0 と次ホップの種類にインターネットを指定して、App Service Environment が含まれるサブネットにそれを適用します。

これらの 2 つの変更を行った場合、App Service Environment サブネットから発信されたインターネット宛てのトラフィックは ExpressRoute 接続を強制的に経由せず、App Service Environment は機能します。

> [!IMPORTANT]
> UDR に定義されているルートは、ExpressRoute 構成でアドバタイズされたどのルートよりも優先されるように、詳細にする必要があります。 前の例では、0.0.0.0/0 という広いアドレス範囲を使用しています。 これは、より具体的なアドレス範囲を使用するルート アドバタイズによって誤って上書きされる可能性があります。
>
> App Service Environment は、パブリック ピアリング パスからプライベート ピアリング パスにルートをクロスアドバタイズした ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、Microsoft からルート アドバタイズを受信します。 これらのアドバタイズには、Microsoft Azure の一連の広い IP アドレス範囲が含まれています。 これらのアドレス範囲がプライベートピアリング パスでクロスアドバタイズされた場合、App Service Environment のサブネットからの送信ネットワーク パケットはすべて、顧客のオンプレミスのネットワーク インフラストラクチャに強制的にトンネリングされます。 既定では現在、このネットワーク フローは App Service Environment でサポートされていません。 この問題の 1 つの解決策として、パブリックピアリング パスからプライベートピアリング パスへのクロスアドバタイズ ルートの停止があります。 もう 1 つの解決策は、強制トンネリング構成で機能するように App Service Environment を設定することです。

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>App Service Environment のエグレス エンドポイントを変更する ##

このセクションでは、強制トンネリング構成で App Service Environment が機能するように、App Service Environment で使用されるエグレス エンドポイントを変更する方法について説明します。 App Service Environment からの送信トラフィックがオンプレミス ネットワークへと強制トンネリングされている場合、そのトラフィックが、App Service Environment の VIP アドレスではない IP アドレスから出て行くことを許可する必要があります。

App Service Environment には外部の依存関係があるだけでなく、受信トラフィックをリッスンしてこのようなトラフィックに応答する必要があります。 TCP が中断するため、別のアドレスから返信を送ることはできません。 App Service Environment のエグレス エンドポイントを変更するために必要なステップは次の 3 つです。

1. 受信管理トラフィックが同じ IP アドレスから出て行くことができるようにルート テーブルを設定する。

2. エグレス用の IP アドレスを App Service Environment のファイアウォールに追加する。

3. App Service Environment から外に出て行くトラフィックのルートをトンネリングする。

   ![強制トンネリングのネットワーク フロー][1]

App Service Environment が起動して操作可能の状態になったら、App Service Environment が別のエグレス アドレスを持つように構成できます。または、App Service Environment のデプロイ時に設定できます。

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>App Service Environment が操作可能になった後にエグレス アドレスを変更する ###
1. App Service Environment のエグレス IP として使用する IP アドレスを取得します。 強制トンネリングを実施している場合、これらのアドレスはご使用の NAT またはゲートウェイ IP から取得します。 NVA を経由するよう App Service Environment の送信トラフィックをルーティングする場合は、その NVA のパブリック IP がエグレス アドレスになります。

2. そのエグレス アドレスを App Service Environment の構成情報で設定します。 resource.azure.com にアクセスして Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name> に移動します。 すると、ご利用の App Service Environment を表す JSON を確認できます。 一番上に**読み取り/書き込み**の表示があることを確認してください。 **[編集]** を選択します。 一番下までスクロールし、**userWhitelistedIpRanges** の値を **null** から次のような値に変更します。 エグレス アドレス範囲として設定するアドレスを使用してください。 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   一番上にある **[PUT]** を選択します。 このオプションは、App Service Environment 上のスケール操作をトリガーし、ファイアウォールを調整します。
 
3. ルート テーブルを作成するか編集し、App Service Environment の場所に対応する管理アドレスへのアクセスと、それらのアドレスからのアクセスを許可するルールを設定します。 管理アドレスは、「[App Service Environment の管理アドレス][management]」に記載されています。

4. ルート テーブルまたは BGP ルートを使用して、App Service Environment サブネットに適用されているルートを調整します。 

App Service Environment がポータルからの操作に反応しない場合、実施した変更に問題があります。 エグレス アドレスの一覧が不完全であったり、トラフィックが失われていたり、トラフィックがブロックされていたりする可能性があります。 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>別のエグレス アドレスを持つ新しい App Service Environment を作成する ###

仮想ネットワークが既にすべてのトラフィックを強制トンネリングするように構成されている場合、App Service Environment が正常に起動するよう、さらにいくつかのステップを踏んで App Service Environment を作成する必要があります。 App Service Environment を作成する過程で、もう 1 つのエグレス エンドポイントの使用を有効にする必要があります。 そのためには、許可するエグレス アドレスが指定されたテンプレートで App Service Environment を作成する必要があります。

1. App Service Environment のエグレス アドレスとして使用する IP アドレスを取得します。

2. App Service Environment で使用するサブネットをあらかじめ作成しておきます。 これはルートを設定するために必要となるほか、テンプレートでも必要となります。

3. App Service Environment の場所に対応した管理 IP を使用してルート テーブルを作成します。 これを、App Service Environment に割り当てます。

4. [テンプレートを使用した App Service Environment の作成][template]に関する指示に従います。 適切なテンプレートを入手します。

5. azuredeploy.json ファイルの "resources" セクションを編集します。 次のように、値が含まれた **userWhitelistedIpRanges** 行を追加します。

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

このセクションが正しく構成されると、App Service Environment が正常に起動します。 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
