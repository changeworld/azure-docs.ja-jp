---
title: インクルード ファイル description: インクルード ファイル services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 05/10/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

次の表に、Azure Event Hubs の特定のサービス レベルで使用できる (または使用できない) 機能の一覧を示します。 

| 機能 | Basic |  Standard | Premium | 専用 |
| ------- | ------| -------- | ------- | --------- |
| テナント | マルチテナント | マルチテナント | リソース分離を使用したマルチテナント | 排他シングル テナント |
| プライベート リンク | 該当なし | はい | はい | Yes |
| カスタマー マネージド キー <br/>(Bring Your Own Key) | 該当なし | 該当なし | はい | はい |
| キャプチャ | なし | 個別に価格設定 | Included | Included |
| 動的パーティション スケールアウト | 該当なし | 該当なし | はい | Yes |
| イングレス イベント | 100 万イベントごとの課金 | 100 万イベントごとの課金 | Included | Included

> [!NOTE]
> 表中の「**含まれる (Included)** 」は、その機能が使用可能であり、使用しても料金が別途発生しないことを意味します。 







 