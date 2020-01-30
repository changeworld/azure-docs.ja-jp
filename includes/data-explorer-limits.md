---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: d9e23e74cd980ecf44cd04ad2b6f89dc6a4cf868
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76548215"
---
次の表では、Azure Data Explorer クラスターの上限について説明します。

| リソース | 制限 |
| --- | --- |
| サブスクリプションあたりのリージョンごとのクラスター数 | 20 |
| クラスターごとのインスタンス数 | 1000 | 
| クラスター内のデータベースの数 | 10,000 |
| クラスター内のアタッチされたデータベース構成の数 | 70 |

次の表では、Azure Data Explorer クラスターで実行される管理操作の制限について説明します。

| スコープ | 操作 | 制限 |
| --- | --- | --- |
| クラスター | 読み取り (クラスターの取得など) | 5 分あたり 500 |
| クラスター | 書き込み (データベースの作成など) | 1 時間あたり 1000 |

