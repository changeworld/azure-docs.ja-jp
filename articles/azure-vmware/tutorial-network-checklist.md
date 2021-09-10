---
title: チュートリアル - ネットワーク計画のチェックリスト
description: Azure VMware Solution のネットワーク接続とネットワーク ポートのネットワーク要件について説明します。
ms.topic: tutorial
ms.date: 07/01/2021
ms.openlocfilehash: cee4768cbf6382b3cce229267f9b2483c353b464
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123314497"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Azure VMware Solution のネットワーク計画のチェックリスト 

Azure VMware Solution は、オンプレミスおよび Azure ベースの環境またはリソースからユーザーやアプリケーションがアクセスできる VMware プライベート クラウド環境を提供します。 接続は、Azure ExpressRoute 接続や VPN 接続などのネットワーク サービスを通じて提供されます。 サービスを有効にするには、特定のネットワーク アドレス範囲とファイアウォール ポートが必要です。 この記事では、Azure VMware Solution で動作するようにネットワークを適切に構成するために必要な情報を提供します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想ネットワークと ExpressRoute 回線に関する考慮事項
> * ルーティングとサブネットの要件
> * サービスと通信するために必要なネットワーク ポート
> * Azure VMware Solution での DHCP と DNS に関する考慮事項

## <a name="prerequisite"></a>前提条件
ExpressRoute プロバイダーのサービスを含め、すべてのゲートウェイで 4 バイトの自律システム番号 (ASN) がサポートされていることを確認します。 Azure VMware Solution では、4 バイトのパブリック ASN を使用してルートをアドバタイズします。

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>仮想ネットワークと ExpressRoute 回線に関する考慮事項
サブスクリプション内に仮想ネットワーク接続を作成するときには、ピアリングによって ExpressRoute 回線が確立され、Azure portal で要求した承認キーとピアリング ID が使用されます。 ピアリングは、プライベート クラウドと仮想ネットワークの間のプライベートな 1 対 1 の接続です。

> [!NOTE] 
> ExpressRoute 回線は、プライベート クラウドのデプロイには含まれません。 オンプレミスの ExpressRoute 回線については、このドキュメントでは取り扱いません。 プライベート クラウドへのオンプレミス接続が必要な場合は、既存の ExpressRoute 回線のいずれかを使用するか、Azure portal で購入することができます。

プライベート クラウドをデプロイすると、vCenter および NSX-T Manager の IP アドレスが提供されます。 これらの管理インターフェイスにアクセスするには、ご使用のサブスクリプションの仮想ネットワークにさらに多くのリソースを作成する必要があります。 これらのリソースを作成し、[ExpressRoute プライベート ピアリング](tutorial-expressroute-global-reach-private-cloud.md)を確立する手順については、チュートリアルを参照してください。

プライベート クラウドの論理ネットワークには、事前にプロビジョニングされた NSX-T が付属しています。 Tier-0 ゲートウェイと Tier-1 ゲートウェイが事前にプロビジョニングされています。 セグメントを作成し、それを既存の Tier-1 ゲートウェイにアタッチすることも、自分が定義する新しい Tier-1 ゲートウェイにアタッチすることもできます。 NSX-T 論理ネットワーク コンポーネントは、ワークロード間の East-West 接続と、インターネットおよび Azure サービスへの North-South 接続を提供します。

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

## <a name="routing-and-subnet-considerations"></a>ルーティングとサブネットに関する考慮事項
Azure VMware Solution プライベート クラウドは、Azure ExpressRoute 接続を使用して Azure 仮想ネットワークに接続されます。 この高い帯域幅と短い待ち時間の接続によって、ご自分のプライベート クラウド環境から、ご自分の Azure サブスクリプションで実行されているサービスにアクセスできます。 ルーティングは Border Gateway Protocol (BGP) ベースであり、プライベート クラウドのデプロイごとに既定で自動的にプロビジョニングされ、有効になります。 

Azure VMware Solution プライベート クラウドでは、次に示すように、サブネット用に少なくとも `/22` の CIDR ネットワーク アドレス ブロックが必要です。 このネットワークによって、ご自分のオンプレミス ネットワークが補完されます。 そのため、アドレス ブロックは、サブスクリプションの他の仮想ネットワークやオンプレミス ネットワークで使用されているアドレス ブロックと重複しないようにする必要があります。 このアドレス ブロック内では、管理、プロビジョニング、vMotion ネットワークが自動的にプロビジョニングされます。

>[!NOTE]
>アドレス ブロックで許可される範囲は RFC 1918 プライベート アドレス空間 (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) です。ただし、172.17.0.0/16 を除きます。

`/22` CIDR ネットワーク アドレス ブロックの例: `10.10.0.0/22`

サブネット:

| ネットワークの使用法             | Subnet | 例          |
| ------------------------- | ------ | ---------------- |
| プライベート クラウドの管理  | `/26`  | `10.10.0.0/26`   |
| HCX 管理の移行       | `/26`  | `10.10.0.64/26`  |
| 予約済み Global Reach     | `/26`  | `10.10.0.128/26` |
| NSX-T DNS サービス         | `/32`  | `10.10.0.192/32` |
| 予約済み                  | `/32`  | `10.10.0.193/32` |
| 予約済み                  | `/32`  | `10.10.0.194/32` |
| 予約済み                  | `/32`  | `10.10.0.195/32` |
| 予約済み                  | `/30`  | `10.10.0.196/30` |
| 予約済み                  | `/29`  | `10.10.0.200/29` |
| 予約済み                  | `/28`  | `10.10.0.208/28` |
| ExpressRoute ピアリング      | `/27`  | `10.10.0.224/27` |
| ESXi の管理           | `/25`  | `10.10.1.0/25`   |
| vMotion ネットワーク           | `/25`  | `10.10.1.128/25` |
| レプリケーション ネットワーク       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX アップリンク                | `/26`  | `10.10.3.0/26`   |
| 予約済み                  | `/26`  | `10.10.3.64/26`  |
| 予約済み                  | `/26`  | `10.10.3.128/26` |
| 予約済み                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>必要なネットワーク ポート

