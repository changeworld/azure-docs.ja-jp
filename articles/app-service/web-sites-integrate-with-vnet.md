---
title: Azure Virtual Network とのアプリの統合
description: Azure App Service のアプリを Azure Virtual Network と統合します。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649032"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>アプリを Azure 仮想ネットワークに統合する
このドキュメントでは、Azure App Service の仮想ネットワーク統合機能と、それを [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) のアプリで設定する方法について説明します。 [Azure Virtual Network][VNETOverview] (VNet) を使用すると、多くの Azure リソースをインターネットでルーティングできないネットワークに配置できます。  

Azure App Service には、次の 2 つのバリエーションがあります。 

1. Isolated を除くすべての価格プランをサポートするマルチテナント システム
2. VNet にデプロイされ、Isolated 価格プランのアプリをサポートする App Service Environment (ASE)

このドキュメントでは、マルチ テナント App Service での使用に向けた VNet 統合機能について説明します。 アプリが [App Service Environment][ASEintro] 内にある場合、そのアプリは既に VNet 内に存在するため、同じ VNet 内のリソースに到達するために VNet 統合機能を使用する必要はありません。 App Service のすべてのネットワーク機能の詳細については、[App Service のネットワーク機能](networking-features.md)に関するページを参照してください。

VNet 統合により、Web アプリから仮想ネットワーク内のリソースにアクセスできるようになりますが、VNet からその Web アプリへのインバウンド プライベート アクセスは付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 Vnet 統合は、アプリから VNet への送信呼び出しを行うためにのみ存在します。 VNet 統合機能は、同じリージョンの VNet と使用する場合と、他のリージョンの VNet で使用する場合で動作が異なります。 VNet 統合機能には 2 つのバリエーションがあります。

1. リージョン VNet 統合 - 同じリージョンで Resource Manager VNet に接続する場合は、統合先の VNet に専用のサブネットが必要です。 
2. ゲートウェイが必要な VNet 統合 - 他のリージョンの VNet または同じリージョンのクラシック VNet に接続する場合、ターゲット VNet にプロビジョニングされた Virtual Network ゲートウェイが必要です。

以下は、VNet 統合機能の特徴です。

* Standard、Premium、PremiumV2、または Elastic Premium の価格プランが必要 
* TCP と UDP をサポート
* App Service アプリや関数アプリで動作可能

以下に、VNet 統合でサポートされていないことの例を示します。

* ドライブのマウント
* AD 統合 
* NetBIOS

ゲートウェイが必要な VNet 統合では、ターゲット VNet 内、またはピアリングまたは VPN を使用してターゲット VNet に接続されているネットワーク内のリソースのみにアクセスできます。 ゲートウェイが必要な VNet 統合では、ExpressRoute 接続全体で利用可能なリソースへのアクセスを有効にしたり、サービス エンドポイントと連携したりできません。 

使用されているバージョンには関係なく、Vnet 統合は Web アプリに仮想ネットワーク内のリソースへのアクセス権を付与しますが、仮想ネットワークから Web アプリへの受信プライベート アクセス権は付与しません。 プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 Vnet 統合は、アプリから VNet への送信呼び出しを行うためにのみ存在します。 

## <a name="enable-vnet-integration"></a>VNET 統合を有効にする 

1. App Service ポータルで [ネットワーク] の UI に移動します。 [VNet 統合] の下の [*ここをクリックして構成*] を選択します。 

1. **[Add VNet]\(VNet の追加)** を選択します。  

   ![Vnet 統合を選択する][1]

1. ドロップダウン リストには、同じリージョンのサブスクリプション内のすべての Resource Manager VNet が含まれており、その下は、他のすべてのリージョンのすべての Resource Manager VNet の一覧です。 統合する VNet を選択します。

   ![VNet の選択][2]

   * VNet が同じリージョンにある場合は、新しいサブネットを作成するか、空の既存のサブネットを選択します。 

   * 別のリージョンの VNet を選択するには、ポイント対サイトが有効になっている Virtual Network ゲートウェイがプロビジョニングされている必要があります。

   * クラシック VNet と統合するには、VNet ドロップダウンをクリックするのではなく、 **[クラシック VNet に接続するには、ここをクリックします]** を選択します。 目的のクラシック VNet を選択します。 ターゲットの VNet には、ポイント対サイトが有効になっている Virtual Network ゲートウェイがプロビジョニングされている必要があります。

    ![クラシック VNet の選択][3]
    
