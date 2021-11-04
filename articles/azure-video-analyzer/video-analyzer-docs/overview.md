---
title: Azure Video Analyzer とは
description: このトピックでは、Azure Video Analyzer の概要を示します
ms.topic: overview
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b72abcd0f3df246e441d68643456ee7134522728
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563152"
---
# <a name="what-is-azure-video-analyzer-preview"></a>Azure Video Analyzer とは (プレビュー)
 
Azure Video Analyzer は、エッジとクラウドにまたがるインテリジェントなビデオ アプリケーションを構築するためのプラットフォームを提供します。 このプラットフォームは、IoT Edge モジュールと Azure サービスで構成されます。 ライブ ビデオをキャプチャ、記録、分析し、結果 (ビデオやビデオからの分析情報など) をエッジまたはクラウドに公開する機能を提供します。

Azure Video Analyzer エッジ モジュールは、Azure Stream Analytics、Azure Cognitive Services などの他の Azure IoT Edge モジュール、および Azure Event Hubs や Azure Cognitive Services などの他の Azure クラウド サービスと一緒に使用して、強力なハイブリッド (エッジとクラウドの混成) アプリケーションを構築できます。 この拡張可能なエッジ モジュールは、オープン ソースの機械学習モデルとトレーニング データを使用して構築された Azure Cognitive Services コンテナーやカスタム エッジ モジュールなど、さまざまな AI エッジ モジュールとシームレスに統合されます。 Azure Video Analyzer プラットフォーム上に構築することで、複雑なシステムの構築、運用、保守の複雑さを懸念せずに、ライブ ビデオを分析できます。

ライブ ビデオの分析とは別に、エッジ モジュールを使用すると、必要に応じて、エッジまたはクラウドにローカルでビデオを記録したり、Azure サービス (エッジやクラウド内) にビデオ分析情報を発行したりすることができます。 ビデオとビデオの分析情報がクラウドに記録されている場合は、Azure Video Analyzer クラウド サービスを使用してそれらを管理できます。

そのため、Azure Video Analyzer クラウド サービスは、記録、再生、エクスポート (外部で共有できるビデオ ファイルの生成) などの[ビデオ管理システム (VMS)](https://en.wikipedia.org/wiki/Video_management_system) 機能で IoT ソリューションを強化するために使用できます。 また、次の図に示すように、カメラがクラウドに直接接続されているのと同じ機能を備えるクラウドネイティブ ソリューションを構築するためにも使用できます。

## <a name="accelerate-iot-solutions-development"></a>IoT ソリューション開発の高速化 

ビデオ分析を他の IoT センサーからの信号やビジネス データと組み合わせる IoT ソリューションは、ビジネス上の意思決定の自動化または半自動化に役立ち、生産性を高めます。 Video Analyzer を使用すると、このようなソリューションをより迅速に構築できます。 ビジネスに固有のビデオ分析モジュールとロジックの構築に専念でき、ビデオ パイプラインの管理と実行の複雑さは、プラットフォームによって隠されます。

Video Analyzer を使用すると、既存の[ビデオ管理システム (VMS)](https://en.wikipedia.org/wiki/Video_management_system) で引き続き [CCTV カメラ](https://en.wikipedia.org/wiki/Closed-circuit_television_camera)を使用し、独立したビデオ分析アプリを構築できます。 Azure Video Analyzer を、Computer Vision SDK シリーズおよびツールキットと一緒に使用することで、最先端の IoT ソリューションを構築できます。 以下の図は、これを示しています。

![Video Analyzer を使用して IoT ソリューションを開発する](./media/overview/product-diagram.svg)

### <a name="concepts"></a>概念

* [パイプライン](pipeline.md)
* [ビデオ記録を行わない Video Analyzer](analyze-live-video-without-recording.md)
* [ビデオ録画](video-recording.md)


## <a name="compliance-privacy-and-security"></a>コンプライアンス、プライバシー、セキュリティ

重要な注意事項として、Video Analyzer の使用に適用されるすべての法律に従う必要があります。また、他の人の権利を犯したり、他の人に害を及ぼしたりする可能性のある方法で Video Analyzer またはいずれかの Azure サービスを使用することはできません。

Video Analyzer でビデオを処理する前に、ビデオを使用するための適切な権限をすべて取得している必要があります。これには、法律で義務づけられている場合において、ビデオまたは画像に含まれる人物から、Video Analyzer および Azure でのデータの使用、処理、および保管について、必要なすべての同意を得ることが含まれます (このような人物が存在する場合)。 一部の法的管轄区域では、生体認証データなどの特定のカテゴリのデータの収集、オンライン処理、および保管に関して特別な法的要件が課せられる場合があります。 特別な法的要件の対象となるデータの処理と保管のために Video Analyzer と Azure を使用する前に、適用される可能性のあるこのような法的要件に準拠していることを確認する必要があります。

Video Analyzer のコンプライアンス、プライバシー、およびセキュリティについては、Microsoft [トラスト センター](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)にアクセスしてください。 Microsoft のプライバシー義務、データの取り扱いとデータ保有の慣行 (データの削除方法など) については、Microsoft の[プライバシー ステートメント](https://privacy.microsoft.com/PrivacyStatement)、[製品契約条件](https://www.microsoft.com/licensing/terms/welcome/welcomepage)、および [Microsoft の製品およびサービス データ保護の補遺](https://www.microsoft.com/licensing/docs/view/Microsoft-Products-and-Services-Data-Protection-Addendum-DPA) ("DPA") に関するページをご確認ください。 Azure Video Analyzer を使用することによって、製品契約条件、DPA およびプライバシー ステートメントに従うことに同意したものと見なされます。

## <a name="next-steps"></a>次のステップ

* 「[クイックスタート: Azure Video Analyzer で作業を開始する](get-started-detect-motion-emit-events.md)」の記事に従って、ライブ ビデオでモーション検出を実行する方法を確認してください。
* [用語](terminology.md)を確認します。
