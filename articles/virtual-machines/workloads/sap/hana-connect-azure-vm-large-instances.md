---
title: 仮想マシンから SAP HANA on Azure (L インスタンス) への接続のセットアップ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) を使用した仮想マシンからの接続のセットアップ。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167677"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>HANA L インスタンスへの Azure VM の接続

既に「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」でも触れたように、Azure における SAP アプリケーション レイヤーと HANA L インスタンスの最小デプロイ環境は次のようになります。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Azure VNet 側をよく見ると、次のものが必要であることがわかります。

- SAP アプリケーション レイヤーの VM のデプロイ先となる Azure VNet の定義。
- これは、Azure VNet に定義されている既定のサブネットが、実際に VM のデプロイ先として使用されることを意味します。
- 作成される Azure VNet には、少なくとも 1 つの VM サブネットと 1 つの ExpressRoute ゲートウェイ サブネットが必要です。 これらのサブネットには、以降のセクションで指定および説明されているとおりに IP アドレス範囲を割り当てる必要があります。

それでは、HANA L インスタンスに使用する Azure VNet の作成についてもう少し詳しく見ていきましょう。

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Azure VNet for HANA L インスタンスの作成

>[!Note]
>Azure VNet for HANA L インスタンスは、Azure Resource Manager デプロイ モデルを使って作成する必要があります。 クラシック デプロイ モデルとして一般的に知られている以前の Azure デプロイ モデルは、HANA L インスタンス ソリューションではサポートされていません。

VNet は、Azure Portal、PowerShell、Azure テンプレート、または Azure CLI を使用して作成することができます ([Azure Portal を使用した仮想ネットワークの作成](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)に関するページを参照してください)。 以下の例では、Azure Portal で作成した VNet について詳しく説明しています。

Azure VNet の定義を Azure Portal で見ながら、いくつかの定義と、それらがさまざまな IP アドレス範囲の一覧とどのように関係しているかを見ていきます。 ここで用いている**アドレス空間**とは、Azure VNet が使用できるアドレス空間を意味します。 このアドレス空間は、VNet が BGP ルートの伝播に使用するアドレス範囲でもあります。 この**アドレス空間**は、次の画面で確認できます。

