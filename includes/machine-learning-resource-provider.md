---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "75893967"
---
Azure Machine Learning ワークスペース (またはワークスペースで使用されるリソース) を作成しようとすると、次のメッセージに似たエラーが表示されることがあります。

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

ほとんどのリソースプロバイダーは自動的に登録されますが、すべてではありません。 このメッセージが表示された場合は、言及されているプロバイダーを登録する必要があります。

リソースプロバイダーの登録については、「[リソースプロバイダー登録エラーの解決](../articles/azure-resource-manager/templates/error-register-resource-provider.md)」を参照してください。