---
title: Azure Media Services V2 と V3 の間の機能のギャップ
description: この記事では、Azure Media Services V2 と V3 の間の機能のギャップについて説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 564f3127fc6901695890daa520152a7aa1a2337f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962970"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Azure Media Services V2 と V3 の間の機能のギャップ

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-2.svg)

移行ガイダンスのこの部分では、V2 と V3 API の違いに関する詳細な情報を提供します。

## <a name="feature-gaps-between-v2-and-v3-apis"></a>V2 と V3 API の間の機能のギャップ

V3 API には、V2 API との間に次の機能のギャップがあります。 現在、V2 API の Media Encoder Standard にある高度な機能の一部は V3 では利用できません。

- Azure Media Player では必要なくなったため、入力に音声が含まれていない場合、無音オーディオ トラックを挿入します。

- 入力に映像が含まれていない場合、ビデオ トラックを挿入します。

- 現在、コード変換を使用するライブ イベントでは、ストリーム中のスレート挿入および API 呼び出しによる広告マーカー挿入はサポートされていません。

- Azure Media Premium Encoder は、V2 でサポートされなくなりました。 8 ビット HEVC エンコードに使用している場合は、Standard Encoder の新しい HEVC サポートを使用してください。 
    - Standard Encoder へのオーディオ チャネル マッピングのサポートが追加されました。  [Media Services Encoding Swagger ドキュメントの Audio](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) を参照してください。
    - MXF や ProRes などのサードパーティ ライセンス製品の高度な機能または出力形式を使用していた場合は、Telestream の Azure パートナー ソリューションをご利用ください。これは、V2 の廃止時までにトランザクション対応になります。 または、Imagine Communications や [Bitmovin](http://bitmovin.com) を使用できます。

- V2 のストリーミング エンドポイントの "可用性セット" プロパティはサポートされなくなりました。 V3 API での[高可用性 VOD](./architecture-high-availability-encoding-concept.md) 配信に関するサンプル プロジェクトとガイダンスを参照してください。

- Media Services V3 では、FairPlay IV を指定できません。 パッケージ化とライセンス配信の両方で Media Services を使用するお客様には影響しませんが、サードパーティの DRM システムを使用して FairPlay ライセンス (ハイブリッド モード) を配信すると、問題になるおそれがあります。

- 保存されている資産を保護するためのクライアント側のストレージ暗号化は、V3 API では削除され、保存データのストレージサービス暗号化に置き換えられました。 V3 API は、既存のストレージの暗号化された資産では引き続き動作しますが、新しく作成することはできません。

## <a name="terminology-and-entity-changes"></a>用語とエンティティの変更

API に対するその他の変更については、[用語とエンティティ](migrate-v-2-v-3-differences-terminology.md)の変更に関する記事を参照してください。
