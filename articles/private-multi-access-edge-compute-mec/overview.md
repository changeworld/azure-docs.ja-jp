---
title: Azure プライベート マルチアクセス エッジ コンピューティング
description: Microsoft コンピューティング、ネットワーク、アプリケーション サービスを 1 つのポートフォリオにまとめてクラウドから管理できるようにする Azure プライベート マルチアクセス エッジ コンピューティング (MEC) ソリューションについて説明します。
author: niravi-msft
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: kumud
ms.openlocfilehash: 6d85a42b3ff23f4bead8abd3187b9a899ebb12c3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277944"
---
# <a name="what-is-azure-private-multi-access-edge-compute"></a>Azure プライベート マルチアクセス エッジ コンピューティングとは

Azure プライベート マルチアクセス エッジ コンピューティング (MEC) は、Microsoft コンピューティング、ネットワーク、アプリケーション サービスを 1 つのポートフォリオにまとめてクラウドから管理できるようにするソリューションです。 このソリューションを使用すると、通信事業者やシステム インテグレーターは、エンタープライズ エッジで、エンタープライズ デジタル変革の次の波に対応するハイ パフォーマンスかつ低遅延の接続と IoT アプリケーションを提供することができます。 

Azure プライベート MEC は、Private Edge Zone を進化させたもので、可能性の範囲を 1 つのプラットフォームとサービスから、複数のプラットフォームと機能を利用するソリューションに拡張します。 これらの機能としては、エッジ サービスおよびアプリケーション、エッジ ネットワーク機能、エッジ コンピューティング オプション、エッジ無線およびデバイスがあります。 これらの機能は、エンド デバイスにより近いデータを処理することで、ビデオ分析、リアルタイム ロボット、IoT を組み合わせたユース ケースなど、待機時間とスループットの影響を受けやすいユーザー シナリオをグローバルな規模で向上させるのに役立ちます。 お客様とパートナーには、Azure サービスとエコシステム テクノロジ コンポーネントの完全なセットを利用して、ソリューションの構築、デプロイ、管理を簡単かつ迅速に行うことができるというベネフィットがあります。 

## <a name="benefits-to-customers-and-partners"></a>お客様やパートナーにとってのベネフィット
- 企業のお客様:
    - 増え続ける Azure サービス、ISV、開発者エコシステムのポートフォリオへのアクセス。
    - 信頼性があり、業界に合わせたソリューションの選択。
    - 1U のフットプリントから始められる完全に統合されたテクノロジ スタック。

- Telco およびシステム インテグレーター パートナー:
    - 顧客向けの 5G および Edge の調達、デプロイ、管理、収益化が容易。
    - 製品、クラウド サービス、アプリケーションのキュレーション エコシステムの選択。
    - Azure 開発者エコシステムへのアクセス。

- アプリケーション ISV:
    - 超低遅延アプリケーションを開発するためのフル機能のプラットフォーム。 
    - 確立されたツールとリソースを使用した一貫性のある開発者エクスペリエンス。
    - Microsoft Marketplace を介した大規模配布

## <a name="microsoft-capabilities"></a>Microsoft の機能
Azure プライベート MEC には、Microsoft のいくつかの機能が含まれています。 このような機能としては、ネットワーク機能製品、管理サービス、ハードウェア インフラストラクチャとサービスの組み合わせなどがあります。 

### <a name="azure-network-functions-offered-via-marketplace"></a>Marketplace を介して提供される Azure ネットワーク機能

**Metaswitch Fusion Core**: Fusion Core は、完全にコンテナー化された 5G Core ソリューションであり、4G または 5G 無線を介してデータ ネットワークに接続された IoT デバイス間の接続に必要なすべてのネットワーク機能をサポートします。 このソリューションによって、次の主なベネフィットの一部が提供されます。
 - Azure Stack Edge 上の Azure Marketplace ポータルから簡単にデプロイしてプロビジョニングできる。
 - ハイ パフォーマンス (4 物理コアの非常に小さいコンピューティング フットプリントで 25 Gbps のスループット)。
 - 4G と 5G の両方の種類のスタンドアロン アクセスをサポート。
 - エンタープライズ中心のサービス アシュアランスと KPI ダッシュボード用の組み込みツール。 
 
Fusion Core を使用することにより、ISV は、ライブ ビデオ分析などのアプリケーションを、IoT Edge アプリケーション用の同じ Azure Stack Edge ノード上にデプロイできます。 

