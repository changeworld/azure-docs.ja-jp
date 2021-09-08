---
title: メディア占有ユニットの概要 | Microsoft Docs
description: この記事では、Azure Media Services を使用したメディア処理のスケール設定の概要を示します。
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 19454d90b55ab92c92d2e9132198f918f38c6e2c
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835384"
---
# <a name="media-reserved-units"></a>メディア占有ユニット

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

従来、メディア占有ユニット (MRU) は、エンコードの同時実行性やパフォーマンスを制御するために使用されていました。 MRU は、間もなく非推奨となる次のレガシ メディア プロセッサのみで使用されます。 これらのレガシー プロセッサのサポート停止情報については、「[Azure Media Services レガシー コンポーネント](legacy-components.md)」を参照してください。

* メディア エンコーダー Premium ワークフロー
* Media Indexer V1 と V2

その他のメディア プロセッサについては、負荷に応じてシステムが自動的にスケール アップ、スケール ダウンするため、MRU の管理やメディア サービス アカウントのクォータ増加要求は不要です。 また、MRU 使用時と比較して、パフォーマンスが同等、または向上しているのもあります。

## <a name="billing"></a>課金

以前はメディア占有ユニットに対して料金が発生しましたが、2021 年 4 月 17 日時点では、メディア占有ユニット用に構成されているアカウントには料金がかかりません。

## <a name="scaling-mrus"></a>MRU のスケーリング

互換性を確保するために、引き続き次の Azure portal または次の API を使用して、MRU を管理、スケーリングできます。

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

ただし既定では、エンコードのコンカレンシーやパフォーマンスを制御するために設定した MRU 構成は使用されません。 この構成の唯一の例外は、次のいずれかのレガシ メディア プロセッサを使用してエンコードする場合です: Media Encoder Premium Workflow または Media Indexer V1。  
