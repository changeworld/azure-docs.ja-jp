---
title: メディア占有ユニット (MRU) の移行ガイダンス
description: この記事では、Azure Media Services V2 から V3 への移行に役立つ MRU シナリオ ベースのガイダンスを示します。
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 98fd879cb8d960f2f688b3aa002fa86832981744
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353060"
---
# <a name="media-reserved-units-migration-guidance"></a>メディア占有ユニットの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

この記事では、Azure Media Services V2 から V3 への移行に役立つ MRU シナリオ ベースのガイダンスを示します。

> [!Important]
> あらゆる Media Services アカウントでメディア占有ユニットが不要になりました。負荷に基づいてシステムによって自動的にスケールアップとスケールダウンが行われるためです。 

## <a name="scenario-guidance"></a>シナリオのガイダンス

次のシナリオに基づき MRU を移行してください。

* すべての Media Services アカウントについて、メディア占有ユニット (MRU) を設定する必要がなくなりました。 これで、負荷に基づいて、システムが自動的にスケールアップおよびスケールダウンされます。
* API の 2020-05-01 (以降) バージョンの前にアカウントを作成した場合でも、MRU を管理するため API にアクセスできます。ただし、設定した MRU 構成は、エンコードのコンカレンシーやパフォーマンス制御には使用されません。 詳細については、[メディア処理のスケール設定](../previous/media-services-scale-media-processing-overview.md)に関するページを参照してください。 MRU の管理には、CLI 2.0 for Media Services V3、または Azure portal を使用できます。
* Azure portal で MRU を管理するオプションが表示されない場合は、2020-05-01 API 以降で作成されたアカウントを実行しています。
* MRU の種類を S3 に設定していた場合、MRU がなくなったことでパフォーマンスは向上するか、変わりません。
* 既存の V2 のお客様は、移行が完了する前に、既存のアプリケーションをサポートするために新しい V3 アカウントを作成する必要があります。 
* 完全に非推奨とされているインデクサー V1 またはその他のメディア プロセッサは、再度有効にする必要がある場合があります。 

MRU の詳細については、「[メディア占有ユニット](concept-media-reserved-units.md)」と「[メディア占有ユニットをスケーリングする方法](media-reserved-units-cli-how-to.md)」を参照してください。

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU の概念、チュートリアル、および方法に関するガイド

### <a name="concepts"></a>概念

[メディア占有ユニット](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>ハウツー ガイド

[メディア占有ユニットをスケーリングする方法](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>サンプル

[V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。
