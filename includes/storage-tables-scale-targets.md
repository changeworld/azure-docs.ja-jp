---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5b7aa618df63bf813e0ab0d77025cf57cfe55c3a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964572"
---
| リソース | ターゲット |
|----------|---------------|
| 1 つのテーブルの最大サイズ | 500 TiB |
| テーブル エンティティの最大サイズ | 1 MiB |
| テーブル エンティティの最大プロパティ数 | 255 (PartitionKey、RowKey、および Timestamp の 3 つのシステム プロパティも含まれます) |
| テーブルごとの保存されるアクセス ポリシーの最大数 | 5 |
| ストレージ アカウントあたりの最大要求レート | 毎秒 20,000 トランザクション (エンティティ サイズは 1 KiB を想定) |
| 単一テーブル パーティションのターゲット スループット (1 KiB のエンティティ) | 毎秒最大 2000 エンティティ |
