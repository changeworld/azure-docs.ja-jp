---
title: Azure での Fusion Core ソリューション
description: Fusion Core の概要 - Fusion Core とは、3GPP 標準で定義された 5G Next Generation Core (5G NGC または 5GC) のクラウドネイティブ実装であり、5G ネットワーク オペレーターは複数のワイヤレスおよび固定アクセス テクノロジを使用して、すべてのエンド デバイスからのデータ トラフィックを集約できます。
ms.service: private-multi-access-edge-compute-mec
author: djrmetaswitch
ms.author: drichards
ms.topic: overview
ms.date: 06/16/2021
ms.openlocfilehash: dd813b08301da960fb13e6047e51046ac2d95aed
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112464559"
---
# <a name="what-is-fusion-core"></a>Fusion Core とは

Fusion Core とは、3GPP 標準で定義された 5G Next Generation Core (5G NGC または 5GC) のクラウドネイティブ実装であり、5G ネットワーク オペレーターは複数のワイヤレスおよび固定アクセス テクノロジを使用して、すべてのエンド デバイスからのデータ トラフィックを集約できます。 高パフォーマンスでプログラミング可能な5G ユーザー プレーン機能 (UPF)、コア コントロール プレーン機能、サービス ベースのアーキテクチャ要素のポートフォリオ、ネットワーク監視用の管理コンポーネントで構成されています。

Fusion Core は、純粋な5G シナリオに展開することも、4G ネットワークと相互運用することもできます。

Fusion Core には、次の 5G ネットワーク機能が実装されています。

|ネットワーク機能  |説明  |
|---------|---------|
|**AMF**<br><br>アクセスおよびモビリティ管理機能     |これにより次のものがサポートされます。<br><ul> <li>gNB からの NAS シグナリングの終端</li><li>NAS の暗号化と整合性の保護</li><li>登録管理</li><li>接続の管理</li><li>モビリティ管理</li><li>アクセスの認証と認可</li><li>セキュリティ コンテキストの管理</li></ul>         |
|**SMF**<br><br>セッション管理機能     |SMF では、セッションの設定、変更、解放がサポートされています。<br><br>その一環として、UE IP アドレスの割り当てと管理 (DHCP など)、およびセッション管理に関連する NAS シグナリングの終端が提供されます。<br><br>また、ダウンリンク データ トラフィックでのデバイスのページング、およびセッション管理の一部としてのトラフィック ステアリング構成の提供もサポートされています。         |
|**UPF**<br><br>ユーザー プレーン機能     |UPF では、パケット データ トラフィックの処理が行われます。 これには、パケット データ トラフィックのルーティング、転送、検査、QoS の処理が含まれます。<br><br>これは、データ ネットワーク (DN) へのトラフィックおよび異なる無線ネットワーク間のハンドオーバーの両方に対するアンカー ポイントとして機能します。         |
|**PCF**<br><br>ポリシー制御機能     |PCF は、トラフィックに対する集約型ポリシー フレームワークを提供し、コントロール プレーン機能のためのポリシー ルールを提供し、サブスクリプション情報のソースとして機能します。         |
|**AUSF**<br><br>認証サーバー機能     |AUSF は、5G サブスクライバー用の認証サーバーです。         |
|**UDM**<br><br>統合データ管理     |UDM により、AKA 資格情報、ユーザー ID、アクセス認可、サブスクライバー管理の生成がサポートされます。         |
|**UDR**<br><br>統合データ リポジトリ     |UDR は、すべてのサブスクライバー情報の集約型リポジトリです。         |
|**NRF**<br><br>ネットワーク リポジトリ機能     |NRF では、NF のサービス検出が提供されます。         |

また、4G ネットワークと連携するときの次のネットワーク機能も実装されています。

|ネットワーク機能  |説明  |
|---------|---------|
|**MME**<br><br>モビリティ管理エンティティ     |MME は、LTE アクセス ネットワーク用のキー制御ノードであり、ネットワークへの UE アクセスのすべての側面を制御します。         |
|**S11-IWF**<br><br>S11 相互作用関数 |S11-IWF により、4G モビリティ管理エンティティ (MME) への S11 インターフェイスが公開され、機能をサポートするためのプロトコル変換が提供されます。         |
|**UDR**<br><br>統合データ リポジトリ     |UDR は、すべてのサブスクライバー情報の集約型リポジトリです。 Fusion Core の UDR により、4G シナリオにおいて通常はホーム サブスクライバー ストア (HSS) によって実行される役割が実行されます。 S6a インターフェイスを使用して MME との対話が行われます。         |

次の図は、これらの各ネットワーク機能と、サードパーティのコンポーネントとの相互運用に使用されるインターフェイスを示したものです。

