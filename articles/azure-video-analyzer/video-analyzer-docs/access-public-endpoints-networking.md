---
title: パブリック エンドポイントとネットワーク
description: Azure Video Analyzer では、管理、インジェスト、再生など、さまざまな製品シナリオを可能にする一連のパブリック ネットワーク エンドポイントを公開します。 この記事では、パブリック エンドポイントとネットワークにアクセスする方法について説明します。
ms.topic: how-to
ms.date: 11/04/2021
ms.openlocfilehash: 2285408cbdc6fd9f633e8c34c253c2d5921bc4db
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552907"
---
# <a name="public-endpoints-and-networking"></a>パブリック エンドポイントとネットワーク

Azure Video Analyzer では、管理、インジェスト、再生など、さまざまな製品シナリオを可能にする一連のパブリック ネットワーク エンドポイントを公開します。 この記事では、これらのエンドポイントについて説明し、その使い方について詳しい情報を提供します。 次の図は、これらのエンドポイントに加えて、関連付けられている Azure サービスによって公開される主要なエンドポイントの一部を示しています。

:::image type="content" source="./media/access-public-endpoints-networking/endpoints-and-networking.svg" alt-text="Azure Video Analyzer のパブリック エンドポイントを表す画像":::

## <a name="video-analyzer-endpoints"></a>Video Analyzer エンドポイント 

このセクションでは、Video Analyzer エンドポイントのリストを示します。

### <a name="streaming"></a>ストリーム

* **目的**: オーディオ、ビデオ、推論のデータを公開します。これらは、[Video Analyzer プレーヤー ウィジェット](player-widget.md)または互換性のある DASH/HLS プレーヤーで使用できます。
* **認証と認可**: エンドポイントの認可は、Video Analyzer サービスによって発行されたトークンを通じて実施されます。 トークンは 1 つのビデオに制限され、ビデオごとにクライアントおよび管理 API に適用される承認規則に基づいて暗黙的に発行されます。 承認フローは、Video Analyzer プレーヤー ウィジェットによって自動的に処理されます。
* **要件**: クラウド経由でコンテンツを再生するには、この一連のエンドポイントへのアクセスが必要です。

### <a name="client-apis"></a>クライアント API

* **目的**: [Video Analyzer ビデオ リソース](terminology.md#video)のメタデータ (タイトル、説明など) を公開します。 これにより、顧客が開発したクライアント アプリケーションに情報豊富なビデオ オブジェクトを表示できます。 このメタデータは、Video Analyzer プレーヤー ウィジェットによって活用され、顧客アプリケーションから直接利用することもできます。
* **認証と認可**: エンドポイントの認可は、顧客が定義した [アクセス ポリシー](access-policies.md)と、顧客が発行した JWT トークンの組み合わせによって実施されます。 Video Analyzer 管理 API を使用して、1 つ以上のアクセス ポリシーを定義できます。 このようなポリシーは、アクセスのスコープと、トークンで検証するために必要な要求を記述します。 Video Analyzer アカウントに作成されたアクセス ポリシーがない場合、アクセスは拒否されます。
* **要件**: Video Analyzer プレーヤー ウィジェットと顧客が開発した同様のクライアント アプリケーションでビデオ メタデータを取得し、再生の認可を行う場合に、このエンドポイントへのアクセスが必要です。

### <a name="edge-service-integration"></a>Edge サービスの統合

* **目的**: 

    * Video Analyzer Edge モジュールによって定期的にダウンロードされるポリシーを公開します。 このようなポリシーは、課金や接続の要件など、Edge モジュールの基本的な動作を制御します。
    * Video Analyzer Edge モジュールが顧客のストレージ アカウントにビデオ データを記録できるようにする Azure Storage SAS URL の取得など、クラウドへのビデオ公開のオーケストレーション。
* **認証と認可**: 初期認証は、Video Analyzer 管理 API によって発行される、有効期限の短いプロビジョニング トークンを使用して行われます。 初期ハンドシェイクが完了すると、モジュールとサービスは、この時点以降に使用される、自動的にローテーションされる承認キーのセットを交換します。
* **要件**: Video Analyzer Edge モジュールが正しく機能するには、このエンドポイントへのアクセスが必要です。 このエンドポイントに 36 時間以内に到達できない場合、Edge モジュールは機能を停止します。

## <a name="telemetry"></a>テレメトリ

* **目的**: オプションでテレメトリ データを定期的に送信すると、Microsoft では Video Analyzer Edge モジュールの使用状況をよりよく把握し、互換性、パフォーマンス、その他の製品分野で行える将来の改善を事前に特定できます。
* **認証と認可**: 認可は、事前に確立されたキーに基づいて行います。
* **要件**: このエンドポイントへのアクセスは省略可能であり、製品の機能に影響することはありません。 データの収集と送信は、モジュール ツインのプロパティを使用して無効にすることができます。

## <a name="associated-azure-endpoints"></a>関連付けられている Azure エンドポイント 

> [!NOTE]
> この記事で説明するエンドポイントのリストは、関連付けられているサービス エンドポイントの包括的なリストではありません。 これは、Video Analyzer の通常の操作に必要なエンドポイントを示す、参考のためのリストです。 各サービスによって公開されるエンドポイントの完全なリストについては、個々の Azure サービスのドキュメントをご覧ください。

## <a name="azure-storage"></a>Azure Storage

* **目的** : [ビデオ シンク](pipeline.md#video-sink) ノードを介してクラウド上にビデオを格納するように [パイプライン](pipeline.md)が構成されている場合、音声、ビデオ、および推論データを記録します。
* **認証と承認**: 承認は、標準の Azure Storage サービスの認証と承認の実施によって行われます。 この場合、ストレージにはコンテナー固有の SAS URL を介してアクセスします。
* **要件**: このエンドポイントへのアクセスは、ビデオをクラウドにアーカイブするように Video Analyzer エッジ パイプラインが構成されている場合にのみ必要です。

## <a name="iot-hub"></a>IoT Hub

* **目的**: Azure IoT Hub およびエッジ デバイスの制御およびデータ プレーン。
* **認証と承認**: Azure IoT Hub のドキュメントを参照してください。
* **要件**: Azure Video Analyzer エッジ モジュールが正しく動作するためには、Azure IoT Edge ランタイムが実行されているエッジ デバイスが適切に構成され、機能していることが必要です。

##  <a name="tls-encryption"></a>TLS 暗号化 

* **暗号化とサーバー認証**: すべての Video Analyzer エンドポイントは、TLS 1.2 準拠のエンドポイントを介して公開されます。

##  <a name="references"></a>リファレンス 

パブリック:

* [Azure リソース マネージャーの概要](../../azure-resource-manager/management/overview.md)
* [Azure IoT Hub のエンドポイントについて](../../iot-hub/iot-hub-devguide-endpoints.md)
* [Azure Private Link とは](../../private-link/private-link-overview.md)
* [Azure サービス タグの概要](../../virtual-network/service-tags-overview.md)

## <a name="next-steps"></a>次のステップ

[アクセス ポリシー](access-policies.md) 