統合中にアプリは再起動されます。  統合が完了すると、統合されている VNet の詳細が表示されます。 

VNet に統合されたアプリは、VNet が構成されているのと同じ DNS サーバーを使用します (Azure DNS Private Zones の場合を除く)。 現時点では、Azure DNS Private Zones で VNet 統合を使用することはできません。

## <a name="regional-vnet-integration"></a>リージョン VNet 統合

リージョン VNet 統合を使用すると、アプリは次のものにアクセスできるようになります。

* 統合するのと同じリージョン内の VNet 内のリソース 
* 同じリージョンにある VNet とピアリングされた VNet 内のリソース
* サービス エンドポイントでセキュリティ保護されたサービス
* ExpressRoute 接続にまたがるリソース
* 接続されている VNet 内のリソース
* ExpressRoute 接続を含むピアリングされた接続にまたがるリソース
* プライベート エンドポイント 

同じリージョンの VNet との VNet 統合を使用する場合は、次の Azure ネットワーク機能を使用できます。

* ネットワーク セキュリティ グループ (NSG) - 統合サブネットに配置されているネットワーク セキュリティ グループを使用して送信トラフィックをブロックできます。 VNet 統合を使用して Web アプリへの受信アクセスを提供できないため、受信規則は適用されません。
* ルート テーブル (UDR) - 統合サブネットにルート テーブルを配置し、必要な場所に送信トラフィックを送信できます。 

既定では、アプリは RFC1918 トラフィックのみを VNet にルーティングします。 すべての送信トラフィックを VNet にルーティングする場合は、アプリの設定 WEBSITE_VNET_ROUTE_ALL をアプリに適用します。 アプリ設定を構成するには、次のようにします。

1. アプリ ポータルの [構成] UI にアクセスします。 **[新しいアプリケーション設定]** を選択します
1. [名前] フィールドに「**WEBSITE_VNET_ROUTE_ALL**」、[値] フィールドに「**1**」と入力します。

   ![アプリケーション設定を指定する][4]

1. **[OK]** を選択します。
1. **[保存]** を選びます。

すべての送信トラフィックを VNet にルーティングする場合は、統合サブネットに適用される NSG と UDR の対象となります。 すべての送信トラフィックを VNet にルーティングすると、トラフィックを他の場所に送信するためのルートを指定しない限り、送信アドレスはアプリのプロパティに一覧表示される送信アドレスになります。 

同じリージョンの VNet との VNet 統合を使用する場合、いくつかの制限があります。

* グローバル ピアリング接続にまたがるリソースには到達できません。
* この機能は、PremiumV2 の App Service プランをサポートする新しい App Service スケール ユニットからのみ使用できます。
* 統合サブネットは、1 つの App Service プランでしか使用できません。
* この機能は、App Service Environment にある Isolated プランのアプリでは使用できません。
* この機能では、Resource Manager VNet 内に 32 個以上のアドレスを含む /27 である未使用のサブネットが必要です。
* アプリと VNet は同じリージョンに存在する必要があります。
* 統合アプリで VNet を削除することはできません。 VNet を削除する前に統合を削除してください。 
* Web アプリと同じサブスクリプション内 VNet とのみ統合できます
* App Service プランごとに 1 リージョンの VNet 統合のみを持つことができます。 同じ App Service プラン内の複数のアプリが同じ VNet を使用できます。 
* リージョン VNet 統合を使用しているアプリがあるときに、アプリまたは App Service プランのサブスクリプションを変更することはできません

