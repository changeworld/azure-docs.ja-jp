---
title: Azure Stack Edge Mini R の概要 | Microsoft Docs
description: Azure への Wi-Fi 経由での転送にバッテリを備えた携帯用物理デバイスを使用する、軍事用アプリケーション向けのストレージ ソリューションである Azure Stack Edge Mini R について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/04/2021
ms.author: alkohli
ms.openlocfilehash: df6838d1616d27c12d7176df0041a35d0d773f83
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545117"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Azure Stack Edge Mini R とは

Azure Stack Edge Mini R は、過酷な環境で使用するために設計された、携帯性に特別に優れ、頑丈なエッジ コンピューティング デバイスです。 Azure Stack Edge Mini R は、サービスとしてのハードウェア ソリューションとして提供されます。 ネットワーク ストレージ ゲートウェイとして機能し、かつ、高速な AI 推論を実現する Vision Processing Unit (VPU) 搭載のクラウドマネージド デバイスをお届けします。

この記事では、Azure Stack Edge Mini R ソリューションの概要、主要な機能、このデバイスを配置できるシナリオについて説明します。


## <a name="key-capabilities"></a>主な機能

Azure Stack Edge Mini R には、次の機能があります。

|機能 |説明  |
|---------|---------|
|ラグド ハードウェア| 過酷な環境向けに設計されたラグド ハードウェア|
|優れた携帯性| 非常に持ち運びやすいバッテリ式のフォーム ファクター。|
|クラウドでの管理|デバイスとサービスは Azure portal を介して管理されます。|
|Edge コンピューティング ワークロード|データの分析、処理、フィルター処理が可能です。<br>VM とコンテナー化されたワークロードがサポートされています。 <ul><li>VM ワークロードの詳細については、[Azure Stack Edge の VM の概要](azure-stack-edge-gpu-virtual-machine-overview.md)に関するトピックを参照してください。</li> <li>コンテナー化されたワークロードについては、[Azure Stack Edge の Kubernetes の概要](azure-stack-edge-gpu-kubernetes-overview.md)に関するページを参照してください。</li></ul>  |
|高速 AI 推論| Intel Movidius Myriad X VPU によって実現されます。 |
|有線およびワイヤレス | 有線およびワイヤレスのデータ転送が可能です。|
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。 デバイス上のローカル キャッシュは、最近使用されたファイルの高速アクセスに使用されます。|
|切断モード|  デバイスとサービスは、必要に応じて Azure Stack Hub を介して管理できます。 アプリケーションをオフライン モードで展開、実行、管理します。 <br> 切断モードでは、オフライン アップロードのシナリオに対応します。|
|サポートされているファイル転送プロトコル      |データ インジェスト用に標準の SMB、NFS、REST プロトコルがサポートされています。 <br> サポート対象のバージョンについては、<bpt id="p1">[</bpt>Azure Stack Edge Mini R のシステム必要条件<ept id="p1">](azure-stack-edge-gpu-system-requirements.md)</ept>に関する記事をご覧ください。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。 <br> 詳細については、「[Azure Stack Edge 上の共有の更新](azure-stack-edge-gpu-manage-shares.md#refresh-shares)」に関するページを参照してください。|
|二重暗号化    | 自己暗号化ドライブの使用によって暗号化の最初のレイヤーが提供されます。 VPN によって暗号化の第 2 レイヤーが提供されます。 ローカルでデータを暗号化し、クラウドへのデータ転送を <bpt id="p1">*</bpt>https<ept id="p1">*</ept> 経由で保護する BitLocker のサポート。 <br> 詳細については、[Azure Stack Edge Pro R デバイス上の VPN の構成](azure-stack-edge-mini-r-configure-vpn-powershell.md)に関するページを参照してください。|
|帯域幅調整| ピーク時の帯域幅の使用量を制限するように調整します。 <br> 詳細については、[Azure Stack Edge 上での帯域幅スケジュールの管理](azure-stack-edge-gpu-manage-bandwidth-schedules.md)に関するページを参照してください。|
|簡単な発注| Azure Edge Hardware Center によるデバイスの一括発注と追跡 (プレビュー)。 <br> 詳細については、[Azure Edge Hardware Center を介したデバイスの注文](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)に関するページを参照してください。|

## <a name="use-cases"></a>ユース ケース

ここでは、Azure Stack Edge Mini R を使用してエッジで高速な機械学習 (ML) 推論を実行し、データを Azure に送信する前に前処理するさまざまなシナリオを示します。

- <bpt id="p1">**</bpt>Azure Machine Learning による推論<ept id="p1">**</ept> - Azure Stack Edge Mini R により、ML モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 必要に応じて、ML モデルを再調整して改善し続けるために、データ セット全体を転送することができます。 Azure Stack Edge Mini R デバイスで Azure ML ハードウェア アクセラレータ モデルを使用する方法の詳細については、<bpt id="p1">[</bpt>Azure Stack Edge Mini R への Azure ML ハードウェア アクセラレーション対応モデルのデプロイ<ept id="p1">](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)</ept>に関する記事をご覧ください。

- <bpt id="p1">**</bpt>データの前処理<ept id="p1">**</ept> - データを Azure に送信する前に、コンテナーや仮想マシンなどのコンピューティング オプションを使用して変換し、より実用的なデータセットを作成します。 前処理は以下の目的で使用できます。

    - データを集計します。
    - 個人データを削除するなど、データを変更します。
    - ストレージと帯域幅を最適化したり、詳細な分析を行ったりするためのサブセット データ。
    - 分析して、IoT イベントに対応します。

- <bpt id="p1">**</bpt>ネットワーク経由でデータを Azure に転送する<ept id="p1">**</ept> - Azure Stack Edge Mini R を使用して簡単かつ迅速に Azure にデータを転送し、さらなるコンピューティングや分析を行ったり、アーカイブしたりできます。

## <a name="components"></a>コンポーネント

Azure Stack Edge Mini R ソリューションは、Azure Stack Edge リソース、Azure Stack Edge Mini R のラグドで持ち運びやすい物理デバイス、およびローカル Web UI で構成されています。

* <bpt id="p1">**</bpt>Azure Stack Edge Mini R 物理デバイス<ept id="p1">**</ept> - Microsoft が提供する、携帯性に特別に優れ、頑丈なコンピューティングおよびストレージ デバイス。 このデバイスにはバッテリが搭載されており、重量は 7 ポンド (3175 グラム) 未満です。

    ![Azure Stack Edge Mini R デバイス](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    詳細については、<bpt id="p1">[</bpt>Azure Stack Edge Mini R デバイスの注文の作成<ept id="p1">](azure-stack-edge-mini-r-deploy-prep.md#create-a-new-resource)</ept>に関する記事を参照してください。

* <bpt id="p1">**</bpt>Azure Stack Edge リソース<ept id="p1">**</ept> - さまざまな地理的な場所からアクセス可能な Web インターフェイスから、ラグドな Azure Stack Edge Mini R デバイスを管理できる、Azure portal でのリソース。 Azure Stack Edge リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  

* <bpt id="p1">**</bpt>Azure Stack Edge Mini R のローカル Web UI<ept id="p1">**</ept> - Azure Stack Edge Mini R デバイス上のブラウザー ベースのローカル ユーザー インターフェイス。主にデバイスの初期構成を想定しています。 他にも、ローカル Web UI を使用して、診断の実行、Azure Stack Edge Pro デバイスのシャットダウンと再起動し、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]

## <a name="region-availability"></a>利用可能なリージョン

Azure Stack Edge Mini R 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じリージョン内にある必要はありません。

- <bpt id="p1">**</bpt>リソースの使用可能性<ept id="p1">**</ept> - Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、「<bpt id="p2">[</bpt>リージョン別の利用可能な製品<ept id="p2">](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)</ept>」を参照してください。 

- <bpt id="p1">**</bpt>デバイスの可用性<ept id="p1">**</ept> - Azure Stack Edge Mini R デバイスが使用可能なすべての国の一覧については、<bpt id="p2">[</bpt>Azure Stack Edge Mini R の価格<ept id="p2">](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR)</ept>に関するページの [Azure Stack Edge Mini R] タブにある [可用性] セクションにアクセスしてください。

- <bpt id="p1">**</bpt>転送先ストレージ アカウント<ept id="p1">**</ept> - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 最適なパフォーマンスを得るには、ストレージ アカウントによって Azure Stack Edge Mini R のデータが格納されるリージョンと、デバイスが配置される場所を近くにする必要があります。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。

Azure Stack Edge サービスは、非リージョン サービスです。 詳細については、「<bpt id="p1">[</bpt>Azure のリージョンと Availability Zones<ept id="p1">](../availability-zones/az-overview.md)</ept>」をご覧ください。 Azure Stack Edge サービスは、特定の Azure リージョンに依存しません。そのため、ゾーン全体の障害やリージョン全体の障害に対する回復性があります。

Azure Stack Edge サービス、デバイス、およびデータ ストレージのリージョンの選択に関する考慮事項については、「<bpt id="p1">[</bpt>Azure Stack Edge のリージョンの選択<ept id="p1">](azure-stack-edge-gpu-regions.md)</ept>」を参照してください。

## <a name="next-steps"></a>次のステップ

- <bpt id="p1">[</bpt>Azure Stack Edge Mini R のシステム必要条件<ept id="p1">](azure-stack-edge-gpu-system-requirements.md)</ept>を確認します。