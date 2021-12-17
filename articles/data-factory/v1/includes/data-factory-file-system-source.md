---
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: include
ms.date: 10/22/2021
ms.author: jingwang
ms.openlocfilehash: dd219214a6c1a50befe2064110dc804e2acc6be0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242797"
---
コピー アクティビティで、source が **FileSystemSource** 型の場合は、typeProperties セクションで次のプロパティを使用できます:

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |

