---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/29/2019
ms.author: tamram
ms.openlocfilehash: 6bbd2a421122a05399d35be1b61e65f505253ed2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420750"
---
| Resource | ターゲット |
|----------|---------------|
| 1 つのテーブルの最大サイズ | 500 TiB |
| テーブル エンティティの最大サイズ | 1 MiB |
| テーブル エンティティの最大プロパティ数 | 255。次の 3 つのシステム プロパティが含まれます。PartitionKey、RowKey、Timestamp |
| エンティティ内のプロパティ値の最大合計サイズ | 1 MiB |
| テーブルあたりの保存されるアクセス ポリシーの最大数 | 5 |
| ストレージ アカウントあたりの最大要求レート | 毎秒 20,000 トランザクション (エンティティ サイズは 1 KiB を想定) |
| 1 つのテーブル パーティションのターゲット スループット (1 KiB のエンティティ) | 毎秒最大 2,000 エンティティ |
