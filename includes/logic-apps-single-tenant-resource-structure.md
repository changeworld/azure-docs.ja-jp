---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 05/25/2021
ms.openlocfilehash: 88d0292048edcc6846d0e1637b79f115d1321e70
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369134"
---
マルチテナント モデルでは、ロジック アプリのリソース構造には、1 つのワークフローのみを含めることができます。 この一対一の関係により、多くの場合、ロジック アプリとワークフローの双方が同義と見なされ、参照されます。 ただし、シングルテナント モデルでは、ロジック アプリのリソース構造に複数のワークフローを含めることができます。 この一対多の関係は、同じロジック アプリ内でワークフローが他のリソースを共有し、再利用できることを意味します。 同じロジック アプリおよびテナント内のワークフローでも、この共有テナントと相互の近接性により、パフォーマンスが向上します。 このリソース構造は、1 つの関数アプリが多数の関数をホストできる Azure Functions と、見かけと動作が似ています。

ロジック アプリでのワークフローの整理、パフォーマンス、スケーリングに関する詳細とベスト プラクティスについては、一般的にシングルテナント Azure Logic Apps に適用できる、類似の [Azure Functions に関するガイダンス](../articles/azure-functions/functions-best-practices.md)を参照してください。