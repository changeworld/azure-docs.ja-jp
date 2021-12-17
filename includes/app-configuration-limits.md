---
author: tfitzmac
ms.service: azure-app-configuration
ms.topic: include
ms.date: 08/19/2021
ms.author: tomfitz
ms.openlocfilehash: 3b04007ef7329231da7de31ec465de2c1585c7e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122752764"
---
| リソース | 制限 | 解説 |
| --- | --- | ---|
| Free レベルの構成ストア | サブスクリプションあたり 1 つのストア |
| Standard レベルの構成ストア | サブスクリプションあたり無制限のストア | 
| Free レベルの構成ストア要求 | 1 日あたり 1,000 個の要求  | クォータを使い果たした後は、その日の終わりまで、すべての要求に対して HTTP 状態コード 429 が返されます |
| Standard レベルの構成ストア要求 | 1 時間あたり 30,000 件  |クォータを使い果たした後は、1 時間が終わるまで、要求数が多すぎることを示す HTTP 状態コード 429 が要求に対して返される場合があります。|  
| Free レベルのストレージ | 10 MB |
| Standard レベルのストレージ | 1 GB |
| キーと値 | 10 KB  | 単一のキーと値項目に対して (すべてのメタデータを含む)