App Service プランのインスタンスごとに 1 つのアドレスが使用されます。 アプリを 5 つのインスタンスにスケールする場合は、5 つのアドレスが使用されます。 割り当てた後はサブネット サイズを変更できないため、アプリが到達する可能性のあるスケールに対応できるだけの十分な大きさを持つサブネットを使用する必要があります。 推奨されるサイズは、64 のアドレスを持つ /26 です。 64 のアドレスを持つ /26 は、Premium App Service プランの 30 インスタンスに対応します。 App Service プランをスケールアップまたはスケールダウンする場合は、短時間に 2 倍の数のアドレスが必要になります。 

別の App Service プランのアプリで、別の App Service プランのアプリから既に接続されている VNet に到達するようにしたい場合は、既存の VNet 統合によって使用されているものとは異なるサブネットを選択する必要があります。  

この機能は、Linux ではプレビュー段階にあります。 Linux 形式の機能では、RFC 1918 アドレス (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) への呼び出しのみがサポートされます。

### <a name="web-app-for-containers"></a>Web App for Containers

Linux 上の App Service を組み込みイメージで使用する場合、リージョン VNet 統合は追加の変更なしで機能します。 Web App for Containers を使用している場合は、VNet 統合を使用するために docker イメージを変更する必要があります。 docker イメージで、ハードコーディングされたポート番号を使用するのではなく、メイン Web サーバーのリスニング ポートとして PORT 環境変数を使用します。 PORT 環境変数は、コンテナーの起動時に App Service プラットフォームによって自動的に設定されます。 SSH を使用している場合は、リージョン VNet 統合を使用するときに SSH_PORT 環境変数で指定されたポート番号でリッスンするように SSH デーモンを構成する必要があります。  Linux では、ゲートウェイが必要な VNet 統合はサポートされていません。 

### <a name="service-endpoints"></a>サービス エンドポイント

リージョン VNet 統合では、サービス エンドポイントを使用できます。  アプリでサービス エンドポイントを使用するには、リージョン VNet 統合を使用し、選択した VNet に接続してから、統合に使用したサブネット上のサービス エンドポイントを構成します。 

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループを使用すると、VNet 内のリソースへの受信および送信トラフィックをブロックできます。 リージョン VNet 統合を使用する Web アプリでは、[ネットワーク セキュリティ グループ][VNETnsg]を使用して、VNet またはインターネット内のリソースへの送信トラフィックをブロックできます。 パブリック アドレスへのトラフィックをブロックするには、WEBSITE_VNET_ROUTE_ALL アプリケーション設定を 1 に設定する必要があります。 NSG の受信規則はアプリに適用されません。これは、VNet 統合がアプリからの送信トラフィックにのみ影響するためです。 Web アプリへの受信トラフィックを制御するには、アクセス制限機能を使用します。 統合サブネットに適用される NSG は、統合サブネットに適用されているルートに関係なく有効になります。 WEBSITE_VNET_ROUTE_ALL が 1 に設定されていて、統合サブネットでパブリック アドレス トラフィックに影響を与えるルートがない場合、すべての送信トラフィックは、統合サブネットに割り当てられた NSG の対象となります。 WEBSITE_VNET_ROUTE_ALL が設定されていない場合、NSG は RFC1918 トラフィックにのみ適用されます。

### <a name="routes"></a>ルート

ルート テーブルを使用すると、アプリからの送信トラフィックを任意の場所にルーティングすることができます。 既定では、ルート テーブルは、RFC1918 の送信先トラフィックにのみ影響します。  WEBSITE_VNET_ROUTE_ALL を 1 に設定すると、すべての送信呼び出しが影響を受けます。 統合サブネットで設定されているルートは、受信アプリ要求への応答には影響しません。 一般的な宛先には、ファイアウォール デバイスまたはゲートウェイを含めることができます。 オンプレミスのすべての送信トラフィックをルーティングする場合は、ルート テーブルを使用して、すべての送信トラフィックを ExpressRoute ゲートウェイに送信できます。 ゲートウェイにトラフィックをルーティングする場合は、外部ネットワークのルートを設定して、応答を返信するようにしてください。

