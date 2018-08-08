---
title: Azure Stack 統合システムのネットワーク統合に関する考慮事項 | Microsoft Docs
description: マルチノードの Azure Stack とデータセンター ネットワークの統合を計画するためにできることを説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: f54849b9fef8ff09aa9b5b6254c92fc3c452ef4a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414427"
---
# <a name="network-connectivity"></a>ネットワーク接続
この記事では、Azure Stack を既存のネットワーク環境に統合する最善の方法を決定するために役立つ Azure Stack ネットワーク インフラストラクチャの情報を提供します。 

> [!NOTE]
> Azure Stack から外部の DNS 名を解決するには (たとえば www.bing.com)、DNS 要求を転送するための DNS サーバーを提供する必要があります。 Azure Stack の DNS 要件の詳細については、「[Azure Stack とデータセンターの統合 - DNS](azure-stack-integrate-dns.md)」をご覧ください。

## <a name="physical-network-design"></a>物理ネットワークの設計
Azure Stack ソリューションには、その操作やサービスをサポートするための回復性と可用性の高い物理インフラストラクチャが必要です。 ToR から境界スイッチへのアップリンクは、SFP + または SFP28 メディア、1 GB、10 GB、または 25 GB の速度に制限されます。 OEM (Original Equipment Manufacturer) ハードウェア ベンダーに可用性を確認してください。 推奨される設計を次の図に示します。