:::image type="content" source="./media/metaswitch-overview/fusion-core-architecture.png" alt-text="Fusion Core のアーキテクチャ":::

Fusion Core は、Fusion Core Base VM と呼ばれる仮想マシン (VM) で提供されます。 Fusion Core Base VM は、Azure Stack Edge (ASE) 上の Azure で管理されるアプリケーションとして展開されるように設計されています。 Fusion Core の機能を提供するために必要なネットワーク機能とインフラストラクチャ コンポーネントは、Fusion Core Base VM にコンテナーとして展開され、Kubernetes によって調整されます。

:::image type="content" source="./media/metaswitch-overview/fusion-core-base-vm-azure-stack-edge-with-networking.png" alt-text="Azure Stack Edge 上の Fusion Core Base VM":::

## <a name="why-use-fusion-core"></a>Fusion Core を使用する理由

### <a name="deployment-in-private-networks"></a>プライベート ネットワークでの展開

Fusion Core では、Azure のプライベート マルチアクセス エッジ コンピューティングの機能を使用し、Azure による一貫した一元的管理により、Industry 4.0 のユース ケースをサポートするために必要なエッジ コンピューティング リソースのパフォーマンスと低待機時間を組み合わせた、企業向けの 5G ソリューションが提供されます。 プライベート マルチアクセス エッジ コンピューティングの詳細については、「[Azure プライベート マルチアクセス エッジ コンピューティングとは](overview.md)」を参照してください。

Fusion Core をエンタープライズ エッジに展開することにより、サービス提供対象のデバイスとの間が最短になり、同じ場所にあるアプリケーション ロジックと組み合わせたときに、ローカル データ処理によって低遅延レベルとバックホールの削減が実現されます。 これにより、企業には次のようないくつかの重要なメリットが提供されます。

- **自動化** - 自動化されたシステム (ロボットなど) からのコマンド アンド コントロール メッセージをリアルタイムで処理して、速度の低下を防ぎ、生産性を高めることができます。
- **テレメトリ** - 正常性評価のためのテレメトリ データと自動化されたシステムの操作をリアルタイムで処理して、事故を防ぎ、オンサイトの安全性を確保することができます。
- **分析** - 大量の操作データと診断データを、最小限のコストで転送でき、重要なアクションが遅れないようにすることができます。

:::image type="content" source="./media/metaswitch-overview/enterprise-edge-latency.png" alt-text="プライベート 5G エッジでの Fusion Core":::

Fusion Core では、この低遅延と、プライベート 5G ネットワークによって提供されるセキュリティおよび高帯域幅を併せて利用できるので、次のような Industry 4.0 のユース ケースをサポートするのに最適です。

- **生産** - 生産ライン分析およびロボットによる倉庫の自動化。
- **公共の安全性** - 緊急作業員や災害復旧作業者のためのモビリティと接続性。
- **エネルギーおよび公益事業** - スマート メーターとネットワーク スライスおよびコントロールのためのバックホール ネットワーク。
- **防衛** - 接続された指揮所と、リアルタイム分析による戦場。
- **スマート ファーム** - 農作業のための接続された機器。

### <a name="pure-5g-and-4g-interworking-support"></a>ピュア 5G と 4G の連携のサポート

Fusion Core は、5G のみのシナリオで展開できます。 この場合、Fusion Core は **5G スタンドアロン モード** で実行されます。

また、Fusion Core では 4G との連携もサポートされており、クラウドネイティブ コアを介して 4G UE にサービスを提供します。 これは、**4G モード** と呼ばれます。 新規および既存のネットワーク オペレーターの場合、4G モードを使用すると、4G コアの維持への投資を続けることなく、5G に直接進むことができます。

### <a name="service-assurance-and-kpi-metrics"></a>サービス保証と KPI メトリック

Fusion Core は **Metaswitch の Service Assurance Server (SAS)** と統合されており、NGAP/NAS メッセージや HTTP の要求と応答など、すべてのメッセージ トラフィックのプロアクティブでリアルタイムの分析が提供されます。

Service Assurance Server に用意された Web GUI を使用して、Fusion Core を含むシグナリング フローの詳細なトレースを収集できます。 これらを使用して、ユーザー サービスに影響を与える構成、ネットワーク、相互運用性に関する多くの一般的な問題を診断できます。

:::image type="content" source="./media/metaswitch-overview/service-assurance-server-detailed-timeline.png" alt-text="Service Assurance Server の Web GUI での詳細なタイムライン ビュー":::