Border Gateway Protocol (BGP) ルートもアプリのトラフィックに影響を与えます。 ExpressRoute ゲートウェイのようなものから BGP ルートを使用している場合は、アプリの送信トラフィックが影響を受けます。 既定では、BGP ルートは、RFC1918 の送信先トラフィックにのみ影響します。 WEBSITE_VNET_ROUTE_ALL が 1 に設定されている場合、すべての送信トラフィックは、BGP ルートの影響を受ける可能性があります。 

### <a name="how-regional-vnet-integration-works"></a>リージョン VNET 統合のしくみ

App Service 内のアプリは、worker ロールでホストされます。 Basic 以上の価格プランは、同じワーカー上で他の顧客のワークロードが実行されていない専用のホスティング プランです。 リージョン VNet 統合は、委任されたサブネット内のアドレスで仮想インターフェイスをマウントすることによって機能します。 送信元アドレスは VNet 内に存在するため、VNet 内の VM と同様に、VNet 内または VNet 経由でほとんどの場所にアクセスできます。 ネットワークの実装は VNet 内の VM の実行とは異なるため、この機能の使用中に、一部のネットワーク機能はまだ使用できません。

![リージョン VNET 統合のしくみ][5]

リージョン VNet 統合が有効になっても、アプリは引き続き、通常と同じチャネル経由でインターネットへの送信呼び出しを行います。 アプリのプロパティ ポータルに一覧表示される送信アドレスは引き続き、そのアプリによって使用されるアドレスです。 アプリに関する変更は、サービス エンドポイントでセキュリティ保護されたサービスまたは RFC 1918 アドレスへの呼び出しが VNet に転送されることです。 WEBSITE_VNET_ROUTE_ALL が 1 に設定されている場合、すべての送信トラフィックを VNet 内に送信できます。 

この機能は、ワーカーあたり 1 つの仮想インターフェイスのみをサポートします。  worker あたり 1 つの仮想インターフェイスは、App Service プランごとに 1 リージョンの VNet 統合があることを意味します。 同じ App Service プラン内のすべてのアプリが同じ Vnet 統合を使用できますが、アプリで追加の VNet に接続する必要がある場合は、別の App Service プランを作成する必要があります。 使用される仮想インターフェイスは、顧客が直接アクセスできるリソースではありません。

このテクノロジが動作する方法の性質のために、Vnet 統合で使用されるトラフィックは Network Watcher や NSG フロー ログには表示されません。  

## <a name="gateway-required-vnet-integration"></a>ゲートウェイが必要な VNet 統合

ゲートウェイが必要な VNet 統合では、別のリージョンの VNet またはクラシック VNet への接続をサポートしています。 ゲートウェイが必要な VNet 統合: 

* アプリは、一度に 1 つの VNet にのみ接続できます
* App Service プランで、最大 5 つの VNet と統合できます 
* App Service プランで使用できる総数に影響を与えることなく、App Service プラン内の複数のアプリで同じ VNet を使用できます。  同じ App Service プラン内の同じ VNet を使用しているアプリが 6 つある場合、使用されている VNet は 1 つとしてカウントされます。 
* ゲートウェイに対する SLA により、99.9% の SLA をサポートします
* VNet が構成されている DNS をアプリで使用できるようにします
* アプリに接続するために、Virtual Network のルート ベースのゲートウェイが SSTP ポイント対サイト VPN を使用して構成されている必要があります。 

ゲートウェイが必要な VNet 統合は次の場合は使用できません。

* Linux アプリに対して
* ExpressRoute に接続されている VNet に対して 
* サービス エンドポイントによって保護されているリソースにアクセスする場合
* ExpressRoute とポイント対サイト/サイト間 VPN の両方をサポートする共存ゲートウェイに対して

### <a name="set-up-a-gateway-in-your-vnet"></a>VNet 内にゲートウェイを設定する ###

ゲートウェイを作成するには:

1. VNet 内に[ゲートウェイ サブネットを作成][creategatewaysubnet]します。  

1. [VPN ゲートウェイを作成][creategateway]します。 VPN の種類はルート ベースを選択します。

