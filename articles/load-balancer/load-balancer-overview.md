---
title: Azure Load Balancer の概要
titleSuffix: Azure Load Balancer
description: Azure Load Balancer の機能の概要、アーキテクチャ、実装。 Load Balancer の動作とクラウドでの活用について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: 335549f4ccae01fa36921e0e4668fa15e8b33835
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423911"
---
# <a name="what-is-azure-load-balancer"></a>Azure Load Balancer の概要

Azure Load Balancer を使うと、アプリケーションを拡張し、サービスを高可用性にすることができます。 Load Balancer は受信と送信のどちらのシナリオもサポートし、低遅延と高スループットを実現できるだけでなく、あらゆる TCP アプリケーションと UDP アプリケーションの数百万ものフローにスケールアップできます。

Load Balancer は、ロード バランサーのフロントエンドで到着した新しい受信フローを、指定されたルールと正常性プローブに従って、バックエンド プールのインスタンスに分配します。

パブリック ロード バランサー は、プライベート IP アドレスをパブリック IP アドレスに変換することによって、仮想ネットワーク内の仮想マシン (VM) の送信接続を提供できます。

Azure Load Balancer は 2 つの価格レベル (*SKU*) で使用でき、これらはBasic と Standard です。 両者の間には、スケール、機能、および料金の違いがあります。 Basic Load Balancer で可能なシナリオをすべて、Standard Load Balancer でも作成できますが、アプローチは若干異なります。 Load Balancer について学習するときは、基礎および SKU 固有の違いを理解するようにしてください。

## <a name="why-use-load-balancer"></a>ロード バランサーを使用する理由

Azure Load Balancer を使って次のことができます。

