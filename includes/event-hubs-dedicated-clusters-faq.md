---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516998"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>クラスターでは何ができますか?

Event Hubs クラスターの場合、どれだけの容量を取り込んでストリーミングできるかは、プロデューサー、コンシューマー、取り込みや処理の速度などのさまざまな要因によって異なります。 

次の表は、弊社でのテスト中に実現したベンチマーク結果を示しています。

| ペイロードの形態 | 受信者 | イングレス帯域幅| イングレス メッセージ | エグレス帯域幅 | エグレス メッセージ | 合計 TU 数 | CU あたりの TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB のバッチ | 2 | 400 MB/秒 | 400,000 メッセージ/秒 | 800 MB/秒 | 800,000 メッセージ/秒 | 400 TU | 100 TU | 
| 10x10KB のバッチ | 2 | 666 MB/秒 | 66,600 メッセージ/秒 | 1.33 GB/秒 | 133,000 メッセージ/秒 | 666 TU | 166 TU |
| 6x32KB のバッチ | 1 | 1.05 GB/秒 | 34,000 メッセージ/秒 | 1.05 GB/秒 | 34,000 メッセージ/秒 | 1000 TU | 250 TU |

このテストでは、次の条件が使用されました。

- 4 容量ユニット (CU) を持つ専用レベルの Event Hubs クラスターが使用されました。 
- 取り込みに使用されたイベント ハブには 200 パーティションが存在しました。 
- 取り込まれたデータは、すべてのパーティションから受信している 2 つの受信側アプリケーションによって受信されました。

### <a name="can-i-scale-updown-my-cluster"></a>クラスターをスケールアップ/ダウンできますか?

作成後、クラスターは 4 時間以上の使用に対して課金されます。 セルフサービス エクスペリエンスのプレビュー リリースでは、 **[技術]**  >  **[クォータ]**  >  **[Request to Scale Up or Scale Down Dedicated Cluster]\(Dedicated Cluster のスケールアップまたはスケールダウンを要求する\)** でクラスターをスケールアップまたはスケールダウンする [サポート リクエスト](https://ms.portal.azure.com/#create/Microsoft.Support)を Event Hubs チームに送ることができます。 クラスターのスケールダウンが要求するまで、最大で 7 日かかる場合があります。 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>geo DR はクラスターでどのように機能しますか?

Dedicated レベルのクラスターの名前空間を、Dedicated レベルのクラスターの別の名前空間と、geo ペアリングすることができます。 Dedicated レベルの名前空間と Standard オファリングの名前空間のペアリングは、スループットの制限に互換性がないためエラーが発生するので、お勧めしません。 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Standard の名前空間を Dedicated レベルのクラスターに属するように移行できますか?
現在、Standard の名前空間から Dedicated にイベント ハブ データを移行するための自動移行プロセスはサポートされていません。 