1. [ポイント対サイト アドレスを設定][setp2saddresses]します。 ゲートウェイが Basic SKU 内にない場合は、ポイント対サイトの構成で IKEV2 を無効にする必要があり、SSTP を選択する必要があります。 ポイント対サイトのアドレス空間は、RFC 1918 アドレス ブロック 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16 内に存在する必要があります。

App Service の VNet 統合で使用するゲートウェイを作成しているだけの場合は、証明書をアップロードする必要はありません。 ゲートウェイの作成には 30 分かかることがあります。 ゲートウェイがプロビジョニングされるまで、アプリを VNet に統合することはできません。 

### <a name="how-gateway-required-vnet-integration-works"></a>ゲートウェイが必要な VNet 統合のしくみ

ゲートウェイが必要な Vnet 統合は、ポイント対サイト VPN テクノロジに基づいて構築されています。 ポイント対サイト VPN によって、ネットワーク アクセスを、アプリをホストしている仮想マシンのみに制限しています。 アプリは、ハイブリッド接続または VNet 統合を通してのみ、インターネットにトラフィックを送信するように制限されています。 ゲートウェイが必要な VNet 統合を使用するようにアプリをポータルで構成すると、ゲートウェイとアプリケーション側で証明書を作成して割り当てるための複雑なネゴシエーションがユーザーの代わりに管理されます。 最終的に、アプリをホストするために使用される worker は、選択した VNet 内の仮想ネットワーク ゲートウェイに直接接続できるようになります。 

![ゲートウェイが必要な VNet 統合のしくみ][6]

### <a name="accessing-on-premises-resources"></a>オンプレミスのリソースへのアクセス

アプリは、サイト間接続がある Vnet と統合することで、オンプレミスのリソースにアクセスできます。 ゲートウェイが必要な Vnet 統合を使用している場合は、ポイント対サイト アドレス ブロックでオンプレミスの VPN ゲートウェイのルートを更新する必要があります。 サイト間 VPN が初めてセットアップされるときには、その構成に使用するスクリプトによって、ルートが正しくセットアップされるはずです。 サイト間 VPN を作成した後にポイント対サイト アドレスを追加する場合は、ルートを手動で更新する必要があります。 その詳しい手順はゲートウェイごとに異なるため、ここでは取り上げません。 BGP をサイト間 VPN 接続で構成することはできません。

VNet 経由でオンプレミスに到達するために、リージョン Vnet 統合機能に追加の構成は必要ありません。 必要なのは、単に ExpressRoute またはサイト間 VPN を使用して VNet をオンプレミスに接続することだけです。 

> [!NOTE]
> ゲートウェイが必要な Vnet 統合機能では、アプリは ExpressRoute ゲートウェイを含む VNet とは統合されません。 ExpressRoute ゲートウェイが[共存モード][VPNERCoex]で構成されている場合であっても、VNet 統合は機能しません。 ExpressRoute 接続経由でリソースにアクセスする必要がある場合は、VNet で実行されるリージョン VNet 統合機能または [App Service Environment][ASE] を使用できます。 
> 
> 

### <a name="peering"></a>ピアリング

リージョン Vnet 統合でピアリングを使用している場合、追加の構成を行う必要はありません。 

ゲートウェイが必要な Vnet 統合でピアリングを使用している場合は、いくつかの追加の項目を構成する必要があります。 アプリで動作するようにピアリングを構成するには:

1. アプリの接続先の VNet でピアリング接続を追加します。 ピアリング接続の追加時には、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** チェック ボックスと **[ゲートウェイ転送を許可する]** チェック ボックスをオンにします。
1. 接続先の Vnet にピアリングされている VNet でピアリング接続を追加します。 対象の VNet でピアリング接続を追加するときには、 **[仮想ネットワークアクセスを許可する]** を有効にして、 **[転送されたトラフィックを許可する]** チェック ボックスと **[Allow remote gateways]\(リモート ゲートウェイを許可する)** チェック ボックスをオンにします。
1. ポータルで [App Service プラン] > [ネットワーク] > VNet 統合 UI と移動します。  アプリの接続先 VNet を選択します。 ルーティングのセクションで、アプリの接続先 VNet とピアリングされている VNet のアドレス範囲を追加します。  

