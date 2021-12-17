---
title: マネージド オンライン エンドポイント VM SKU の一覧 (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) に使用できる VM SKU を示します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: laobri
ms.author: seramasu
author: rsethur
ms.custom: devplatv2
ms.date: 05/10/2021
ms.openlocfilehash: 0081f43ba5574469646dd41267ce2a12a33afe4a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520125"
---
# <a name="managed-online-endpoints-sku-list-preview"></a>マネージド オンライン エンドポイント SKU の一覧 (プレビュー)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

次の表は、Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) としてサポートされる VM SKU を示しています。

* デプロイに使用する `instance_type` 属性は、"Standard_F4s_v2" という形式で指定する必要があります。 次の表に、F2s v2 などのインスタンス名を示します。 これらの名前は、デプロイを作成および更新する Azure CLI または Azure Resource Manager テンプレート (ARM テンプレート) 要求に対して、指定された形式 (`Standard_{name}`) で配置する必要があります。 

* CPU や RAM などの構成の詳細については、「[Azure Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)」を参照してください。

| サイズ | General Purpose | コンピューティング最適化 | メモリ最適化 | GPU |
| --- | --- | --- | --- | --- | --- | 
| V.Small | DS2 v2 | F2s v2 | E2s v3 | NC4as_T4_v3 |
| Small | DS3 v2 | F4s v2 |  E4s v3 | NC6s v2 <br/> NC6s v3 <br/> NC8as_T4_v3 |
| Medium | DS4 v2 | F8s v2 | E8s v3 | NC12s v2 <br/> NC12s v3 <br/> NC16as_T4_v3 |
| Large | DS5 v2 | F16s v2 | E16s v3 | NC24s v2 <br/> NC24s v3 <br/> NC64as_T4_v3 |
| X-Large| - | F32s v2 <br/> F48s v2 <br/> F64s v2 <br/> F72s v2 | E32s v3 <br/> E48s v3 <br/> E64s v3 | - |


