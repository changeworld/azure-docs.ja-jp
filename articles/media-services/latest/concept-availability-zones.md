---
title: 可用性ゾーン
description: Media Services では、障害の分離をもたらす Availability Zones をサポートします
services: media-services
author: johndeu
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: johndeu
ms.openlocfilehash: d50af7acadb17fe060ede90ad93896e465d3bad6
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746953"
---
# <a name="availability-zones"></a>可用性ゾーン

Azure Media Services では、同じ Azure リージョン内に障害から分離された場所を提供する [Availability Zones](../../availability-zones/az-overview.md) を使用します。 Media Services は、[利用可能な場所](../../availability-zones/az-region.md#azure-regions-with-availability-zones)で既定でゾーン冗長になっており、この機能を有効にするためにアカウントで追加構成を行う必要はありません。  Media Services は、関連付けられているストレージ アカウントにメディア データを格納します。  これらのストレージ アカウントは、Media Services アカウントと同じレベルの冗長性を提供するために、ゾーン冗長ストレージ (ZRS) または geo ゾーン冗長ストレージ (GZRS) として作成する必要があります。 関連付けられているストレージ アカウントのレプリケーションを構成する方法の詳細については、「[ストレージ アカウントがレプリケートされる方法を変更する](../../storage/common/redundancy-migration.md)」を参照してください。

## <a name="how-media-services-components-handle-an-availability-zone-fault"></a>Media Services コンポーネントで可用性ゾーンの障害を処理する方法

| コンポーネント             | 可用性ゾーンの障害の動作 |
|-----------            |----------------------|
| コントロール プレーン (Azure Resource Management) | 通常どおりに動作し続けます |
| キー配信            | 通常どおりに動作し続けます |
| ジョブ                    | ジョブは、別の可用性ゾーンで再スケジュールされます。 実行中の処理ジョブが可用性ゾーンで最初からやり直すように再スケジュールされるので、処理時間に遅れが生じます |
| ライブ イベント             | ライブ イベントへのストリーミングと取り込みは、通常どおりに動作し続けます。 可用性ゾーンの障害中には、ライブ イベントでの "reset" の呼び出しは現在サポートされていません。 "reset" 操作の代わりに、ライブ イベントを最初に停止して再起動することをお勧めします。 ゾーン ダウン イベント中にライブ イベントが "実行中" 状態に切り替わっていた場合、顧客には、ライブ イベントが "開始" 状態で停止しているように見えることがあります。 この場合は、新しいライブ イベントを開始し、ゾーンが復旧した後に "開始" ライブ イベントをクリーンアップすることをお勧めします。  |
| ストリーミング エンドポイント     | 通常どおりに動作し続けます |


## <a name="high-availability-streaming-and-encoding-for-vod"></a>VOD 用の高可用性ストリーミングとエンコード

Availability Zones は、単一のリージョンでの障害の分離を増強します。 オンデマンド ストリーミングおよびエンコードに高可用性を提供するために、他の Azure サービスを使用して、冗長性、正常性の監視、負荷分散、データのバックアップと回復などの懸念事項を扱うアーキテクチャを作成できます。 このようなアーキテクチャの 1 つを「[Media Services とビデオ オン デマンド (VOD) を使用した高可用性](architecture-high-availability-encoding-concept.md)」の記事で取り上げています。
この記事とサンプル コードを使用すれば、個々のリージョンの Media Services アカウントを使用して VOD アプリケーションの高可用性アーキテクチャを作成する方法がわかります。

## <a name="media-services-support-for-availability-zones-by-region"></a>リージョン別の Availability Zones に対する Media Services のサポート

Availability Zones は現在、特定の Azure リージョンでのみサポートされます。 Availability Zones リージョンのサポートの詳細については、「[Azure のリージョンと Availability Zones](../../availability-zones/az-region.md#azure-regions-with-availability-zones)」を参照してください

## <a name="further-reading"></a>参考資料

Availability Zones の詳細については、「[Azure のリージョンと Availability Zones](../../availability-zones/az-overview.md)」を参照してください。

高可用性のエンコードとストリーミングの詳細については、「[Media Services とビデオ オン デマンド (VOD) を使用した高可用性](architecture-high-availability-encoding-concept.md)」を参照してください。

Availability Zones をサポートするようにストレージ アカウントのレプリケーションを適切に構成する方法については、「[ストレージ アカウントがレプリケートされる方法を変更する](../../storage/common/redundancy-migration.md)」を参照してください。
