---
title: GPU 搭載の Microsoft Azure Stack Edge Pro の概要 | Microsoft Docs
description: Azure へのネットワークベースの転送に物理デバイスを使用するストレージ ソリューションである GPU 搭載の Azure Stack Edge Pro について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 01/18/2021
ms.author: alkohli
ms.openlocfilehash: 8e829d149e96cf658c7a5aaf37291e84def2061e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602657"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>GPU 搭載の Azure Stack Edge Pro とは

GPU 搭載の Azure Stack Edge Pro は、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 この記事では、Azure Stack Edge Pro ソリューションの概要、利点、主な機能、このデバイスを配置できるシナリオについて説明します。

GPU 搭載の Azure Stack Edge Pro は、サービスとしてのハードウェア ソリューションです。 ネットワーク ストレージ ゲートウェイとして機能し、かつ、高速な AI 推論を実現する GPU (グラフィカル処理ユニット) 搭載のクラウドマネージド デバイスをお届けします。 

## <a name="use-cases"></a>ユース ケース

ここでは、Azure Stack Edge Pro を使用してエッジで高速な機械学習 (ML) 推論を実行し、データを Azure に送信する前に前処理するさまざまなシナリオを示します。

- **Azure Machine Learning による推論** - Azure Stack Edge Pro により、ML モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 必要に応じて、ML モデルを再調整して改善し続けるために、データ セット全体を転送することができます。 Azure Stack Edge Pro デバイスで Azure ML ハードウェア アクセラレータ モデルを使用する方法の詳細については、[Azure Stack Edge Pro への Azure ML ハードウェア アクセラレーション対応モデルのデプロイ](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)に関する記事を参照してください。

- **データの前処理** - コンテナー化されたワークロードや Virtual Machines などのコンピューティング オプションを使用してデータを Azure に送信する前にデータを変換し、より実用的なデータセットを作成します。 前処理は以下の目的で使用できます。 

    - データを集計します。
    - 個人データを削除するなど、データを変更します。
    - ストレージと帯域幅を最適化したり、詳細な分析を行ったりするためのサブセット データ。
    - 分析して、IoT イベントに対応します。 

- **ネットワーク経由でデータを Azure に転送する** - 計算や分析をさらに進めたり、アーカイブ目的のために、Azure Stack Edge Pro を使用して、Azure にデータを簡単かつ迅速に転送します。 

## <a name="key-capabilities"></a>主な機能

Azure Stack Edge Pro には、次の機能があります。

|機能 |説明  |
|---------|---------|
|高速 AI 推論| 組み込みの GPU によって実現されます (モデルにより 1 つまたは 2 つ搭載)。|
|エッジ コンピューティング      |VM とコンテナー化されたワークロードをサポートすることで、データの分析、処理、フィルター処理を可能にします。 |
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。 デバイス上のローカル キャッシュは、最近使用されたファイルの高速アクセスに使用されます。|
|クラウドでの管理     |デバイスとサービスは Azure portal を介して管理されます。  |
|オフライン アップロード     | 切断モードでは、オフライン アップロードのシナリオに対応します。|
|サポートされているファイル転送プロトコル      | データ インジェストのために、標準の SMB、NFS、REST プロトコルをサポートします。 <br> サポート対象のバージョンについては、「[Azure Stack Edge Pro のシステム要件](azure-stack-edge-system-requirements.md)」を参照してください。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|暗号化    | ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート。|
|帯域幅調整| ピーク時の帯域幅の使用量を制限するように調整します。|
<!--|ExpressRoute | ExpressRoute によってセキュリティが強化されました。 ローカル デバイスからクラウド ストレージ エンドポイントへのトラフィックが ExpressRoute 経由で転送されるピアリング構成を使用します。 詳細については、「[ExpressRoute の概要](../expressroute/expressroute-introduction.md)」を参照してください。|-->

## <a name="components"></a>コンポーネント

Azure Stack Edge Pro ソリューションは、Azure Stack Edge リソース、Azure Stack Edge Pro 物理デバイス、およびローカル Web UI で構成されています。

* **Azure Stack Edge Pro 物理デバイス** - Azure にデータを送信するように構成可能な Microsoft 製の 1U ラックマウント型サーバー。
    
* **Azure Stack Edge リソース** - さまざまな地理的な場所からアクセスできる Web インターフェイスから、Azure Stack Edge Pro デバイスを管理できる、Azure portal でのリソース。 Azure Stack Edge リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  

    詳細については、[Azure Stack Edge Pro デバイスの注文の作成](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)に関する記事を参照してください。

* **Azure Stack Edge Pro のローカル Web UI** - Azure Stack Edge Pro デバイス上のブラウザー ベースのローカル ユーザー インターフェイス。主にデバイスの初期構成を想定しています。 他にも、ローカル Web UI を使用して、診断の実行、Azure Stack Edge Pro デバイスのシャットダウンと再起動し、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。

    Web ベース UI の使用については、[Web ベース UI を使用した Azure Stack Edge Pro の管理](azure-stack-edge-manage-access-power-connectivity-mode.md)に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン

Azure Stack Edge Pro 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じリージョンにある必要はありません。

- **リソースの提供** - このリリースでは、米国東部、西ヨーロッパ、東南アジアの各リージョンでリソースが提供されます。

- **デバイスの可用性** - Azure Stack Edge Pro デバイスが使用可能なすべての国またはリージョンの一覧については、[Azure Stack Edge Pro の価格](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)に関するページの **[Azure Stack Edge Pro]** タブにある **[可用性]** セクションにアクセスしてください。
    
- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 パフォーマンスを最適にするには、ストレージ アカウントによって Azure Stack Edge Pro のデータが格納されるリージョンと、デバイスが配置されている場所を、近くにする必要があります。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。

Azure Stack Edge サービスは、非リージョン サービスです。 詳細については、「[Azure のリージョンと Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview)」をご覧ください。 Azure Stack Edge サービスは、特定の Azure リージョンに依存しません。そのため、ゾーン全体の障害やリージョン全体の障害に対する回復性があります。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro のシステム要件](azure-stack-edge-gpu-system-requirements.md)を確認します。

- [Azure Stack Edge Pro の制限](azure-stack-edge-limits.md)を理解します。
- Azure portal で [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) を配置します。
