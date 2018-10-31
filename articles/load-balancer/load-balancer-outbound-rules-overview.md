---
title: Azure Load Balancer のアウトバウンド規則 | Microsoft Docs
description: アウトバウンド規則を使用して、送信ネットワーク アドレス変換を定義します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jpconnock
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: kumud
ms.openlocfilehash: 0ba7ed902c6ecb7a328aa6db3d3855b88bed2813
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637564"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer のアウトバウンド規則

Azure Load Balancer は、受信だけでなく、仮想ネットワークからの送信接続も提供します。  アウトバウンド規則を使用すると、パブリック [Standard Load Balancer](load-balancer-standard-overview.md) の送信ネットワーク アドレス変換の構成を簡素化できます。  送信接続を宣言的に完全に制御することで、特定のニーズに合わせてこの機能を拡張および調整できます。

![Load Balancer のアウトバウンド規則](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

アウトバウンド規則を使用すると、Load Balancer を使って以下を行うことができます。 
- 送信 NAT をゼロから定義する。
- 既存の送信 NAT の動作を拡張および調整する。 

アウトバウンド規則によって以下を制御できます。
- どの仮想マシンをどのパブリック IP アドレスに変換するか。 
- [送信 SNAT ポート](load-balancer-outbound-connections.md#snat)を割り当てる方法。
- 送信変換の提供対象となるプロトコル。
- 送信接続のアイドル タイムアウトの時間。
- アイドル タイムアウト時に TCP リセットを送信するかどうか (パブリック プレビュー)。 

アウトバウンド規則により、[送信接続](load-balancer-outbound-connections.md)に関する記事に記載されている[シナリオ 2](load-balancer-outbound-connections.md#lb) が拡張されます。シナリオの優先順位はそのままです。

## <a name="outbound-rule"></a>送信規則

すべてのロード バランサー規則と同様に、アウトバウンド規則は、負荷分散規則および受信 NAT 規則と同じ構文に従います。

**フロントエンド** + **パラメーター** + **バックエンド プール**

アウトバウンド規則では、"_フロントエンド_" に変換される、"_バックエンド プールによって識別されるすべての仮想マシン_" の送信 NAT を構成します。  また、"_パラメーター_" により、送信 NAT アルゴリズムをさらに細かく制御できます。

API バージョン "2018-07-01" では、次のような構造のアウトバウンド規則定義が許可されています。

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>有効な送信 NAT 構成は、すべてのアウトバウンド規則と負荷分散規則を合成したものです。 アウトバウンド規則は負荷分散規則に付加されます。 VM に複数の規則を適用するときに、効果的な送信 NAT 変換を管理するために、[負荷分散規則の送信 NAT を無効にする方法](#disablesnat)を確認してください。 負荷分散規則と同じパブリック IP アドレスを使用するアウトバウンド規則を定義するときは、[送信 SNAT を無効にする](#disablesnat)必要があります。

### <a name="scale"></a>複数の IP アドレスで送信 NAT を拡張する

アウトバウンド規則は 1 つのパブリック IP アドレスでのみ使用できますが、アウトバウンド規則によって、送信 NAT を拡張するための構成の負担が軽減されます。 複数の IP アドレスを使用することで、大規模なシナリオを計画できます。また、アウトバウンド規則を使用して、[SNAT が枯渇](load-balancer-outbound-connections.md#snatexhaust)しがちなパターンを緩和することもできます。  

フロントエンドによって提供される追加の IP アドレスごとに、Load Balancer が SNAT ポートとして使用する 64,000 個のエフェメラル ポートが提供されます。 負荷分散規則または受信 NAT 規則では単一のフロントエンドを使用しますが、アウトバウンド規則ではフロントエンドの概念を拡張し、規則ごとに複数のフロントエンドを使用できます。  規則ごとに複数のフロントエンドを使用すると、使用可能な SNAT ポートの数にパブリック IP アドレスの数が乗算されるので、非常に大規模なシナリオをサポートできます。

さらに、[パブリック IP プレフィックス](https://aka.ms/lbpublicipprefix)をアウトバウンド規則で直接使用することもできます。  これにより、Azure デプロイからのフローのスケーリングが容易になり、ホワイトリスト登録が簡素化されます。 パブリック IP アドレス プレフィックスを直接参照するように、Load Balancer リソース内のフロントエンド IP 構成を設定できます。  これにより、Load Balancer はそのパブリック IP プレフィックスを排他的に制御することが可能になります。また、アウトバウンド規則で、パブリック IP プレフィックスに含まれるすべてのパブリック IP アドレスが自動的に送信接続に使用されるようになります。  パブリック IP プレフィックスの範囲内の IP アドレスごとに、Load Balancer が SNAT ポートとして使用する 64,000 個のエフェメラル ポートが提供されます。   

アウトバウンド規則によって、パブリック IP プレフィックスを完全に制御する必要があるため、このオプションを使用するときは、パブリック IP プレフィックスから作成される個々のパブリック IP アドレス リソースを使用することはできません。  より細かい制御が必要な場合は、パブリック IP プレフィックスから個々のパブリック IP アドレス リソースを作成し、複数のパブリック IP アドレスをアウトバウンド規則のフロントエンドに個別に割り当てます。

### <a name="snatports"></a>SNAT ポートの割り当てを調整する

アウトバウンド規則を使用して、[バックエンド プール サイズに基づく SNAT ポートの自動割り当て](load-balancer-outbound-connections.md#preallocatedports)を調整し、SNAT ポートの自動割り当てによって提供されるポート数よりも多くのポートを割り当てたり、割り当て数を減らしたりできます。

VM (NIC IP 構成) ごとに 10,000 個の SNAT ポートを割り当てるには、次のパラメーターを使用します。
 

          "allocatedOutboundPorts": 10000

アウトバウンド規則のすべてのフロントエンドのパブリック IP アドレスごとに、SNAT ポートとして使用する最大 64,000 個のエフェメラル ポートが提供されます。  Load Balancer は、SNAT ポートを 8 の倍数で割り当てます。 8 で割り切れない値を指定すると、その構成操作は拒否されます。  パブリック IP アドレスの数に基づく使用可能な SNAT ポートの数よりも多くのポートを割り当てようとすると、その構成操作は拒否されます。  たとえば、VM ごとに 10,000 個のポートを割り当て、バックエンド プール内の 7 つの VM が 1 つのパブリック IP アドレスを共有している場合、この構成は拒否されます (7 x 10,000 SNAT ポート > 64,000 SNAT ポート)。  このシナリオに対応するには、アウトバウンド規則のフロントエンドにパブリック IP アドレスを追加します。

ポート数に 0 を指定することで、[バックエンド プール サイズに基づく SNAT ポートの自動割り当て](load-balancer-outbound-connections.md#preallocatedports)に戻すことができます。

### <a name="idletimeout"></a>送信フローのアイドル タイムアウトを制御する

アウトバウンド規則には、送信フローのアイドル タイムアウトを制御し、アプリケーションのニーズに合わせるための構成パラメーターがあります。  送信アイドル タイムアウトの既定値は 4 分です。  このパラメーターには、特定の規則に一致するフローのアイドル タイムアウトの分数として、4 から 66 の値を指定できます。

送信アイドル タイムアウトを 1 時間に設定するには、次のパラメーターを使用します。

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a>アイドル タイムアウト時の TCP リセットを有効にする (プレビュー)

Load Balancer の既定の動作では、送信アイドル タイムアウトに達すると、警告なしにフローが破棄されます。  enableTCPReset パラメーターを使用すると、アプリケーションのより予測可能な動作を有効にし、送信アイドル タイムアウトに達したときに、双方向 TCP リセット (TCP RST) を送信するかどうかを制御できます。 

アウトバウンド規則で TCP リセットを有効にするには、次のパラメーターを使用します。

           "enableTcpReset": true

利用可能なリージョンなど、詳細については、[アイドル タイムアウト時の TCP リセット (プレビュー)](https://aka.ms/lbtcpreset) に関する記事をご覧ください。

### <a name="proto"></a>1 つの規則で TCP と UDP の両方のトランスポート プロトコルをサポートする

ほとんどの場合、アウトバウンド規則のトランスポート プロトコルには "All" を使用しますが、必要に応じて、アウトバウンド規則を特定のトランスポート プロトコルに適用することもできます。

プロトコルを TCP と UDP に設定するには、次のパラメーターを使用します。

          "protocol": "All"

### <a name="disablesnat"></a>負荷分散規則の送信 NAT を無効にする

以前に説明したように、負荷分散規則では送信 NAT の自動プログラミングを提供します。 ただし、負荷分散規則による送信 NAT の自動プログラミングを無効にして、送信 NAT の動作を制御または調整できるようにすることでメリットが得られるシナリオやこれが必要なシナリオもあります。  アウトバウンド規則には、送信 NAT の自動プログラミングを停止することが重要なシナリオがあります。

このパラメーターには、次の 2 通りの使い方があります。
- 送信 NAT での受信 IP アドレスの使用を必要に応じて抑制します。  アウトバウンド規則は負荷分散規則に付加されますが、このパラメーターを設定することで、アウトバウンド規則による制御が可能になります。
  
- 受信と送信に同時に使用される IP アドレスの送信 NAT パラメーターを調整します。  アウトバウンド規則による制御を可能にするには、送信 NAT の自動プログラミングを無効にする必要があります。  たとえば、受信にも使用されるアドレスの SNAT ポートの割り当てを変更するには、このパラメーターを true に設定する必要があります。  アウトバウンド規則を使用して、受信にも使用される IP アドレスのパラメーターを再定義しようとしたときに、負荷分散規則の送信 NAT プログラミングを解除していない場合、アウトバウンド規則の構成操作は失敗します。

>[!IMPORTANT]
> このパラメーターを true に設定し、送信接続を定義するアウトバウンド規則 (または[インスタンス レベルのパブリック IP のシナリオ](load-balancer-outbound-connections.md#ilpip)) がない場合、仮想マシンは送信接続できなくなります。  VM またはアプリケーションの一部の操作は、送信接続が使用可能であることに依存している場合があります。 シナリオの依存関係を理解し、この変更の影響を考慮してください。

負荷分散規則で送信 SNAT を無効にするには、次の構成パラメーターを使用します。

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

disableOutboundSnat パラメーターの既定値は false です。この場合、負荷分散規則は、負荷分散規則構成のミラー イメージとして自動送信 NAT を提供**します**。  

負荷分散規則で disableOutboundSnat を true に設定すると、負荷分散規則は送信 NAT の自動プログラミングの制御を解放します。  負荷分散規則の結果としての送信 SNAT は無効になります。

### <a name="reuse-existing-or-define-new-backend-pools"></a>既存のバックエンド プールを再利用するか、新しいバックエンド プールを定義する

アウトバウンド規則では、規則を適用する VM のグループを定義するための新しい概念は導入されていません。  代わりに、負荷分散規則にも使用されるバックエンド プールの概念を再利用します。 これを使用して、既存のバックエンド プール定義を再利用するか、アウトバウンド規則専用のものを作成することで、構成を簡素化できます。

## <a name="scenarios"></a>シナリオ

### <a name="groom"></a>送信接続をパブリック IP アドレスの特定のセットに調整する

アウトバウンド規則を使用して、パブリック IP アドレスの特定のセットが発信元になるように送信接続を調整し、ホワイトリスト登録のシナリオを容易にすることができます。  この発信元パブリック IP アドレスは、負荷分散規則で使用されているものと同じであっても、負荷分散規則で使用されているものとは異なるパブリック IP アドレスのセットであっても構いません。  

1. [パブリック IP プレフィックス](https://aka.ms/lbpublicipprefix) (またはパブリック IP プレフィックスのパブリック IP アドレス) を作成します。
2. パブリック Standard Load Balancer を作成する
3. 使用するパブリック IP プレフィックス (またはパブリック IP アドレス) を参照するフロントエンドを作成します。
4. バックエンド プールを再利用するか、バックエンド プールを作成し、VM をパブリック Load Balancer のバックエンド プールに配置します。
5. フロントエンドを使用してこれらの VM の送信 NAT をプログラムするように、パブリック Load Balancer のアウトバウンド規則を構成します。
   
送信に負荷分散規則を使用しない場合は、負荷分散規則で[送信 SNAT を無効にする](#disablesnat)必要があります。

### <a name="modifysnat"></a>SNAT ポートの割り当てを変更する

アウトバウンド規則を使用して、[バックエンド プール サイズに基づく SNAT ポートの自動割り当て](load-balancer-outbound-connections.md#preallocatedports)を調整できます。

たとえば、送信 NAT の 1 つのパブリック IP アドレスを共有する 2 つの仮想マシンがあり、SNAT ポートが枯渇している場合は、割り当てられた SNAT ポートの数を既定の 1024 個から増やすことができます。 パブリック IP アドレスごとに、最大 64,000 個のエフェメラル ポートを提供できます。  単一のパブリック IP アドレス フロントエンドでアウトバウンド規則を構成した場合、合計 64,000 個の SNAT ポートをバックエンド プール内の VM に配布できます。  2 つの VM の場合、アウトバウンド規則を使用して最大 32,000 個 (2 x 32,000 = 64,000) の SNAT ポートを割り当てることができます。

[送信接続](load-balancer-outbound-connections.md)の概要と、[SNAT](load-balancer-outbound-connections.md#snat) ポートを割り当てて使用する方法の詳細をご覧ください。

### <a name="outboundonly"></a>送信のみを有効にする

パブリック Standard Load Balancer を使用して、VM のグループに送信 NAT を提供できます。 このシナリオでは、追加の規則を必要とせずに、アウトバウンド規則を単独で使用できます。

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>VM の送信 NAT のみ (受信なし)

パブリック Standard Load Balancer を定義し、VM をバックエンド プールに配置します。送信 NAT をプログラムするようにし、特定のパブリック IP アドレスが発信元になるように送信接続を調整するアウトバウンド規則を構成します。 パブリック IP プレフィックスを使用して、送信接続の発信元のホワイトリスト登録を簡素化することもできます。

1. パブリック Standard Load Balancer を作成します。
2. バックエンド プールを作成し、VM をパブリック Load Balancer のバックエンド プールに配置します。
3. これらの VM の送信 NAT をプログラムするように、パブリック Load Balancer のアウトバウンド規則を構成します。

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>内部 Standard Load Balancer の送信 NAT のシナリオ

内部 Standard Load Balancer を使用する場合、送信接続が明示的に宣言されるまで送信 NAT は使用できません。 以下の手順で、アウトバウンド規則を使用して内部 Standard Load Balancer の背後にある VM の送信接続を作成し、送信接続を定義します。

1. パブリック Standard Load Balancer を作成します。
2. 内部 Load Balancer に加えて、バックエンド プールを作成し、VM をパブリック Load Balancer のバックエンド プールに配置します。
3. これらの VM の送信 NAT をプログラムするように、パブリック Load Balancer のアウトバウンド規則を構成します。

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>パブリック Standard Load Balancer で送信 NAT の TCP プロトコルと UDP プロトコルの両方を有効にする

- パブリック Standard Load Balancer を使用する場合、提供される送信 NAT の自動プログラミングは負荷分散規則のトランスポート プロトコルに対応します。  

   1. 負荷分散規則で送信 SNAT を無効にします。
   2. 同じ Load Balancer のアウトバウンド規則を構成します。
   3. VM によって既に使用されているバックエンド プールを再利用します。
   4. アウトバウンド規則の一部として "protocol": "All" を指定します。

- 受信 NAT 規則のみを使用している場合、送信 NAT は提供されません。

   1. VM をバックエンド プールに配置します。
   2. パブリック IP アドレスまたはパブリック IP プレフィックスを使用して、1 つまたは複数のフロントエンド IP 構成を定義します。
   3. 同じ Load Balancer のアウトバウンド規則を構成します。
   4. アウトバウンド規則の一部として "protocol": "All" を指定します。

## <a name="limitations"></a>制限事項

- フロントエンド IP アドレスごとに使用可能なエフェメラル ポートの最大数は 64,000 個です。
- 構成可能な送信アイドル タイムアウトの範囲は、4 から 66 分 (240 から 4000 秒) です。
- Load Balancer では、送信 NAT の ICMP はサポートされていません。
- ポータルを使用して、アウトバウンド規則を構成または表示することはできません。  代わりに、テンプレート、REST API、Az CLI 2.0、または PowerShell を使用します。

## <a name="next-steps"></a>次の手順

- [送信接続に対する Load Balancer](load-balancer-outbound-connections.md) の使用について学習する。
- [Standard Load Balancer](load-balancer-standard-overview.md) を参照します。
- [アイドル タイムアウト時の双方向 TCP リセット](load-balancer-tcp-reset.md)の詳細を確認する。
- [Azure CLI 2.0 を使用してアウトバウンド規則を構成する](configure-load-balancer-outbound-cli.md)。
