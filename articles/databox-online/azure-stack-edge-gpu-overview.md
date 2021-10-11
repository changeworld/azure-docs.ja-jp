---
title: GPU 搭載の Microsoft Azure Stack Edge Pro の概要 | Microsoft Docs
description: Azure へのネットワークベースの転送に物理デバイスを使用するストレージ ソリューションである GPU 搭載の Azure Stack Edge Pro について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/05/2021
ms.author: alkohli
ms.openlocfilehash: 551933814b034c3ac6370e4fe25ffe93fc616a4a
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544984"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>GPU 搭載の Azure Stack Edge Pro とは

GPU 搭載の Azure Stack Edge Pro は、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 この記事では、Azure Stack Edge Pro ソリューションの概要、利点、主な機能、このデバイスを配置できるシナリオについて説明します。

GPU 搭載の Azure Stack Edge Pro は、サービスとしてのハードウェア ソリューションです。 ネットワーク ストレージ ゲートウェイとして機能し、かつ、高速な AI 推論を実現する GPU (グラフィカル処理ユニット) 搭載のクラウドマネージド デバイスをお届けします。 

## <a name="use-cases"></a>ユース ケース

ここでは、Azure Stack Edge Pro GPU を使用してエッジで高速な機械学習 (ML) 推論を実行し、データを Azure に送信する前に前処理するさまざまなシナリオを示します。

- <bpt id="p1">**</bpt>Azure Machine Learning による推論<ept id="p1">**</ept> - Azure Stack Edge Pro GPU により、ML モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 必要に応じて、ML モデルを再調整して改善し続けるために、データ セット全体を転送することができます。 Azure Stack Edge Pro GPU デバイスで Azure ML ハードウェア アクセラレータ モデルを使用する方法の詳細については、<bpt id="p1">[</bpt>Azure Stack Edge Pro GPU への Azure ML ハードウェア アクセラレーション対応モデルのデプロイ<ept id="p1">](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)</ept>に関する記事を参照してください。

- <bpt id="p1">**</bpt>データの前処理<ept id="p1">**</ept> - コンテナー化されたワークロードや Virtual Machines などのコンピューティング オプションを使用してデータを Azure に送信する前にデータを変換し、より実用的なデータセットを作成します。 前処理は以下の目的で使用できます。 

    - データを集計します。
    - 個人データを削除するなど、データを変更します。
    - ストレージと帯域幅を最適化したり、詳細な分析を行ったりするためのサブセット データ。
    - 分析して、IoT イベントに対応します。 

- <bpt id="p1">**</bpt>ネットワーク経由でデータを Azure に転送する<ept id="p1">**</ept> - 計算や分析をさらに進めたり、アーカイブ目的のために、Azure Stack Edge Pro GPU を使用して、Azure にデータを簡単かつ迅速に転送します。 

## <a name="key-capabilities"></a>主な機能

Azure Stack Edge Pro GPU には、次の機能があります。

