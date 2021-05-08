---
title: Communication Services のための Azure インターネット ピアリングのチュートリアル
titleSuffix: Azure
description: Communication Services のための Azure インターネット ピアリングのチュートリアル
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498873"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Communication Services のための Azure インターネット ピアリングのチュートリアル

このセクションでは、Microsoft との直接相互接続を確立するために Communication Services プロバイダーが従う必要がある手順について説明します。

**Communication Services プロバイダー:**  Communication Services プロバイダーは、コミュニケーション サービス (コミュニケーション、メッセージング、会議など) を提供し、コミュニケーション サービス インフラストラクチャ (SBC または SIP ゲートウェイなど) を Azure Communication Services および Microsoft Teams と統合することを検討している組織です。 

Azure インターネット ピアリングでは、Communication Services プロバイダーが任意のエッジ サイト (POP の場所) で Microsoft との直接相互接続を確立できるようにサポートします。 すべてのパブリック エッジ サイトの一覧については、[PeeringDB](https://www.peeringdb.com/net/694) を参照してください。

Azure インターネット ピアリングでは、Communication Services の信頼性が高く QoS (サービスの品質) が有効な相互接続を提供して、高品質でパフォーマンスに優れたサービスを保証します。

## <a name="technical-requirements"></a>技術的な要件
Communication Services の直接相互接続を確立するための技術的な要件は次のとおりです。
-   Peer では独自の自律システム番号 (ASN) を提供する必要があります。これはパブリックである必要があります。
-   ローカル冗長性を確保するために、Peer には各相互接続場所での冗長相互接続 (PNI) が必要です。
-   リージョンまたは都市圏でサイト障害が発生した場合にフェールオーバーを確実に行うために、Peer では geo 冗長性を確保する必要があります。
-   Peer では、高可用性とより高速な収束を保証するために、アクティブ/アクティブとしての BGP セッションが必要であり、プライマリおよびバックアップとしてプロビジョニングすることはできません。
-   Peer では、Peer のピアリング ルーターとピアリング回線の比率を 1:1 に維持する必要があり、レート制限は適用されません。
-   Peer では、Peer の通信サービス エンドポイント (SBC など) によって使用される、パブリックにルーティング可能な独自の IPv4 アドレス空間を提供および公開する必要があります。 
-   Peer では、公開された各サブネットに格納されているトラフィックおよびエンドポイントのクラスの詳細を提供する必要があります。 
-   1 秒未満のルート収束を容易にするために、Peer では双方向の転送検出 (BFD) を介して BGP を実行する必要があります。
-   すべての通信インフラストラクチャ プレフィックスは Azure portal に登録され、コミュニティ文字列 8075:8007 を使用して公開されます。
-   Peer では、ステートフルなファイアウォールを実行しているデバイスでピアリングを終端化することはできません。 
-   Microsoft により、既定ですべての相互接続リンクが LAG (リンク バンドル) として構成されます。そのため、Peer では相互接続リンクで LACP (Link Aggregation Control Protocol) をサポートする必要があります。

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Communication Services のための Microsoft との直接相互接続の確立。

Azure インターネット ピアリングを使用して直接相互接続を確立する場合は、以下の手順に従ってください。

**1.    Azure サブスクリプションに Peer パブリック ASN を関連付ける:**

Peer によりパブリック ASN が Azure サブスクリプションに既に関連付けられている場合は、この手順を無視してください。

[ポータルを使用してピア ASN を Azure サブスクリプションに関連付ける - Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

次の手順では、Peering Service のための Direct ピアリング接続を作成します。

> [!NOTE]
> ASN の関連付けが承認されたら、ご利用の ASN とサブスクリプション ID で peeringservices@microsoft.com 宛てに電子メールを送信し、サブスクリプションを Communication Services に関連付けます。 

**2.    Peering Service のための Direct ピアリング接続を作成する:**

指示に従い、[ポータルを使用して Direct ピアリングを作成または変更します](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal)

高可用性の要件を満たしていることを確認してください。

[ピアリングの作成] ページで次のオプションを選択していることを確かめてください。

ピアリングの種類:   **Direct**

Microsoft Network:  **8075**

SKU:        **Premium Free**


[Direct Peering Connection]\(Direct ピアリング接続\) ページで、次のオプションを選択します。

Session Address provider (セッション アドレス プロバイダー):   **Microsoft**

Use for Peering Services (Peering Service に使用):   **有効**

> [!NOTE] 
> Peering Service をアクティブ化するために選択している間は、次のようなメッセージは無視してください。
> *MAPS プロバイダーになることについて、peering@microsoft.com まで連絡していない限り、有効にしないでください。*


  **2a. Peering Service に既存の Direct ピアリング接続を使用する**

Peering Service をサポートするために使用する既存の Direct ピアリングがある場合は、Azure portal でアクティブにすることができます。
1.  指示に従い、[ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換します](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal)。
必要に応じて、高可用性の要件を満たす追加の回線を注文します。

2.  手順に従い、ポータルを使用して Direct ピアリングで [Peering Service を有効にします](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal)。




**3.    最適化されたルーティングにプレフィックスを登録する**

Communication Services のインフラストラクチャ プレフィックスに対して最適化されたルーティングを行うには、すべてのプレフィックスをピアリング相互接続に登録する必要があります。
[Azure Peering Service を登録する - Azure portal | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

プレフィックス キーは、Communications Service パートナーに対して自動的に設定されるので、そのパートナーが登録にプレフィックス キーを使用する必要はありません。 

登録されているプレフィックスが、リージョンに対して確立された直接相互接続でアナウンスされていることを確かめてください。


## <a name="faqs"></a>よく寄せられる質問: 

**Q.**  Communication Services のサブネットが小さく (</24) なっています。 小さなサブネットもルーティングできますか?

**A.**  はい。Microsoft Azure Peering サービスでは、より小さなプレフィックス ルーティングもサポートされています。 ルーティング用により小さなプレフィックスを登録していること、および同じものが相互接続でアナウンスされていることを確かめてください。

**Q.**  Microsoft ではこれらの相互接続を介して、どのようなルートを受信しますか?

**A.** Microsoft ではこれらの相互接続を介して、Microsoft のすべてのパブリック サービス プレフィックスをアナウンスしています。 これにより、Communication だけでなく、他のクラウド サービスに同じ相互接続で確実にアクセスできるようになります。

**Q.**  プレフィックスの制限を設定する必要があるのですが、Microsoft でアナウンスするルートの数はいくつですか?

**A.** Microsoft では、インターネット上で約 280 のプレフィックスをアナウンスしており、今後、10 から 15% 増える可能性があります。 そのため、400 から 500 の安全限度を "最大プレフィックス数" として設定することをお勧めします

**Q.** Microsoft では Peer プレフィックスをインターネットに再公開しますか?

**A.** いいえ。

**Q.** このサービスに対して料金は発生しますか?

**A.** いいえ。しかし、Peer ではサイト間の接続コストが発生することが予想されます。

**Q.** 相互接続の最低リンク速度はどれくらいですか?

**A.** 10 Gbps。

**Q.** Peer は SLA にバインドされていますか?

**A.** はい。使用率が 40% に達したら、45 から 60 日の LAG 補強プロセスを開始する必要があります。

**Q.** 現在の Direct ピアリングや Express Route よりもこのサービスが優れている点は何ですか?

**A.** 決済なしで、パス全体が Microsoft WAN 経由の音声トラフィック用に最適化されています。また、収束は BFD を使用して 1 秒未満になるように調整されます。

**Q.** オンボード プロセスを完了するのにどれくらいかかりますか?

**A.** 時間は、サイトの数と場所に応じて、また Peer によって既存のプライベート ピアリングが移行されるか、新しいケーブル接続が確立されるかによって変わります。 通信事業者は、3 週間以上計画を立てる必要があります。

**Q.** オンボードに API を使用できますか?

**A.** 現在、API はサポートされていません。構成は Web ポータル経由で行う必要があります。 
