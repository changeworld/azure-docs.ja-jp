---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181594"
---
Azure Resource Manager を使用すると、テンプレートをデプロイするときに使用する値のパラメーターを定義できます。 テンプレートには、すべてのパラメーター値を含む `parameters` セクションが含まれます。 各パラメーター値は、デプロイするリソースを定義するためにテンプレートによって使用されます。

> [!NOTE]
> 常に同じ値に対してはパラメーターを定義しないでください。 デプロイするプロジェクトまたはデプロイ先の環境に基づいて、異なる値に対してのみパラメーターを定義します。

パラメーターを定義する場合:

* デプロイ中にユーザーが指定できる値を指定するには、**allowedValues** フィールドを使用します。

* デプロイ中に値が指定されなかった場合に、パラメーターに既定値を割り当てるには、**defaultValue** フィールドを使用します。 