|機能 |説明  |
|---------|---------|
|高速 AI 推論| 組み込みの GPU によって実現されます (モデルにより 1 つまたは 2 つ搭載)。 <br> 詳細については、[Azure Stack Edge デバイス上の GPU の共有](azure-stack-edge-gpu-sharing.md)を参照してください。|
|エッジ コンピューティング      |VM とコンテナー化されたワークロードをサポートすることで、データの分析、処理、フィルター処理を可能にします。 <ul><li>VM ワークロードの詳細については、[Azure Stack Edge の VM の概要](azure-stack-edge-gpu-virtual-machine-overview.md)に関するトピックを参照してください。</li> <li>コンテナー化されたワークロードについては、[Azure Stack Edge の Kubernetes の概要](azure-stack-edge-gpu-kubernetes-overview.md)に関するページを参照してください。</li></ul> |
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。 デバイス上のローカル キャッシュは、最近使用されたファイルの高速アクセスに使用されます。|
|クラウドでの管理     |デバイスとサービスは Azure portal を介して管理されます。|
|オフライン アップロード     | 切断モードでは、オフライン アップロードのシナリオに対応します。|
|サポートされているファイル転送プロトコル      | データ インジェストのために、標準の SMB、NFS、REST プロトコルをサポートします。 <br> サポート対象のバージョンについては、<bpt id="p1">[</bpt>Azure Stack Edge Pro GPU のシステム要件<ept id="p1">](azure-stack-edge-system-requirements.md)</ept>に関する記事を参照してください。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。 <br> 詳細については、「[Azure Stack Edge 上の共有の更新](azure-stack-edge-gpu-manage-shares.md#refresh-shares)」に関するページを参照してください。|
|暗号化    | ローカルでデータを暗号化し、クラウドへのデータ転送を <bpt id="p1">*</bpt>https<ept id="p1">*</ept> 経由で保護する BitLocker のサポート。|
|帯域幅調整| ピーク時の帯域幅の使用量を制限するように調整します。 <br> 詳細については、[Azure Stack Edge 上での帯域幅スケジュールの管理](azure-stack-edge-gpu-manage-bandwidth-schedules.md)に関するページを参照してください。|
|簡単な発注| Azure Edge Hardware Center によるデバイスの一括発注と追跡 (プレビュー)。 <br> 詳細については、[Azure Edge Hardware Center を介したデバイスの注文](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)に関するページを参照してください。|
|特殊なネットワーク機能|Azure Network Function Manager の Azure Marketplace エクスペリエンスを利用することで、モバイル パケット コア、SD-WAN エッジ、VPN サービスなどのネットワーク機能を、オンプレミス環境で実行されている Azure Stack Edge デバイスに迅速にデプロイします。 詳細については、<bpt id="p1">[</bpt>Azure Network Function Manager とは (プレビュー)<ept id="p1">](../network-function-manager/overview.md)</ept> に関するページを参照してください。|

<!--|ExpressRoute | Added security through ExpressRoute. Use peering configuration where traffic from local devices to the cloud storage endpoints travels over the ExpressRoute. For more information, see [ExpressRoute overview](../expressroute/expressroute-introduction.md).|-->


## <a name="components"></a>コンポーネント

Azure Stack Edge Pro GPU ソリューションは、Azure Stack Edge リソース、Azure Stack Edge Pro GPU 物理デバイス、およびローカル Web UI で構成されています。

* <bpt id="p1">**</bpt>Azure Stack Edge Pro GPU 物理デバイス<ept id="p1">**</ept> - Azure にデータを送信するように構成できる、Microsoft によって提供される 1U ラックマウント型サーバー。

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    詳細については、<bpt id="p1">[</bpt>Azure Stack Edge Pro GPU デバイスの注文の作成<ept id="p1">](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)</ept>に関する記事を参照してください。
    
* <bpt id="p1">**</bpt>Azure Stack Edge リソース<ept id="p1">**</ept> - さまざまな地理的な場所からアクセスできる Web インターフェイスから Azure Stack Edge Pro GPU デバイスを管理できるようにする Azure portal のリソース。 Azure Stack Edge リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  
   

* <bpt id="p1">**</bpt>Azure Stack Edge Pro GPU のローカル Web UI<ept id="p1">**</ept> - Azure Stack Edge Pro GPU デバイス上のブラウザーベースのローカル ユーザー インターフェイス。主にデバイスの初期構成を想定しています。 他にも、ローカル Web UI を使用して、診断の実行、Azure Stack Edge Pro GPU デバイスのシャットダウンと再起動し、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]
    
    Web ベース UI の使用については、<bpt id="p1">[</bpt>Web ベース UI を使用した Azure Stack Edge Pro GPU の管理<ept id="p1">](azure-stack-edge-manage-access-power-connectivity-mode.md)</ept>に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン

Azure Stack Edge Pro GPU 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じリージョンにある必要はありません。

- <bpt id="p1">**</bpt>リソースの提供<ept id="p1">**</ept> - このリリースでは、米国東部、西ヨーロッパ、東南アジアの各リージョンでリソースが提供されます。

- <bpt id="p1">**</bpt>デバイスの可用性<ept id="p1">**</ept> - Azure Stack Edge Pro GPU デバイスが使用可能なすべての国またはリージョンの一覧については、<bpt id="p4">[</bpt>Azure Stack Edge Pro GPU の価格<ept id="p4">](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)</ept>に関するページの <bpt id="p3">**</bpt>[Azure Stack Edge Pro]<ept id="p3">**</ept> タブにある <bpt id="p2">**</bpt>[可用性]<ept id="p2">**</ept> セクションにアクセスしてください。
    
- <bpt id="p1">**</bpt>転送先ストレージ アカウント<ept id="p1">**</ept> - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 パフォーマンスを最適にするには、ストレージ アカウントによって Azure Stack Edge Pro GPU のデータが格納されるリージョンと、デバイスが配置されている場所を、近くにする必要があります。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。

Azure Stack Edge サービスは、非リージョン サービスです。 詳細については、「<bpt id="p1">[</bpt>Azure のリージョンと Availability Zones<ept id="p1">](../availability-zones/az-overview.md)</ept>」をご覧ください。 Azure Stack Edge サービスは、特定の Azure リージョンに依存しません。そのため、ゾーン全体の障害やリージョン全体の障害に対する回復性があります。

Azure Stack Edge サービス、デバイス、およびデータ ストレージのリージョンの選択に関する考慮事項については、「<bpt id="p1">[</bpt>Azure Stack Edge のリージョンの選択<ept id="p1">](azure-stack-edge-gpu-regions.md)</ept>」を参照してください。

## <a name="next-steps"></a>次のステップ

- <bpt id="p1">[</bpt>Azure Stack Edge Pro GPU のシステム必要条件<ept id="p1">](azure-stack-edge-gpu-system-requirements.md)</ept>を確認します。

- <bpt id="p1">[</bpt>Azure Stack Edge Pro GPU の制限<ept id="p1">](azure-stack-edge-limits.md)</ept>を理解します。

- Azure portal で <bpt id="p1">[</bpt>Azure Stack Edge Pro GPU<ept id="p1">](azure-stack-edge-gpu-deploy-prep.md)</ept> をデプロイします。
