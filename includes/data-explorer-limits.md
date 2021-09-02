---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: f9143f3220ad5f90345436c3c1f97419e4d26fc4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114594643"
---
次の表では、Azure Data Explorer クラスターの上限について説明します。

| リソース | 制限 |
| --- | --- |
| サブスクリプションあたりのリージョンごとのクラスター数 | 20 |
| クラスターごとのインスタンス数 | 1000 | 
| クラスター内のデータベースの数 | 10,000 |
| リーダー クラスター (データ共有プロデューサー) あたりのフォロー クラスター (データ共有コンシューマー) の数 | 70 |

次の表では、Azure Data Explorer クラスターで実行される管理操作の制限について説明します。

| Scope | Operation | 制限 |
| --- | --- | --- |
| クラスター | 読み取り (クラスターの取得など) | 5 分あたり 500 |
| クラスター | 書き込み (データベースの作成など) | 1 時間あたり 1000 |

