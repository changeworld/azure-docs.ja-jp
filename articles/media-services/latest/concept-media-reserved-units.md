---
title: メディア占有ユニット - Azure
description: メディア占有ユニットを使用すると、メディア プロセスをスケーリングし、メディア処理タスクの速度を決定することができます。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 3fc68505712bc5cae0defd216b30a3c5913c3c77
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866438"
---
# <a name="media-reserved-units"></a>メディア占有ユニット

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

以前は Azure Media Services v2 のメディア占有ユニット (MRU) を使用して、エンコードの同時実行性とパフォーマンスを制御していました。 システムが負荷に基づいて自動的にスケール アップおよびスケール ダウンするため、メディア サービス アカウントの MRU 管理や、クォータの増加要求を行う必要がなくなりました。 また、MRU 使用時と比較して、パフォーマンスが同等、または向上しているのもあります。 

2020-05-01 API より前のバージョンで作成されたアカウントがある場合でも、MRU を管理するため API にアクセスできます。ただし、設定した MRU 構成は、エンコードの同時実行やパフォーマンス制御には使用されません。 Azure portal で MRU を管理するオプションが表示されない場合は、2020-05-01 API 以降で作成されたアカウントを使用しています。 

## <a name="billing"></a>課金

以前はメディア占有ユニットに対して料金が発生しましたが、2021 年 4 月 17 日時点では、メディア占有ユニット用に構成されているアカウントには料金がかかりません。 エンコード ジョブの課金の詳細は、[Media Services を使用したビデオとオーディオのエンコード](encoding-concept.md)に関する記述を参照してください

**2020-05-01** バージョンの API (v3 バージョンなど) または Azure portal を使用して作成されたアカウントの場合、スケーリングとメディア占有ユニットは不要になりました。 スケーリングはサービスによって自動的に内部処理されます。 Azure Media Services アカウントでは、メディア占有ユニットが不要になり、サポートされなくなりました。 その他の情報については、「[メディア占有ユニット (レガシ)](concept-media-reserved-units.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [Media Services v2 から v3 への移行](migrate-v-2-v-3-migration-introduction.md)
* [CLI を使用してメディア占有ユニットをスケーリングする](media-reserved-units-cli-how-to.md)