![Azure Portal に表示される Azure VNet のアドレス空間](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

前のケースでは、Azure VNet で使用される IP アドレス範囲が 10.16.0.0/16 と、かなり広範囲に割り当てられています。 つまり、この VNet に後から追加されるサブネットにはすべて、このアドレス空間内の IP アドレス範囲を割り当てることができます。 通常 Azure では、このように大きなアドレス範囲を 1 つの VNet に使用することは推奨されません。 しかし、Azure VNet に定義されるサブネットについて、さらに踏み込んで見てみましょう。

![Azure VNet のサブネットとその IP アドレス範囲](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

ご覧のように、この VNet にはまず "default" という VM サブネットがあり、さらに "GatewaySubnet" というサブネットがあります。
次のセクションでは、この図の "default" サブネットの IP アドレス範囲を **Azure VM サブネット IP アドレス範囲**と呼びます。 また、ゲートウェイ サブネットの IP アドレス範囲を **VNet ゲートウェイ サブネット IP アドレス範囲**と呼びます。 

上の 2 つの図に示したケースでは、**VNet アドレス空間**に、**Azure VM サブネット IP アドレス範囲**と **VNet ゲートウェイ サブネット IP アドレス範囲**の両方が含まれていることがわかります。 

アドレス空間を節約しなければならないケースや IP アドレス範囲が限られているケースでは、VNet の **VNet アドレス空間**を各サブネットで使用されている特定の範囲に限定することができます。 このケースでは、はっきり限定された複数の範囲として次のように **VNet アドレス空間**を定義することができます。

![2 つの空間から成る Azure VNet アドレス空間](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

このケースでは、**VNet アドレス空間**に 2 つの空間が定義されています。 この 2 つの空間は、**Azure VM サブネット IP アドレス範囲**と **VNet ゲートウェイ サブネット IP アドレス範囲**に対して定義されている IP アドレス範囲と同じです。

これらのテナント サブネット (VM サブネット) には、任意の命名標準を使用できます。 ただし、**各 VNet にゲートウェイ サブネットが常に 1 つだけ存在する必要があります**。それが SAP HANA on Azure (L インスタンス) ExpressRoute 回線に接続します。 また、**このゲートウェイ サブネットは、常に "GatewaySubnet" という名前である必要があります**。これは、ExpressRoute ゲートウェイが適切に配置されるようにするためです。

> [!WARNING] 
> ゲートウェイ サブネットに必ず "GatewaySubnet" と命名することは非常に重要です。

使用するアドレス範囲が不連続であっても、複数の VM サブネットを使用することができます。 ただし前述のように、そのようなアドレス範囲は、集約された形式か、VM サブネットとゲートウェイ サブネットの範囲を厳密に列挙したリスト形式の **VNet アドレス空間**に含まれている必要があります。

HANA L インスタンスに接続する Azure VNet について重要な情報を以下にまとめます。

- HANA L インスタンスの初期デプロイを行う際、**VNet アドレス空間**を Microsoft に提出する必要があります。 
- **VNet アドレス空間**は、Azure VM サブネット IP アドレス範囲と VNet ゲートウェイ サブネット IP アドレス範囲を含む 1 つの大きな範囲です。
- また、VM サブネット IP アドレス範囲と VNet ゲートウェイ サブネット IP アドレス範囲という異なる IP アドレス範囲を含んだ複数の範囲を **VNet アドレス空間**として送信することもできます。
- 定義した **VNet アドレス空間**は、BGP ルートの伝播に使用されます。
- ゲートウェイ サブネットの名前は **"GatewaySubnet"** にする必要があります。
- **VNet アドレス空間**は、HANA L インスタンス側で、Azure から HANA L インスタンス ユニットへのトラフィックを許可または拒否するためのフィルターとして使用されます。 Azure VNet の BGP ルーティング情報と、HANA L インスタンス側でフィルター処理用に構成されている IP アドレス範囲とが一致しない場合、接続に問題が生じることがあります。
- ゲートウェイ サブネットについては、「HANA L インスタンスの ExpressRoute への VNet の接続」セクションでさらに詳しく説明します。



## <a name="different-ip-address-ranges-to-be-defined"></a>定義する各種の IP アドレス範囲 

前のセクションで、HANA L インスタンスをデプロイするうえで必要な IP アドレス範囲をいくつか挙げました。 しかし、重要な IP アドレス範囲は他にも存在します。 そのいくつかについて詳しく見ていきましょう 初期デプロイを依頼する前に、以下の IP アドレス (Microsoft に提出する必要があるのは一部のみ) を定義する必要があります。

- **VNet アドレス空間:** 前述したように、SAP HANA L インスタンス環境に接続する Azure Virtual Network (VNet) のアドレス空間パラメーターに割り当て済み (または割り当て予定) の IP アドレス範囲が VNet アドレス空間です。 このアドレス空間パラメーターは、Azure VM サブネット範囲と Azure ゲートウェイ サブネット範囲から成る複数行の値として指定することをお勧めします (前の図を参照)。 この範囲をオンプレミスのアドレス範囲やサーバー IP プールのアドレス範囲、ER-P2P のアドレス範囲と重複させることはできません。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 たとえば、Azure VM サブネット (上の行) が 10.0.1.0/24 で、Azure ゲートウェイ サブネット (下の行) が 10.0.2.0/28 である場合、Azure VNet アドレス空間は 2 行 (10.0.1.0/24 と 10.0.2.0/28) に分けて記述することをお勧めします。 アドレス空間の値は集約することもできますが、将来ネットワーク内のどこかで大きなアドレス空間内の未使用の IP アドレス範囲が誤って再利用されることを防ぐために、サブネット範囲に合わせることをお勧めします。 **VNET アドレス空間は、初期デプロイを依頼する際、Microsoft に提出する必要がある IP アドレス範囲です。**

- **Azure VM サブネット IP アドレス範囲:** 前述したように、SAP HANA L インスタンス環境に接続する Azure VNET の Azure VNet サブネット パラメーターに割り当て済み (または割り当て予定) の IP アドレス範囲です。 この IP アドレス範囲は、Azure VM に IP アドレスを割り当てる目的で使用されます。 この範囲外の IP アドレスは、SAP HANA L インスタンス サーバーへの接続が許可されます。 必要に応じて、複数の Azure VM サブネットを使用できます。 Microsoft は、各 Azure VM サブネットに /24 CIDR ブロックの使用を推奨しています。 このアドレス範囲は、Azure VNet アドレス空間に使用されている値の範囲内であることが必要です。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。

- **VNet ゲートウェイ サブネット IP アドレス範囲:** 推奨されるサイズは、使用する予定の機能によって異なります。
   - Ultra-performance ExpressRoute ゲートウェイ: /26 アドレス ブロック - Type II クラスの SKU で必要
   - High Performance ExpressRoute ゲートウェイ (以下) を使った ExpressRoute と VPN の共存: /27 アドレス ブロック
   - その他すべて: /28 アドレス ブロック。 このアドレス範囲は、"VNet アドレス空間" に使用されている値の範囲内であることが必要です。 このアドレス範囲は、Microsoft に提出する必要のある Azure VNet アドレス空間に使用されている値の範囲内であることが必要です。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 

- **ER-P2P 接続のアドレス範囲:** SAP HANA L インスタンスの ExpressRoute (ER) P2P 接続に使用する IP 範囲です。 この範囲の IP アドレスは、/29 CIDR の IP アドレス範囲であることが必要です。 この範囲をオンプレミスのアドレス範囲や他の Azure IP アドレス範囲と重複させることはできません。 この IP アドレス範囲は、Azure VNet ExpressRoute ゲートウェイから SAP HANA L インスタンス サーバーへの ER 接続を設定する際に使用されます。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 **この IP アドレス範囲は、初期デプロイを依頼する際、Microsoft に提出する必要があります。**
  
- **サーバー IP プール アドレス範囲:** HANA L インスタンス サーバーに個々の IP アドレスを割り当てるときに、この IP アドレス範囲が使用されます。 推奨サブネット サイズは /24 CIDR ブロックですが、必要であればもっと小さくすることもできます (IP アドレス数の下限は 64 個)。 この範囲の先頭から 30 個の IP アドレスは、Microsoft 用に予約されています。 範囲のサイズを選ぶ際は、必ずこの点を考慮してください。 この範囲をオンプレミスのアドレス範囲や他の Azure IP アドレスと重複させることはできません。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 /24 (推奨) の一意の CIDR ブロック。SAP HANA on Azure (L インスタンス) に必要な特定の IP アドレスの割り当てに使用します。 **この IP アドレス範囲は、初期デプロイを依頼する際、Microsoft に提出する必要があります。**
 
以上の IP アドレス範囲を自分で設計、定義する必要があります。ただしそのすべてを Microsoft に送信する必要はありません。 これらを要約すると、Microsoft に提出する必要がある IP アドレス範囲は次のとおりです。

- Azure VNet アドレス空間
- ER-P2P 接続のアドレス範囲
- サーバー IP プールのアドレス範囲

HANA L インスタンスへの接続を必要とする VNet をさらに追加する場合は、新たに追加する Azure VNet アドレス空間を Microsoft に提出する必要があります。 

以下に示したのは、各種アドレス範囲の例です。また、ご自分で構成して最終的に Microsoft に提出することになるアドレス範囲の例もいくつか挙げています。 これを見るとわかるように、1 つ目の例では、Azure VNet アドレス空間の値が集約されておらず、先頭の Azure VM サブネット IP アドレス範囲と VNet ゲートウェイ サブネット IP アドレス範囲に基づいて定義されています。 Azure VNet 内で複数の VM サブネットを使用する場合、Azure VNet アドレス空間に追加する新しい VM サブネットの IP アドレス範囲を新たに構成して提出すれば、それに応じて適切に機能します。

![SAP HANA on Azure (L インスタンス) の最小デプロイ環境に必要な IP アドレス範囲](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Microsoft に提出するデータは集約することもできます。 その場合、Azure VNet のアドレス空間に含まれる空間は 1 つだけです。 上の例で使用した IP アドレス範囲を使用しています。 この集約された VNet アドレス空間は、次のようになります。

![SAP HANA on Azure (L インスタンス) の最小限のデプロイに必要な IP アドレス範囲の例 (その 2)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

小さい範囲を 2 つ使って Azure VNet のアドレス空間を定義する代わりに、4,096 個の IP アドレスを含んだ、より大きい範囲が 1 つ使われていることがわかります。 このように大きなアドレス空間を定義すると、かなり大きな範囲が未使用のまま残ってしまいます。 VNet アドレス空間の値は BGP ルートの伝播に使用されるため、未使用の範囲がオンプレミスまたはネットワーク内のどこかで使用されると、ルーティングに問題が生じる可能性があります。 そのため VNet アドレス空間は、実際に使用するサブネットのアドレス空間の境界と厳密に合わせることをお勧めします。 必要に応じて、VNet にダウンタイムを発生させることなく、後からいつでも新しいアドレス空間の値を追加することができます。
 
> [!IMPORTANT] 
> ER-P2P、サーバー IP プール、Azure VNet アドレス空間の各 IP アドレス範囲は、互いに (またはネットワーク内のどこかで使用されている他の範囲と) **重複しない**ようにしてください。つまりそれぞれの IP アドレス範囲は不連続であること、そして他の範囲のサブネットになっていないことが必要です (上の 2 つの図を参照)。 範囲が重複すると、Azure VNet が ExpressRoute 回線に接続できなくなります。

## <a name="next-steps-after-address-ranges-have-been-defined"></a>アドレス範囲を定義した後に行う手順
IP アドレス範囲の定義後、次の作業を行う必要があります。

1. Azure VNet アドレス空間の IP アドレス範囲、ER-P2P 接続のアドレス範囲、サーバー IP プールのアドレス範囲を、このドキュメントの冒頭で挙げた他のデータと一緒に提出します。 この時点で VNet と VM サブネットの作成を開始することもできます。 
2. Azure サブスクリプションと HANA L インスタンス スタンプの間の ExpressRoute 回線が Microsoft によって作成されます。
3. Microsoft によって、L インスタンス スタンプにテナント ネットワークが作成されます。
4. Microsoft が SAP HANA on Azure (L インスタンス) インフラストラクチャ内に、HANA L インスタンスと通信する Azure VNet アドレス空間からの IP アドレスを受け入れるネットワークを構成します。
5. 注文された SAP HANA on Azure (L インスタンス) SKU に応じて、Microsoft がテナント ネットワークにコンピューティング ユニットを割り当て、ストレージを割り当ててマウントし、オペレーティング システム (SUSE または Red Hat Linux) をインストールします。 これらのユニットの IP アドレスは、お客様が Microsoft に提出したサーバー IP プール アドレス範囲から割り当てられます。

デプロイ プロセスの最後に、Microsoft からお客様に以下のデータが送信されます。
- Azure VNet を HANA L インスタンスに接続する ExpressRoute 回線にお客様の Azure VNet を接続するために必要な情報。
     - 承認キー
     - ExpressRoute PeerID
- ExpressRoute 回線と Azure VNet を設定した後で HANA L インスタンスにアクセスするためのデータ。

HANA L インスタンスを接続する手順については、ドキュメント「[End to End Setup for SAP HANA Large Instances (SAP HANA L インスタンスのエンド ツー エンドのセットアップ)](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)」で確認することもできます。 以下の手順の多くは、そのドキュメントのデプロイの例に示されています。 

**次のステップ**

- 「[HANA L インスタンスの ExpressRoute への VNet の接続](hana-connect-vnet-express-route.md)」を参照してください。