**Affirmed Private Network Service**: Affirmed Private Network Service は、企業に 4G および 5G のマネージド サービス オファリングを提供したいモバイル ネットワーク事業者およびマネージド サービス プロバイダーにマネージド プライベート ネットワーク サービスを提供する Azure Marketplace です。 APNS を使用すると、通信事業者は、低遅延、高帯域幅、エンドツーエンドのセキュリティを必要とするビジネス クリティカルなアプリケーションの実行と運用を可能にするキャリアグレードのプライベート モバイル ネットワークを企業に提供することができます。 これは、プライベートとパブリックのオペレーター ネットワーク間の完全なモビリティを提供する統合されたモバイル ネットワーク オペレーターです。 自動化と簡素化された運用機能により、APNS は、何千もの企業のエッジの場所にスケーラビリティを提供します。また、Azure を使用して、プライベート ネットワークとエンタープライズ アプリケーション全体に強化されたセキュリティを提供します。 モバイル コア全体をネットワークのエッジに、すべてクラウド内、またはクラウド上のコントロール プレーンおよびエンタープライズ エッジ上のユーザー プレーンを使用するハイブリッド モードで展開できる柔軟性を提供します。 

### <a name="azure-management-services"></a>Azure 管理サービス

**Azure ネットワーク機能マネージャー (NFM)** : Azure NFM を使用すると、一貫性のある Azure ツールとインターフェイスを使用して、ネットワーク機能をエッジにデプロイできます。 このサービスを使用して、パケット コアと SD-WAN ネットワーク機能を Azure Stack Edge にデプロイします。 詳細については、[Azure ネットワーク機能マネージャー](../network-function-manager/overview.md)に関するページを参照してください。

**Arc 対応 Kubernetes**: Azure Arc 対応 Kubernetes を使用すると、Azure の内部または外部にある Kubernetes クラスターを接続して構成することができます。 ポリシーベースのデプロイで大規模に監視および管理し、一貫性のあるセキュリティ構成を大規模に適用できます。 Azure Arc 対応 Kubernetes は、すべての Cloud Native Computing Foundation (CNCF) 認定 Kubernetes クラスターで動作します。 詳細については、「[Azure Arc](https://azure.microsoft.com/services/azure-arc/)」を参照してください。

### <a name="azure-stack-hardware-and-services"></a>Azure Stack ハードウェアおよびサービス
**Azure Stack Edge**: Azure Stack Edge では、データが作成されるエッジで直接コンピューティング、ストレージ、インテリジェンスを利用できるデバイスのポートフォリオを提供します。 デバイスは、1 から 2 個の NVIDIA T4 GPU が付属する 1U のラックマウント可能なアプライアンスです。 Azure IoT Edge を使用することにより、コンテナーを IoT Hub からデプロイおよび管理し、Azure Stack Edge 上の Azure IoT ソリューションと統合できます。 Azure Stack Edge Pro SKU は、エッジでネットワーク機能を実行することが認定されています。 詳細については、「[Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)」を参照してください。

**Azure Stack HCI**: Azure Stack HCI は、Azure サービスとして提供される新しいハイパーコンバージド インフラストラクチャ (HCI) オペレーティング システムで、最新のセキュリティ、パフォーマンス、機能の更新プログラムが提供されます。 既存のツールやプロセスを使用して、Windows および Linux の仮想マシン (VM) をデータセンターまたはエッジにデプロイして実行できます。 Azure Backup、Azure Monitor、Microsoft Defender for Cloud を使用して、データセンターをクラウドに拡張できます。 詳細については、「[Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/)」を参照してください。

### <a name="application-services"></a>アプリケーション サービス

**Azure IoT Edge ランタイム**: Azure IoT Edge ランタイムを使用すると、クラウド ネイティブのワークロードと同じツールとセキュリティ態勢を使用して、クラウド ワークロードをエッジ コンピューティング アプライアンス全体で管理およびデプロイできます。 詳細については、[Azure IoT Edge ランタイム](/windows/ai/windows-ml-container/iot-edge-runtime)に関するページを参照してください。

**Azure IoT Hub**: Azure IoT Edge ランタイム: Azure IoT Edge ランタイムを使用すると、クラウド ネイティブのワークロードと同じツールとセキュリティ態勢を使用して、クラウド ワークロードをエッジ コンピューティング アプライアンス全体で管理およびデプロイできます。 詳細については、「[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)」を参照してください。

**Azure IoT Central**: Azure IoT Central は、予測可能な価格モデルとグローバル スケールが組み込まれたサービスとしてのデバイス管理とデータ インジェストを可能にするマネージド アプリケーション プラットフォームです。 詳細については、「[Azure IoT Central](https://azure.microsoft.com/services/iot-central/)」を参照してください。

**Azure Digital Twins**: Azure Digital Twins を使用すると、空間的な関係、使用パターン、デバイスのフリートを物理的な資産または環境のデジタル レプリカに変えるその他のビジネス コンテキストを考慮して、デバイス センサーをビジネス コンテキスト内でモデル化できます。 詳細については、「[Azure Digital Twins](https://azure.microsoft.com/services/digital-twins/)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [Metaswitch Fusion Core](metaswitch-fusion-core-overview.md) の詳細情報
- [Affirmed Private Network Service](affirmed-private-network-service-overview.md) の詳細情報