## <a name="managing-vnet-integration"></a>Vnet 統合の管理 

VNet との接続および切断は、アプリ レベルで行われます。 複数のアプリをまたいで VNet 統合に影響する可能性がある操作は、App Service プラン レベルで行われます。 アプリ > [ネットワーク] > [Vnet 統合] ポータルから、VNet に関する詳細を取得できます。 [ASP] > [ネットワーク] > [VNet 統合] ポータルの ASP レベルでも同様の情報が表示されます。

VNet 統合のアプリ ビューで実行できる操作は、現在接続されている VNet からアプリを切断する操作のみです。 VNet からアプリを切断するには、 **[切断]** を選択します。 VNet から切断すると、アプリは再起動されます。 切断によって VNet に変更は生じません。 サブネットやゲートウェイは削除されません。 その後で VNet を削除する場合は、まず VNet からアプリを切断し、ゲートウェイなどのそこに含まれているリソースを削除する必要があります。 

ASP VNet 統合 UI には、ASP でアプリが使用しているすべての VNet 統合が表示されます。 各 VNet の詳細を表示するには、目的の VNet をクリックします。 ここでは、ゲートウェイが必要な VNet 統合に対して実行できるアクションが 2 つあります。

* **ネットワークを同期する**。 ネットワーク同期操作は、ゲートウェイに依存する VNet 統合機能のためにのみ存在します。 ネットワーク同期操作を実行すると、証明書とネットワークの情報が確実に同期されるようになります。VNet の DNS を変更する場合は、**ネットワークの同期**操作を実行する必要があります。 この操作によって、この VNet を使用しているすべてのアプリが再起動されます。
* **ルートを追加する** ルートを追加すると、送信トラフィックが VNet に誘導されます。 

**ゲートウェイが必要な VNet 統合のルーティング** VNet に定義されたルートは、アプリから VNet にトラフィックを転送するために使用されます。 VNet に追加の送信トラフィックを送信する必要がある場合は、ここでそれらのアドレス ブロックを追加できます。 この機能は、ゲートウェイが必要な Vnet 統合でのみ動作します。 ルート テーブルは、ゲートウェイが必要な VNet 統合を使用する場合、アプリのトラフィックに影響しません。これはリージョン VNet 統合の場合とは異なります。

**ゲートウェイが必要な VNet 統合の証明書** ゲートウェイが必要な Vnet 統合が有効になっている場合は、接続のセキュリティを確保するために証明書の交換が必要です。 証明書と共に、DNS の構成、ルート、ネットワークについて記述するその他の同様の情報があります。
証明書またはネットワーク情報が変更された場合は、[ネットワークの同期] をクリックする必要があります。 [ネットワークの同期] をクリックすると、アプリと VNet の間の接続が短時間途切れます。 アプリが再起動されていない間は、接続の途切れによって、サイトが正常に機能しなくなる可能性があります。 

## <a name="pricing-details"></a>価格の詳細
リージョン Vnet 統合機能には、ASP の価格レベルの料金を超える追加の使用料金はありません。

ゲートウェイが必要な Vnet 統合機能の使用に関連する料金には、次の 3 つがあります。

* ASP の価格レベルの料金 - アプリは Standard、Premium、または PremiumV2 の App Service プランに属している必要があります。 これらのコストの詳細については、「[App Service の価格][ASPricing]」を参照してください。 
* データ転送のコスト - VNet が同じデータ センター内に存在する場合でも、データ エグレスの料金が発生します。 これらの料金は、「[帯域幅の料金詳細][DataPricing]」で説明されています。 
* VPN Gateway のコスト - ポイント対サイト VPN に必要な VNet ゲートウェイに対してコストが発生します。 これらの詳細は、「[VPN Gateway の価格][VNETPricing]」のページに記載されています。