| source | 宛先 | Protocol | Port | 説明  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| プライベート クラウドの DNS サーバー | オンプレミスの DNS サーバー | UDP | 53 | DNS クライアント - オンプレミスの DNS クエリのために、PC vCenter からの要求を転送します (後述の DNS に関するセクションを確認してください) |  
| オンプレミスの DNS サーバー   | プライベート クラウドの DNS サーバー | UDP | 53 | DNS クライアント - オンプレミスのサービスからの要求をプライベート クラウド DNS サーバーに転送します (後述の DNS に関するセクションを確認してください) |  
| オンプレミス ネットワーク  | プライベート クラウドの vCenter Server  | TCP (HTTP)  | 80 | vCenter Server では、直接 HTTP 接続用にポート 80 が必要です。 ポート 80 から HTTPS ポート 443 に要求がリダイレクトされます。 このリダイレクトは、`https://server` の代わりに `http://server` を使用する場合に役立ちます。  |  
| プライベート クラウドの管理ネットワーク | オンプレミスの Active Directory  | TCP  | 389/636 | これらのポートは、Azure VMware Solutions vCenter が任意のオンプレミスの Active Directory/LDAP サーバーと通信するための通信を許可するために開いています。  これらのポートはオプションです。プライベート クラウドの vCenter 上でオンプレミス AD を ID ソースとして構成するために使用します。 セキュリティ上の目的でポート 636 がお勧めです。 |  
| プライベート クラウドの管理ネットワーク | オンプレミスの Active Directory のグローバル カタログ  | TCP  | 3268/3269 | これらのポートは、Azure VMware Solutions vCenter が任意のオンプレミスの Active Directory/LDAP グローバル カタログ サーバーと通信するための通信を許可するために開いています。  これらのポートはオプションです。プライベート クラウドの vCenter 上でオンプレミス AD を ID ソースとして構成するために使用します。 セキュリティ上の目的でポート 3269 がお勧めです。 |  
| オンプレミス ネットワーク  | プライベート クラウドの vCenter Server  | TCP (HTTPS)  | 443 | このポートを使用すると、オンプレミス ネットワークから vCenter にアクセスできます。 vCenter Server システムが vSphere クライアントからの接続をリッスンするために使用する既定のポート。 vCenter Server システムが vSphere クライアントからデータを受信できるようにするには、ファイアウォールでポート 443 を開きます。 また、vCenter Server システムは、ポート 443 を使用して、SDK クライアントからのデータ転送を監視します。 |  
| オンプレミス ネットワーク  | HCX Manager  | TCP (HTTPS) | 9443 | Hybrid Cloud Manager のシステム構成用の Hybrid Cloud Manager 仮想アプライアンス管理インターフェイス。 |
| 管理ネットワーク  | Hybrid Cloud Manager | SSH | 22 | Hybrid Cloud Manager への管理者 SSH アクセス。 |
| HCX Manager | クラウド ゲートウェイ | TCP (HTTPS) | 8123 | ホストベースのレプリケーション サービスの指示をハイブリッド クラウド ゲートウェイに送信します。 |
| HCX Manager | クラウド ゲートウェイ | HTTP TCP (HTTPS) | 9443 | REST API を使用してローカルのハイブリッド クラウド ゲートウェイに管理指示を送信します。 |
| クラウド ゲートウェイ | L2C | TCP (HTTPS) | 443 | L2C がハイブリッド クラウド ゲートウェイと同じパスを使用する場合、クラウド ゲートウェイから L2C に管理指示を送信します。 |
| クラウド ゲートウェイ | ESXi ホスト | TCP | 80、902 | 管理と OVF のデプロイ。 |
| クラウド ゲートウェイ (ローカル)| クラウド ゲートウェイ (リモート) | UDP | 4500 | IPsec に必要<br>   双方向トンネルのワークロードをカプセル化するためのインターネット キー交換 (IKEv2)。 ネットワーク アドレス変換トラバーサル (NAT-T) もサポートされています。 |
| クラウド ゲートウェイ (ローカル) | クラウド ゲートウェイ (リモート)  | UDP | 500 | IPsec に必要<br> 双方向トンネルのためのインターネット キー交換 (ISAKMP)。 |
| オンプレミスの vCenter ネットワーク | プライベート クラウドの管理ネットワーク | TCP | 8000 |  オンプレミスの vCenter Server からプライベート クラウドの vCenter への VM の vMotion   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP と DNS の解決に関する考慮事項

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VMware Solution プライベート クラウドをデプロイするための考慮事項と要件について説明しました。 適切なネットワークを準備できたら、次のチュートリアルに進んで Azure VMware Solution のプライベート クラウドを作成します。

> [!div class="nextstepaction"]
> [Azure VMware Solution のプライベート クラウドを作成する](tutorial-create-private-cloud.md)
