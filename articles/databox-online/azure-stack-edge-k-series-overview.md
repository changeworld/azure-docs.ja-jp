---
title: Azure Stack Edge Mini R の概要 | Microsoft Docs
description: Azure への Wi-Fi 経由での転送にバッテリを備えた携帯用物理デバイスを使用する、軍事用アプリケーション向けのストレージ ソリューションである Azure Stack Edge Mini R について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 20efae411ae4d2fae9bf3b5e69dbfdd98da1603a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985639"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Azure Stack Edge Mini R とは

Azure Stack Edge Mini R は、サービスとしてのハードウェア ソリューションです。 Microsoft からお客様に、ラグドで、クラウドで管理される、非常に持ち運びやすいデバイスが発送されます。これには高速な AI 推論を実現する Vision Processing Unit (VPU) が組み込まれており、ネットワーク ストレージ ゲートウェイの全機能が備わっています。 これらのデバイスは最も過酷な環境での使用に適しており、AI、分析、およびサーバーレス コンピューティング向けに最適化されています。

この記事では、Azure Stack Edge Mini R ソリューションの概要、主要な機能、このデバイスを配置できるシナリオについて説明します。


## <a name="key-capabilities"></a>主な機能

Azure Stack Edge Mini R には、次の機能があります。

|機能 |説明  |
|---------|---------|
|ラグド ハードウェア| 最も過酷な環境向けに設計されたラグド ハードウェア。|
|優れた携帯性| 非常に持ち運びやすいバッテリ式のフォーム ファクター。|
|クラウドでの管理|デバイスとサービスは Azure portal を介して管理されます。|
|Edge コンピューティング ワークロード|データの分析、処理、フィルター処理が可能です。<br>VM とコンテナー化されたワークロードがサポートされています。 |
|高速 AI 推論| Intel Movidius Myriad X VPU によって実現されます。|
|有線およびワイヤレス | 有線およびワイヤレスのデータ転送が可能です。|
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。 デバイス上のローカル キャッシュは、最近使用されたファイルの高速アクセスに使用されます。|
|切断モード|  デバイスとサービスは、必要に応じて Azure Stack Hub を介して管理できます。 アプリケーションをオフライン モードで展開、実行、管理します。 <br> 切断モードでは、オフライン アップロードのシナリオに対応します。|
|サポートされるプロトコル     |データ インジェスト用に標準の SMB、NFS、REST プロトコルがサポートされています。 <br> サポート対象のバージョンについては、[Azure Stack Edge Mini R のシステム必要条件](azure-stack-edge-gpu-system-requirements.md)に関する記事をご覧ください。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|二重暗号化    | 自己暗号化ドライブの使用によって暗号化の最初のレイヤーが提供されます。 VPN によって暗号化の第 2 レイヤーが提供されます。 ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート。|
|帯域幅調整| ピーク時の帯域幅の使用量を制限するように調整します。|

## <a name="use-cases"></a>ユース ケース

ここでは、Azure Stack Edge Mini R を使用してエッジで高速な機械学習 (ML) 推論を実行し、データを Azure に送信する前に前処理するさまざまなシナリオを示します。

- **Azure Machine Learning による推論** - Azure Stack Edge Mini R により、ML モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 必要に応じて、ML モデルを再調整して改善し続けるために、データ セット全体を転送することができます。 Azure Stack Edge Mini R デバイスで Azure ML ハードウェア アクセラレータ モデルを使用する方法の詳細については、[Azure Stack Edge Mini R への Azure ML ハードウェア アクセラレーション対応モデルのデプロイ](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)に関する記事をご覧ください。

- **データの前処理** - データを Azure に送信する前に、コンテナーや仮想マシンなどのコンピューティング オプションを使用して変換し、より実用的なデータセットを作成します。 前処理は以下の目的で使用できます。

    - データを集計します。
    - 個人データを削除するなど、データを変更します。
    - ストレージと帯域幅を最適化したり、詳細な分析を行ったりするためのサブセット データ。
    - 分析して、IoT イベントに対応します。

- **ネットワーク経由でデータを Azure に転送する** - Azure Stack Edge Mini R を使用して簡単かつ迅速に Azure にデータを転送し、さらなるコンピューティングや分析を行ったり、アーカイブしたりできます。

## <a name="components"></a>コンポーネント

Azure Stack Edge Mini R ソリューションは、Azure Stack Edge リソース、Azure Stack Edge Mini R のラグドで持ち運びやすい物理デバイス、およびローカル Web UI で構成されています。

* **Azure Stack Edge Mini R 物理デバイス** - Microsoft 提供の、オンボード バッテリとラグドな外装を備えた非常に持ち運びやすいデバイス。Azure にデータを送信するように構成できます。 バッテリを含めたデバイスの重量が 7 lbs 未満。

    ![Azure Stack Edge Mini R デバイス](media/azure-stack-edge-k-series-overview/perspective-view-1.png)

* **Azure Stack Edge リソース** - さまざまな地理的な場所からアクセス可能な Web インターフェイスから、ラグドな Azure Stack Edge Mini R デバイスを管理できる、Azure portal でのリソース。 Azure Stack Edge リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  

* **Azure Stack Edge Mini R ローカル Web UI** - デバイスの初期構成にローカル Web UI を使用して、診断の実行、Azure Stack Edge Mini R デバイスのシャットダウンと再起動、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。


## <a name="region-availability"></a>利用可能なリージョン

Azure Stack Edge Mini R 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じリージョン内にある必要はありません。

- **リソースの使用可能性** - Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)」を参照してください。 

- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 最適なパフォーマンスを得るには、ストレージ アカウントによって Azure Stack Edge Mini R のデータが格納されるリージョンと、デバイスが配置される場所を近くにする必要があります。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Mini R のシステム必要条件](azure-stack-edge-gpu-system-requirements.md)を確認します。


