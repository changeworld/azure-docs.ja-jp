---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: 86b2353109d4005594ee08e27283d6aa9c654120
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078315"
---
次の表では、Azure Data Explorer クラスターの上限について説明します。

| リソース | 制限 |
| --- | --- |
| サブスクリプションあたりのリージョンごとのクラスター数 | 20 |
| クラスターごとのインスタンス数 | 1000 | 
| クラスター内のデータベースの数 | 10,000 |
| リーダー クラスター (データ共有プロデューサー) あたりのフォロー クラスター (データ共有コンシューマー) の数 | 100 |

次の表では、Azure Data Explorer クラスターで実行される管理操作の制限について説明します。

| Scope | Operation | 制限 |
| --- | --- | --- |
| クラスター | 読み取り (クラスターの取得など) | 5 分あたり 500 |
| クラスター | 書き込み (データベースの作成など) | 1 時間あたり 1000 |

