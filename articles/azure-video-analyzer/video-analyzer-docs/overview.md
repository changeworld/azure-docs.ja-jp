---
title: Azure Video Analyzer とは
description: このトピックでは、Azure Video Analyzer の概要を示します。
ms.service: azure-video-analyzer
ms.topic: overview
ms.date: 03/11/2021
ms.openlocfilehash: c1d556662794de6127b0c455ac812b94b5a05742
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387935"
---
# <a name="what-is-azure-video-analyzer-preview"></a>Azure Video Analyzer とは (プレビュー)

Azure Video Analyzer は、エッジとクラウドにまたがるインテリジェントなビデオ アプリケーションを構築するためのプラットフォームを提供します。 このプラットフォームは、IoT Edge モジュールと、関連付けられた Azure サービスで構成されます。 ライブ ビデオをキャプチャ、記録、および分析し、結果 (ビデオやビデオ分析) を発行する機能を提供します。 ビデオは、エッジまたは Video Analyzer クラウド サービスに発行できます。一方、ビデオ分析は、(クラウドまたはエッジ内の) Azure サービスに発行できます。 このプラットフォームは、ビデオ分析を使用して IoT ソリューションを強化するために使用できます。 Video Analyzer 機能は、他の Azure IoT Edge モジュール (Stream Analytics on IoT Edge、Cognitive Services on IoT Edge など)、およびクラウドの Azure サービス (イベント ハブ、Cognitive Services など) と組み合わせて、強力なハイブリッド (たとえばエッジ + クラウド) アプリケーションを構築できます。

Video Analyzer エッジ モジュールは、拡張可能なプラットフォームとして設計されており、さまざまなビデオ分析エッジ モジュール (Cognitive Services コンテナーや、オープンソースの機械学習モデルまたは独自のデータでトレーニングしたカスタム モデルを使用して自分で構築したカスタム エッジ モジュールなど) を接続できます。それらを使用して、ライブ ビデオ パイプラインを構築および実行する複雑さを気にすることなく、ライブ ビデオを分析できます。 Video Analyzer クラウド サービスを使用すると、このようなワークフローからビデオおよびビデオ分析を再生できます。

## <a name="accelerate-iot-solutions-development"></a>IoT ソリューション開発の高速化 

ビデオ分析を他の IoT センサーからの信号やビジネス データと組み合わせる IoT ソリューションは、ビジネス上の意思決定の自動化または半自動化に役立ち、生産性を高めます。 Video Analyzer を使用すると、このようなソリューションをより迅速に構築できます。 ビジネスに固有のビデオ分析モジュールとロジックの構築に専念でき、ビデオ パイプラインの管理と実行の複雑さは、プラットフォームによって隠されます。

Video Analyzer を使用すると、既存の[ビデオ管理システム (VMS)](https://en.wikipedia.org/wiki/Video_management_system) で引き続き [CCTV カメラ](https://en.wikipedia.org/wiki/Closed-circuit_television_camera)を使用し、独立したビデオ分析アプリを構築できます。 Video Analyzer を、Computer Vision SDK シリーズおよびツールキットと組み合わせて使用することで、最先端の IoT ソリューションを構築できます。 以下の図は、これを示しています。

![Video Analyzer を使用して IoT ソリューションを開発する](./media/overview/product-diagram.svg)

### <a name="concepts"></a>概念

* [パイプライン](pipeline.md)
* [ビデオ録画](video-recording.md)
* [継続的なビデオ記録](continuous-video-recording.md)
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)
* [ビデオ記録を行わない Video Analyzer](analyze-live-video-without-recording.md)

## <a name="compliance-privacy-and-security"></a>コンプライアンス、プライバシー、セキュリティ

重要な注意事項として、Video Analyzer の使用に適用されるすべての法律に従う必要があります。また、他の人の権利を犯したり、他の人に害を及ぼしたりする可能性のある方法で Video Analyzer またはいずれかの Azure サービスを使用することはできません。

Video Analyzer でビデオを処理する前に、ビデオを使用するための適切な権限をすべて取得している必要があります。これには、法律で義務づけられている場合において、ビデオまたは画像に含まれる人物から、Video Analyzer および Azure でのデータの使用、処理、および保管について、必要なすべての同意を得ることが含まれます (このような人物が存在する場合)。 一部の法的管轄区域では、生体認証データなどの特定のカテゴリのデータの収集、オンライン処理、および保管に関して特別な法的要件が課せられる場合があります。 特別な法的要件の対象となるデータの処理と保管のために Video Analyzer と Azure を使用する前に、適用される可能性のあるこのような法的要件に準拠していることを確認する必要があります。

Video Analyzer のコンプライアンス、プライバシー、およびセキュリティについては、Microsoft [トラスト センター](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)にアクセスしてください。 Microsoft のプライバシー義務、データの取り扱いとデータ保有の慣行 (データの削除方法など) については、Microsoft の[プライバシー ステートメント](https://privacy.microsoft.com/PrivacyStatement)、[オンライン サービス条件](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST")、および[データ処理の補遺](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") に関するページをご確認ください。 Video Analyzer を使用することによって、OST、DPA およびプライバシー ステートメントに従うことに同意したものと見なされます。

## <a name="next-steps"></a>次の手順

* [Azure Video Analyzer での作業の開始のクイックスタート](get-started-detect-motion-emit-events.md)の記事に従って、ライブ ビデオ フィードでモーション検出を実行する方法を確認してください。
* [用語](terminology.md)を確認します