また、Fusion Core は **Metaswitch ServiceIQ Monitoring** とも統合されています。それによって提供される Prometheus や Grafana などの業界標準のクラウドネイティブ監視ツールを使用して、システム パフォーマンスの分析、障害の特定、トラブルシューティングをリアルタイムで行うことができます。

ServiceIQ Monitoring は Fusion Core VM 上で実行され、Fusion Core の展開に関係する主要なメトリックを柔軟に監視できる、複数の Fusion Core ダッシュボードにアクセスできます。 また、アラートの発生に関する情報を確認し、新たな問題に迅速に対応できます。


[![Fusion Core の概要ダッシュボード](media/metaswitch-overview/fusion-core-overview-dashboard.png)](media/metaswitch-overview/fusion-core-overview-dashboard.png#lightbox)

### <a name="other-features"></a>その他の機能

|機能  |説明  |
|---------|---------|
|**ネットワーク スライス**     |Fusion Core の展開は、独立した論理ネットワークを多重化するために複数のネットワーク スライスを提供するように構成できます。 1 つの Fusion Core 展開内のすべてのネットワーク機能によって、構成されているすべてのスライスが提供されます。<br><br>許可されるスライスと既定のスライスを使用してサブスクライバーをプロビジョニングし、スライス固有のポリシー (QoS) を定義することにより、サブスクライバーのグループの管理の分離を維持するための簡単でユビキタスなメカニズムを提供できます。         |
|**サポートされている 5G 手順**     |Fusion Core は、より広いプライベート5G ソリューションの一部として動作する場合、次の手順に関する 3GPP TS 23.502 に準拠します。<ul><li>UE の登録と登録解除</li><li>モビリティ登録更新と定期的な登録更新</li><li>UE によって開始されるサービス要求 (シグナリング、データ)</li><li>AN およびネットワークによって開始される UE コンテキスト解放</li><li>PDU セッションの確立</li><li>PDU セッションの解放</li><li>NG-RAN ノード間の N2 ベースの引き渡し</li><li>Xn ベースの NG-RAN 間の引き渡し</li><li>ネットワークによって開始されるダウンリンク データ通知とページング</li>        |
|**UE 認証**     |<ul><li>サービス ネットワークで認証機能を提供するための Security Anchor Function (SEAF) のサポート。</li><li>サブスクリプション永続識別子 (SUPI)、サブスクリプション隠ぺい識別子 (SUCI)、グローバル一意一時 ID (5G-GUTI) を使用した認証。</li><li>UE への 5G-GUTI の割り当てまたは再割り当て。</li><li>UE とネットワークの間の相互認証のための第 3 世代の認証と鍵承認 (EAP-AKA) および 5G の世代認証と鍵承認 (5G-AKA)。</li><li>4G モードで実行される展開のための Evolved Packet System ベースの認証と鍵承認 (EPS-AKA)。</li>         |
|**UE セキュリティ コンテキスト管理**     |Fusion Core の AMF により、5G NAS の暗号化と整合性の保護が実行されます。 UE 登録のとき、UE には 128 ビット キーを使用した 5G NAS のためのセキュリティ機能が含まれます。<br><br>暗号化と整合性に関して Fusion Core によってサポートされるアルゴリズムは、次のとおりです。<ul><li>5GS null 暗号化アルゴリズム</li><li>128 ビット Snow3G</li><li>128 ビット AES</li><li>128 ビット ZUC</li>        |
|**UE MTU の構成**     |Fusion Core の SMF により、断片化を回避するために、PDU セッション確立手順の一部として、要求で UE へのデータ ネットワークに MTU をが通知されます。         |
|**ポリシー コントロール**     |Fusion Core により、PDU セッションおよびフローごとに、エンド ツー エンドのポリシー制御の決定と強制が適用されます。 これにより、特定のフローまたは UE に異なる QoS レベルを適用するために必要な柔軟性が提供されて、UE のグループにサービスまたはデータ ネットワークを選択的に公開したり、ネットワークを流れるトラフィックの種類を制御したりすることができます。         |
|**UPF アクセス制御リスト (ACL)**     |ACL を使用すると、特定の IPv4 アドレス範囲を送信元または送信先とするトラフィックを許可またはブロックすることができます。 Fusion Core のアクセス (N3) およびコア (N6) インターフェイスで ACL を構成できます。         |
|**RAT/頻度選択優先順位 (RFSP) へのインデックス**     |Fusion Core の AMF では、RFSP インデックスを含む RAN を提供できます。RAN はローカル構成と一致して、セルの再選択や周波数レイヤー リダイレクトなど、特定の無線リソース管理ポリシーを適用することができます。         |

## <a name="next-steps"></a>次のステップ

- [Fusion Core を展開する](deploy-metaswitch-fusion-core-solution.md)方法を学習します