## <a name="troubleshooting"></a>トラブルシューティング
この機能は簡単にセットアップできるものの、問題が発生しないわけではありません。 目的のエンドポイントへのアクセスに関して問題が発生した場合は、アプリのコンソールからの接続テストに、いくつかのユーティリティを利用できます。 利用できるコンソールが 2 つあります。 1 つは Kudu コンソールで、もう 1 つは Azure portal 内のコンソールです。 アプリから Kudu コンソールにアクセスするには、[ツール]、[Kudu] の順に移動します。 [サイト名].scm.azurewebsites.net で Kudo コンソールにアクセスすることもできます。 Web サイトが読み込まれたら、[デバッグ コンソール] タブに移動します。Azure ポータルにホストされたコンソールにアクセスするには、アプリで [ツール]、[コンソール] の順に移動します。 

#### <a name="tools"></a>ツール
**ping**、**nslookup**、**tracert** の各ツールは、セキュリティの制約により、コンソールから使用することはできません。 それを補うために、2 つの独立したツールが追加されています。 DNS 機能のテスト用に、nameresolver.exe という名前のツールを追加しました。 の構文は次のとおりです。

    nameresolver.exe hostname [optional: DNS Server]

**nameresolver** を使用すると、アプリが依存しているホスト名を確認できます。 この方法で、DNS の構成に誤りがあるかどうかや、DNS サーバーへのアクセス権がないかどうかをテストできます。 環境変数 WEBSITE_DNS_SERVER と WEBSITE_DNS_ALT_SERVER を調べることによって、アプリが使用する DNS サーバーをコンソールで確認できます。

次のツールでは、ホストとポートを組み合わせたものへの TCP 接続をテストすることができます。 このツールは **tcpping** という名前で、構文は次のとおりです。

    tcpping.exe hostname [optional: port]

**tcpping** ユーティリティを使用すると、特定のホストとポートにアクセスできるかどうかがわかります。 成功として示されるのは、ホストとポートの組み合わせでリッスンしているアプリケーションがあり、アプリから指定のホストとポートへのネットワーク アクセスがある場合のみです。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>VNet にホストされたリソースへのアクセスのデバッグ
アプリから特定のホストとポートへのアクセスは、さまざまな要因によって妨げられる可能性があります。 ほとんどの場合、次の 3 つのうちのいずれかです。

* **ファイアウォールがルートを塞いでいる。** ルートを塞いでいるファイアウォールがあると、TCP のタイムアウトに達します。 この場合の TCP タイムアウトは 21 秒です。 **tcpping** ツールを使用して接続をテストします。 TCP タイムアウトの原因は、ファイアウォール以外にさまざまなことが考えられますが、まずここから始めます。 
* **DNS にアクセスできない。** DNS タイムアウトは、DNS サーバーごとに 3 秒です。 2 つの DNS サーバーがある場合、タイムアウトは 6 秒です。 nameresolver を使用して、DNS が機能しているかどうかを確認します。 nslookup は使用できないことに注意してください。これは、nslookup が VNet の構成に使用されている DNS を使用しないためです。 アクセスできない場合は、ファイアウォールが存在するか、NSG が DNS へのアクセスをブロックしているか、または DNS が停止している可能性があります。

これらの項目が問題の回答になっていない場合は、まず次のような点を確認してください。 

**リージョン Vnet 統合**
* 宛先は RFC1918 以外のアドレスであり、WEBSITE_VNET_ROUTE_ALL が 1 に設定されていないこと。
* 統合サブネットからのエグレスをブロックしている NSG は存在するか。
* ExpressRoute または VPN をまたがって移動する場合は、オンプレミスのゲートウェイがトラフィック バックアップを Azure にルーティングするように構成されているか。 VNet 内のエンドポイントには到達できるが、オンプレミスに到達できない場合は、ルートを確認します。
* 統合サブネットに委任を設定するための十分なアクセス許可があるか。 リージョン VNet 統合が構成されている間、統合サブネットは Microsoft.Web に委任されます。 VNet 統合 UI では、Microsoft Web に対するサブネットが自動的に委任されます。 アカウントに委任を設定するための十分なネットワークのアクセス許可がない場合は、サブネットを委任するために、統合サブネットに属性を設定できるユーザーが必要になります。 統合サブネットを手動で委任するには、Azure Virtual Network サブネット UI にアクセスして、Microsoft.Web の委任を設定します。 