* 受信インターネット トラフィックを VM に負荷分散します。 この構成は、[パブリック ロード バランサー](#publicloadbalancer)と呼ばれます。
* 仮想ネットワーク内の VM 間でトラフィックの負荷を分散します。 ハイブリッド シナリオでは、オンプレミスのネットワークから Load Balancer フロントエンドに到達することもできます。 どちらのシナリオでも、[内部ロード バランサー](#internalloadbalancer)と呼ばれる構成が使われます。
* 受信ネットワーク アドレス変換 (NAT) 規則を使って、特定の VM 上の特定のポートに、トラフィックをポート転送します。
* パブリック ロード バランサー を使って、仮想ネットワーク内の VM に[送信接続](load-balancer-outbound-connections.md)を提供します。

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。 トランスポート層セキュリティ (TLS) プロトコル終端 ("SSL オフロード") あるいは HTTP または HTTPS 要求によるアプリケーション レイヤーの処理が必要な場合は、「[Azure Application Gateway とは](../application-gateway/overview.md)」を参照してください。 グローバル DNS の負荷分散が必要な場合は、「[Traffic Manager とは](../traffic-manager/traffic-manager-overview.md)」を参照してください。 実際のエンド ツー エンドのシナリオでは、これらのソリューションを組み合わせると役に立つことがあります。
>
> Azure の負荷分散オプションの比較については、「[Azure の負荷分散オプションの概要](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)」を参照してください。

## <a name="what-are-load-balancer-resources"></a>Load Balancer リソースとは

Load Balancer リソースは、ユーザーが作成したいシナリオを実現するために Azure がそのマルチ テナント インフラストラクチャをプログラミングする方法を指定するオブジェクトです。 Load Balancer リソースと実際のインフラストラクチャの間に直接的な関係はありません。 Load Balancer を作成してもインスタンスは作成されず、容量は常に利用できます。

Load Balancer リソースは、パブリック ロード バランサーまたは内部ロード バランサーとすることができます。 Load Balancer リソースの機能は、フロントエンド、ルール、正常性プローブ、およびバックエンド プールによって定義されます。 VM からバックエンド プールを指定することにより、VM をバックエンド プールに配置します。

## <a name="fundamental-load-balancer-features"></a>ロード バランサーの基本機能

ロード バランサーは、TCP および UDP アプリケーションに対して次の基本機能を提供します。

* **負荷分散**

  Azure Load Balancer では、負荷分散規則を作成して、フロントエンドに到着したトラフィックをバックエンド プールのインスタンスに分散させることができます。 受信フローの分配にはハッシュ アルゴリズムが使われ、バックエンド プール インスタンスへのフローのヘッダーが書き換えられます。 バックエンド エンドポイントが正常であることを正常性プローブが示している場合、サーバーは新しいフローを受信できます。

  既定では、Load Balancer は 5 タプル ハッシュを使用します。 ハッシュには、ソース IP アドレス、ソース ポート、接続先 IP アドレス、接続先ポート、IP プロトコル番号が含まれ、使用可能なサーバーにフローをマップします。 特定の規則に 2 または 3 タプルのハッシュを使用することにより、ソース IP アドレスにアフィニティを作成できます。 同じパケット フローのすべてのパケットは、負荷分散されたフロントエンドの背後にある同じインスタンスに到着します。 クライアントが同じソース IP アドレスから新しいフローを開始すると、ソース ポートが変化します。 結果として得られる 5 タプルのハッシュにより、トラフィックは異なるバックエンド エンドポイントに送られることがあります。

  詳細については、「[Azure Load Balancer の分散モードを構成する](load-balancer-distribution-mode.md)」を参照してください。 次の図は、ハッシュベースの分散を示しています。

  ![ハッシュベースの分散](./media/load-balancer-overview/load-balancer-distribution.png)

  *図: ハッシュベースの分散*

* **ポート フォワーディング**

  Load Balancer では、受信 NAT 規則を作成できます。 この NAT 規則により、特定のフロントエンド IP アドレスの特定のポートから、仮想ネットワーク内の特定のバックエンド インスタンスの特定のポートにトラフィックをポート転送することができます。 この転送も、負荷分散と同じハッシュ ベースの分散によって実行されます。 この機能の一般的なシナリオは、Azure Virtual Network 内の個別の VM インスタンスへのリモート デスクトップ プロトコル (RDP) または Secure Shell (SSH) セッションです。
  
  複数の内部エンドポイントを、同じフロントエンド IP アドレスのポートにマップできます。 フロントエンド IP アドレスを使用すると、ジャンプ ボックスを追加しなくても VM をリモート管理できます。

* **アプリケーションの独立と透明性**

  Load Balancer は、TCP、UDP、またはアプリケーション レイヤーと直接やり取りしません。 TCP または UDP アプリケーションのシナリオがサポートされています。 Load Balancer がフローを終了または開始したり、フローのペイロードと対話したり、アプリケーション レイヤーのゲートウェイ機能を提供したりすることはありません。 プロトコル ハンドシェイクは、常にクライアントとバックエンド プール インスタンスの間で直接発生します。 受信フローへの応答は常に、仮想マシンからの応答です。 仮想マシンにフローが到着するときは、元のソース IP アドレスも保持されます。

  * すべてのエンドポイントは、VM によってのみ応答されます。 たとえば、TCP ハンドシェイクは常に、クライアントと選択されたバックエンド VM の間で行われます。 フロントエンドへの要求に対する応答は、バックエンドの VM によって生成される応答です。 フロントエンドへの接続を正常に検証するときは、少なくとも 1 つのバックエンド仮想マシンへのエンド ツー エンドの接続を検証していることになります。
  * アプリケーション ペイロードは Load Balancer に対して透過的です。 任意の UDP または TCP アプリケーションをサポートできます。 HTTP 要求ごとの処理またはアプリケーション レイヤー ペイロードの操作を必要とするワークロードの場合 (たとえば、HTTP URL の解析)、[Application Gateway](https://azure.microsoft.com/services/application-gateway) のようなレイヤー 7 のロード バランサーを使う必要があります。
  * Load Balancer は TCP ペイロードとやり取りせず、TLS オフロードを提供しているため、エンドツーエンドの暗号化されたシナリオを構築できます。 Load Balancer を使用すると、VM 自体で TLS 接続を終了することで、TLS アプリケーションの大規模なスケールアウトを実現できます。 たとえば、TLS セッションのキー容量は、バックエンド プールに追加する VM の数と種類によってのみ制限されます。 SSL オフロード、アプリケーション レイヤーの処理、または Azure への証明書管理の委任を必要とする場合は、Azure のレイヤー 7 ロード バランサーである [Application Gateway](https://azure.microsoft.com/services/application-gateway) を代わりに使用します。

* **自動再構成**

  Load Balancer は、インスタンスがスケール アップまたはスケール ダウンされると、すぐに自身を再構成します。 バックエンド プールの VM を追加または削除すると、Load Balancer リソースの追加操作なしに、ロード バランサーが再構成されます。

* **正常性プローブ**

  バックエンド プール内のインスタンスの正常性を判断するため、Load Balancer はユーザーが定義した正常性プローブを使います。 プローブで応答できない場合、Load Balancer は異常なインスタンスへの新しい接続の送信を停止します。 プローブの失敗は、既存の接続には影響しません。 アプリケーションがフローを終了するか、アイドル タイムアウトが発生するか、VM がシャットダウンするまで接続は続行されます。

  ロード バランサーは、TCP、HTTP と HTTPS エンドポイント用に別の正常性プローブの種類を提供します。 詳細については、「[プローブの種類](load-balancer-custom-probe-overview.md#types)」を参照してください。

  Classic クラウド サービスを使用している場合、追加の種類である[ゲスト エージェント](load-balancer-custom-probe-overview.md#guestagent)が許可されます。 ゲスト エージェントは、最後の手段の正常性プローブとして考慮する必要があります。 他のオプションが使用可能な場合、Microsoft はこの使用を推奨しません。

* **送信接続 (SNAT)**

  仮想ネットワーク内のプライベート IP アドレスから、インターネット上のパブリック IP アドレスへのすべての送信フローは、Load Balancer のフロントエンド IP アドレスに変換することができます。 パブリック フロントエンドが負荷分散規則によってバックエンドの VM に関連付けられていると、Azure は送信接続をパブリック フロントエンドの IP アドレスに変換します。 この構成には次の利点があります。

  * フロントエンドを別のサービス インスタンスに動的にマップできるため、サービスのアップグレードやディザスター リカバリーが簡単にできます。
  * 容易なアクセス制御リスト (ACL) の管理。 フロントエンド IP として表される ACL は、サービスをスケールアップ、スケールダウン、または再デプロイしても変更されません。 送信接続をマシンより少ない数の IP アドレスに変換すると、安全な受信者リストの実装負荷を軽減できます。

  詳しくは、「[Azure の送信接続](load-balancer-outbound-connections.md)」を参照してください。

Standard Load Balancer には、以下に示すような、これらの基礎には含まれない追加の SKU 固有機能があります。

## <a name="skus"></a> Load Balancer の SKU の比較

Load Balancer は、Basic SKU と Standard SKU の両方をサポートしています。 これらの SKU の間には、シナリオのスケール、機能、および料金の違いがあります。 Basic Load Balancer で可能なシナリオをすべて、Standard Load Balancer でも作成できます。 両方の SKU の API は似ており、SKU を指定することによって呼び出されます。 Load Balancer の SKU とパブリック IP をサポートするための API は、`2017-08-01` API から使用できるようになりました。 一般的な API と構造は、どちらの SKU でも同じです。

SKU によって、完全なシナリオ構成が若干異なる場合があります。 Load Balancer のドキュメントでは、記事が特定の SKU だけに適用される場合は、そのことが示されています。 違いを比較して理解するには、次の表をご覧ください。 詳しくは、「[Azure Standard Load Balancer の概要](load-balancer-standard-overview.md)」を参照してください。

>[!NOTE]
> 新しい設計では、Standard Load Balancer の採用をお勧めします。

スタンドアロン VM、可用性セット、および仮想マシン スケール セットは、どちらか一方の SKU にのみ接続でき、両方には接続できません。 パブリック IP アドレスで使うときは、Load Balancer とパブリック IP アドレスの SKU が一致していなければなりません。 Load Balancer とパブリック IP の SKU は変更できません。

SKU を明示的に指定することをお勧めします。 現時点では、必要な変更は最小限に抑えられています。 SKU が指定されていない場合の既定値は、Basic SKU の `2017-08-01` API バージョンです。

>[!IMPORTANT]
>Standard Load Balancer は新しい Load Balancer 製品です。 これは主に Basic Load Balancer のスーパーセットですが、2 つの製品には重要な違いがあります。 Basic Load Balancer で可能なエンド ツー エンドのシナリオはすべて、Standard Load Balancer でも作成できます。 既に Basic Load Balancer に使用している場合は、Standard Load Balancer と比較して、動作における最新の変更点を理解してください。

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

詳細については、「[Load Balancer の制限](https://aka.ms/lblimits)」を参照してください。 Standard Load Balancer について詳しくは、[概要](load-balancer-standard-overview.md)、[価格](https://aka.ms/lbpricing)、[SLA](https://aka.ms/lbsla) に関するページもご覧ください。

## <a name="concepts"></a>概念

### <a name = "publicloadbalancer"></a>パブリック ロード バランサー

パブリック ロード バランサーは、受信トラフィックパブリック IP アドレスおよびポートを、VM のプライベート IP アドレスおよびポートにマップします。 ロード バランサーは、VM からの応答トラフィックについてはその逆にマップします。 負荷分散規則を適用することで、特定の種類のトラフィックを複数の VM やサービスに分散できます。 たとえば、複数の Web サーバー間で Web 要求のトラフィックの負荷を分散できます。

>[!NOTE]
>可用性セットごとに 1 つのパブリック ロード バランサーと 1 つの内部ロード バランサーのみを実装できます。

次の図は、Web トラフィック用の負荷分散されたエンドポイントを示しています。このエンドポイントでは、3 台の VM でパブリックと TCP ポート 80 が共有されます。 この 3 台の VM は、1 つの負荷分散セット内にあります。

![パブリック ロード バランサーの例](./media/load-balancer-overview/IC727496.png)

*図:パブリック ロード バランサー を使った Web トラフィックの負荷分散*

インターネット クライアントは Web ページの要求を、TCP ポート 80 の Web アプリのパブリック IP アドレスに送信します。 Azure Load Balancer は、負荷分散セット内の 3 つの VM 全体に要求を分散させます。 ロード バランサーのアルゴリズムの詳細については、「[ロード バランサーの基本機能](load-balancer-overview.md##fundamental-load-balancer-features)」を参照してください。

Azure Load Balancer は、ネットワーク トラフィックを複数の VM インスタンスに均等に分散させます。 セッション アフィニティを構成することもできます。 詳細については、「[Azure Load Balancer の分散モードを構成する](load-balancer-distribution-mode.md)」を参照してください。

### <a name = "internalloadbalancer"></a> 内部ロード バランサー

パブリック ロード バランサーとは対照的に、内部ロード バランサーは、仮想ネットワーク内のリソースまたは VPN を使って Azure インフラストラクチャにアクセスするリソースにのみトラフィックを送信します。 Azure インフラストラクチャでは、仮想ネットワークの負荷分散フロントエンド IP アドレスへのアクセスが制限されます。 フロントエンド IP アドレスと仮想ネットワークは、インターネット エンドポイントに直接公開されることはありません。 社内の基幹業務アプリケーションは Azure で実行され、Azure 内またはオンプレミス リソースからアクセスされます。

内部ロード バランサーにより、次の種類の負荷分散が可能になります。

* **仮想ネットワーク内**: 仮想ネットワーク内の VM から、同じ仮想ネットワーク内に存在する一連の VM に負荷を分散する。
* **クロスプレミス仮想ネットワークの場合**: オンプレミスのコンピューターから、同じ仮想ネットワーク内に存在する一連の VM に負荷を分散する。
* **多層アプリケーションの場合**: バックエンド層がインターネットに接続しない、インターネットに接続する多層アプリケーションの負荷を分散する。 バックエンド層では、インターネットに接続する層からのトラフィックを負荷分散する必要があります。 次の図を参照してください。
* **基幹業務アプリケーション**: 負荷分散用のハードウェアやソフトウェアの追加を伴わずに、Azure でホストされている基幹業務アプリケーションの負荷を分散する。 このシナリオには、トラフィックが負荷分散されるコンピューターのセットに含まれるオンプレミスのサーバーが含まれます。

![内部ロード バランサーの例](./media/load-balancer-overview/IC744147.png)

*図:パブリック ロード バランサーと内部ロード バランサーの両方を使った、多層アプリケーションの負荷分散*

## <a name="pricing"></a>価格

Standard Load Balancer の使用量は課金されます。

* 構成された負荷分散および送信規則の数。 受信 NAT 規則は規則の合計数にカウントされません。
* 規則に関係なくインバウンドとアウトバウンドで処理されたデータの量。

Standard Load Balancer の価格の情報については、「[Load Balancer の価格](https://azure.microsoft.com/pricing/details/load-balancer/)」を参照してください。

Basic Load Balancer は無料で提供されます。

## <a name="sla"></a>SLA

Standard Load Balancer の SLA については、「[Load Balancer の SLA](https://aka.ms/lbsla)」を参照してください。

## <a name="limitations"></a>制限事項

* Load Balancer は特定の TCP または UDP プロトコルに対する負荷分散とポート フォワーディングを行います。 負荷分散規則と受信 NAT 規則は TCP および UDP をサポートしますが、ICMP を含む他の IP プロトコルはサポートしていません。

  Load Balancer は、UDP または TCP のフローのペイロードを終了したり、それに応答したり、それ以外の対話を行うことはありません。 プロキシではありません。 フロントエンドへの接続の正常な検証は、負荷分散規則または受信 NAT 規則で使用されているプロトコルと同じプロトコルを使用して帯域内で行う必要があります。 フロントエンドからの応答を確認するには、少なくとも 1 つの仮想マシンがクライアントに対して応答を生成する必要があります。

  Load Balancer フロントエンドからの帯域内応答を受け取らない場合は、仮想マシンが応答できないことを示します。 応答できる仮想マシンがない状態で、Load Balancer フロントエンドと対話することはできません。 この原理は、ポート マスカレード SNAT が TCP および UDP に対してのみサポートされている送信接続にも当てはまります。 ICMP などの他の IP プロトコルも失敗します。 この問題を軽減するには、インスタンスレベルのパブリック IP アドレスを割り当てます。 詳細については、「[SNAT と PAT の理解](load-balancer-outbound-connections.md#snat)」を参照してください。

* 内部ロード バランサーは、送信接続を内部ロード バランサーのフロントエンドに変換しません。これは、両方ともプライベート IP アドレス空間にあるためです。 パブリック ロード バランサーは、仮想ネットワーク内のプライベート IP アドレスからパブリック IP アドレスへの[送信接続](load-balancer-outbound-connections.md)を提供します。 内部ロード バランサーの場合、この方法を使用すると、変換が必要ない固有内部 IP アドレス空間内で SNAT ポートの枯渇が発生する可能性が回避されます。

  副作用として、バックエンド プール内の VM からの送信フローが、そのプール内の内部ロード バランサーのフロントエンドへのフローを試み、"_かつ_"、それ自体にマップバックされている場合、フローの 2 つのレッグは一致しません。 これらは一致しないため、フローは失敗します。 フローが、フロントエンドへのフローを作成したバックエンド プール内の同じ VM にマップバックしなかった場合、フローは成功します。

  フローがそれ自体にマップバックする場合、送信フローは VM からフロントエンドに発信されるように見え、対応する受信フローは VM からそれ自体に発信されるように見えます。 ゲスト オペレーティング システムの観点からは、同じフローの受信部分と送信部分は、仮想マシン内と一致しません。 TCP スタックは、同じフローのこれらの半分を、同じフローの一部と認識しません。 送信元と送信先が一致しないためです。 フローがバックエンド プール内の他の VM にマップする場合、フローの半分は一致し、VM はフローに応答できます。

  このシナリオの現象は、フローがそのフロー自体の発生元と同じバックエンドに返されるときの、断続的な接続のタイムアウトです。 一般的な回避策としては、内部ロード バランサーの背後にプロキシ レイヤーを挿入し、Direct Server Return (DSR) スタイル ルールを使用することなどが挙げられます。 詳細については、「[Azure Load Balancer の複数のフロントエンド](load-balancer-multivip-overview.md)」を参照してください。

  ユーザーは、内部ロード バランサーを任意のサード パーティ製プロキシと組み合わせるか、HTTP/HTTPS を使用したプロキシ シナリオに内部の [Application Gateway](../application-gateway/application-gateway-introduction.md) を使用することができます。 この問題はパブリック ロード バランサーを使って軽減できますが、結果として得られるシナリオでは [SNAT の枯渇](load-balancer-outbound-connections.md#snat)が発生しやすくなります。 慎重に管理しない限り、この 2 番目のアプローチは避けてください。

* 一般に、転送 IP フラグメントは、負荷分散規則ではサポートされていません。 UDP パケットと TCP パケットの IP の断片化は負荷分散規則ではサポートされていません。 高可用性ポートの負荷分散規則を使用すると、既存の IP フラグメントを転送できます。 詳細については、「[高可用性ポートの概要](load-balancer-ha-ports-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Load Balancer の使用を始めるには、「[Basic Load Balancer を作成する](quickstart-create-basic-load-balancer-portal.md)」を参照して、Load Balancer を 1 つ作成し、カスタム IIS 拡張機能がインストールされている VM を作成して、Web アプリを VM 間で負荷分散します。