![推奨される Azure Stack ネットワークの設計](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>論理ネットワーク
論理ネットワークは、基礎となる物理ネットワーク インフラストラクチャの抽象化を表します。 これらは、ホスト、仮想マシン、およびサービスへのネットワーク割り当てを整理および簡略化するために使用されます。 論理ネットワーク作成の一部として、それぞれの物理的な場所にある論理ネットワークに関連付けられた仮想ローカル エリア ネットワーク (VLAN)、IP サブネット、および IP サブネット/VLAN ペアを定義するためのネットワーク サイトが作成されます。

次の表に、論理ネットワークと、計画する必要がある関連付けられた IPv4 サブネット範囲を示します。

| 論理ネットワーク | 説明 | サイズ | 
| -------- | ------------- | ------------ | 
| パブリック VIP | Azure Stack では、このネットワークからの合計 32 個のアドレスが使用されます。 少数の Azure Stack サービスに 8 個のパブリック IP アドレスが使用されます。残りはテナント仮想マシンによって使用されます。 App Service と SQL リソース プロバイダーを使用する場合は、さらに 7 個のアドレスを使用します。 | /26 (62 ホスト) - /22 (1022 ホスト)<br><br>推奨 = /24 (254 ホスト) | 
| スイッチのインフラストラクチャ | ルーティングを目的としたポイント ツー ポイント IP アドレス (スイッチ管理専用インターフェイス) と、スイッチに割り当てられたループバック アドレス。 | /26 | 
| インフラストラクチャ | Azure Stack 内部コンポーネントの通信に使用します。 | /24 |
| プライベート | 記憶域ネットワークとプライベート VIP に使用します。 | /24 | 
| BMC | 物理ホスト上の BMC との通信に使用します。 | /27 | 
| | | |

## <a name="network-infrastructure"></a>ネットワーク インフラストラクチャ
Azure Stack のネットワーク インフラストラクチャは、スイッチ上に構成されたいくつかの論理ネットワークから成ります。 それらの論理ネットワークを次の図に示し、各論理ネットワークと TOR (top-of-rack)、ベースボード管理コントローラー (BMC)、境界 (カスタマー ネットワーク) スイッチを統合する方法を説明します。

![論理ネットワーク図とスイッチ接続](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC ネットワーク
このネットワークは、すべてのベースボード管理コントローラー (サービス プロセッサとも呼ばれます。iDRAC、iLO、iBMC など) の管理ネットワークへの接続専用です。 存在する場合は、ハードウェア ライフサイクル ホスト (HLH) がこのネットワーク上に配置され、ハードウェアのメンテナンスまたは監視のための OEM 固有のソフトウェアが提供される可能性があります。 

HLH では、デプロイメント仮想マシン (DVM) もホストされます。 DVM は Azure Stack のデプロイ中に使用され、デプロイが完了すると削除されます。 接続されたデプロイのシナリオでは、DVM には、複数のコンポーネントのテスト、検証、およびアクセスのためにインターネット アクセスが必要です。 これらのコンポーネントは、企業ネットワークの内外に配置できます (たとえば NTP、DNS、Azure)。 接続の要件について詳しくは、[「Azure Stack ファイアウォールの統合」の NAT に関するセクション](azure-stack-firewall.md#network-address-translation)をご覧ください。 

### <a name="private-network"></a>プライベート ネットワーク
この /24 (254 のホスト IP) ネットワークは Azure Stack リージョンに対してプライベートであり (Azure Stack リージョンの境界スイッチ デバイスを超えて拡張されません)、2 つのサブネットに分割されます。

- **ストレージ ネットワーク**。 スペース ダイレクトおよびサーバー メッセージ ブロック (SMB) ストレージ トラフィックの使用や仮想マシンのライブ マイグレーションをサポートするために使用される /25 (126 のホスト IP) ネットワーク。 
- **内部仮想 IP ネットワーク**。 ソフトウェア ロード バランサーのための内部のみの VIP 専用の /25 ネットワーク。

### <a name="azure-stack-infrastructure-network"></a>Azure Stack インフラストラクチャ ネットワーク
この /24 ネットワークは、内部の Azure Stack コンポーネントが互いにデータを通信したり交換したりできるように、これらのコンポーネント専用です。 このサブネットはルーティング可能な IP アドレスを必要としますが、アクセス制御リスト (ACL) を使用してソリューションに対してプライベートに保持されます。 これらのサービスの一部が外部リソースやインターネットにアクセスする必要がある場合に利用する、/27 ネットワークと同等のサイズである小さな範囲を除き、境界スイッチを超えてルーティングされることは想定されていません。 

### <a name="public-infrastructure-network"></a>パブリック インフラストラクチャ ネットワーク
この /27 ネットワークは、前に説明した Azure Stack インフラストラクチャ サブネットからの小さな範囲であり、パブリック IP アドレスは必要ありませんが、NAT または透過プロキシ経由のインターネット アクセスが必要です。 このネットワークは、緊急回復コンソール システム (ERCS) に割り当てられます。ERCS VM は Azure への登録中およびインフラストラクチャのバックアップ中にインターネット アクセスを必要とします。 ERCS VM はトラブルシューティングのために管理ネットワークにルーティング可能である必要があります。

### <a name="public-vip-network"></a>パブリック VIP ネットワーク
パブリック VIP ネットワークは、Azure Stack 内のネットワーク コントローラーに割り当てられます。 これはスイッチ上の論理ネットワークではありません。 SLB はアドレスのプールを使用して、テナント ワークロードに /32 ネットワークを割り当てます。 スイッチのルーティング テーブルでは、これらの /32 IP は BGP 経由で使用可能なルートとしてアドバタイズされます。 このネットワークには外部からアクセス可能な、つまりパブリック IP アドレスが含まれています。 Azure Stack インフラストラクチャは、このパブリック VIP ネットワークの最初の 31 個のアドレスを予約し、残りはテナント VM によって使用されます。 このサブネット上のネットワーク サイズは、最小 /26 (64 のホスト) から最大 /22 (1022 のホスト) までの範囲があり、/24 ネットワークを計画することをお勧めします。

### <a name="switch-infrastructure-network"></a>スイッチ インフラストラクチャ ネットワーク
この /26 ネットワークは、ルーティング可能なポイント ツー ポイント IP /30 (2 つのホスト IP) サブネット、およびインバンド スイッチ管理と BGP ルーター ID に専用の /32 サブネットであるループバックを含むサブネットです。 この範囲の IP アドレスは Azure Stack ソリューションの外部のデータセンターにルーティング可能である必要があり、プライベート IP またはパブリック IP のどちらでもかまいません。

### <a name="switch-management-network"></a>スイッチ管理ネットワーク
この /29 (6 つのホスト IP) ネットワークは、スイッチの管理ポートの接続専用です。 これにより、デプロイ、管理、およびトラブルシューティングのためのアウトオブバンド アクセスが可能になります。 これは、上で説明したスイッチ インフラストラクチャ ネットワークから計算されます。

## <a name="publish-azure-stack-services"></a>Azure Stack サービスの公開
Azure Stack の外部のユーザーに対して Azure Stack サービスを使用可能にする必要があります。 Azure Stack は、さまざまなエンドポイントをそのインフラストラクチャ ロールに応じて設定します。 それらのエンドポイントには、パブリック IP アドレス プールから VIP が割り当てられます。 デプロイ時に指定した外部 DNS ゾーン内のエンドポイントごとに DNS エントリが作成されます。 たとえば、ユーザー ポータルに portal.*&lt;region>.&lt;fqdn>* の DNS ホスト エントリが割り当てられます。

### <a name="ports-and-urls"></a>ポートと URL
Azure Stack サービス (ポータル、Azure Resource Manager、DNS など) を外部ネットワークに対して使用可能にするには、特定の URL、ポート、プロトコルに対して、これらのエンドポイントへの受信トラフィックを許可する必要があります。
 
透過プロキシから従来のプロキシ サーバーへのアップリンクが存在するデプロイ環境では、特定のポートと URL に[受信](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)および[送信](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)の両方の通信を許可する必要があります。 これには、ID、マーケットプレース、パッチと更新プログラム、登録、使用状況データに使用するポートと URL が該当します。

## <a name="next-steps"></a>次の手順
[境界接続](azure-stack-border-connectivity.md)