**ゲートウェイが必要な Vnet 統合**
* ポイント対サイトのアドレス範囲が RFC 1918 の範囲 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255) にあるか。
* ゲートウェイはポータルに稼働中と表示されているか。 ゲートウェイがダウンしている場合は、再起動してください。
* 証明書は同期していると表示されているか。または、ネットワーク構成が変更されたことが疑われるか。  証明書が同期していないか、または ASP と同期していない VNet 構成への変更が行われたことが疑われる場合は、[ネットワークの同期] をクリックします。
* VPN をまたがって移動する場合は、オンプレミスのゲートウェイがトラフィック バックアップを Azure にルーティングするように構成されているか。 VNet 内のエンドポイントには到達できるが、オンプレミスに到達できない場合は、ルートを確認します。
* ポイント対サイトと ExpressRoute の両方をサポートする共存ゲートウェイを使用しようとしているか。 VNet 統合では、共存ゲートウェイはサポートされていません 

ホストとポートの特定の組み合わせへのアクセスを何がブロックしているかを確認できないため、ネットワークに関する問題のデバッグは課題です。 以下に原因の例を示します。

* ホスト上で稼働しているファイアウォールが、ポイント対サイト IP の範囲からアプリケーション ポートへのアクセスを妨げている。 サブネットの境界を越えるには、多くの場合パブリック アクセスが必要になります。
* ターゲット ホストがダウンしている
* アプリケーションがダウンしている
* IP またはホスト名が誤っている
* アプリケーションが予期しないポートでリッスンしている。 エンドポイント ホストで "netstat-aon" を使用することで、プロセス ID と、リッスンしているポートを一致させることができます。 
* ネットワーク セキュリティ グループが、ポイント対サイト IP の範囲からアプリケーション ホストとポートへのアクセスをブロックするように構成されている

アプリが実際にどのようなアドレスを使用するかは認識できないことに注意してください。 統合サブネットまたはポイント対サイトのアドレス範囲内の任意のアドレスである可能性があるため、アドレス範囲全体からのアクセスを許可する必要があります。 

追加のデバッグ手順は次のとおりです。

* VNet 内の VM に接続し、そこからリソースのホスト:ポートへのアクセスを試します。 TCP アクセスのテストには、PowerShell コマンド **test-netconnection** を使用します。 の構文は次のとおりです。

      test-netconnection hostname [optional: -Port]

* VM 上でアプリケーションを起動し、**tcpping** を使用して、アプリのコンソールからそのホストとポートへのアクセスをテストします。

#### <a name="on-premises-resources"></a>オンプレミスのリソース ####

アプリがオンプレミスのリソースにアクセスできない場合は、VNet からリソースにアクセスできるかどうかを確認します。 TCP アクセスを確認するには、PowerShell コマンド **test-netconnection** を使用します。 VM がオンプレミス リソースに到達できない場合は、VPN または ExpressRoute 接続が正しく構成されていない可能性があります。

VNet でホストされている VM はオンプレミス システムにアクセスでき、アプリはアクセスできない場合、以下の理由のいずれかが原因と考えられます。

* オンプレミスのゲートウェイで、ルートがサブネットまたはポイント対サイトのアドレス範囲で構成されていない。
* ネットワーク セキュリティ グループが、ポイント対サイト IP 範囲へのアクセスをブロックしている。
* オンプレミスのファイアウォールが、ポイント対サイト IP 範囲からのトラフィックをブロックしている。
* リージョン Vnet 統合機能を使用して、RFC 1918 以外のアドレスに到達しようとしている。


## <a name="automation"></a>Automation

リージョン VNet 統合では CLI がサポートされています。 次のコマンドにアクセスするには、[Azure CLI をインストール][installCLI]します。 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

ゲートウェイが必要な VNet 統合では、PowerShell を使用して App Service を Azure Virtual Network と統合できます。 すぐに使用できるスクリプトについては、「[Connect an app in Azure App Service to an Azure Virtual Network (Azure App Service のアプリを Azure 仮想ネットワークに接続する)](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)」をご覧ください。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
