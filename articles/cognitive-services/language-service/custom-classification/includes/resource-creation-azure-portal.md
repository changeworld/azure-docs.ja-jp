---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 93ee37d6ef607a65f5b8be68d71c46fc258387f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090013"
---
### <a name="create-a-new-resource-from-the-azure-portal"></a>Azure portal から新しいリソースを作成します

[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) に移動し、新しい Azure 言語リソースを作成します。 追加機能の選択を求められた場合は、 **[この手順をスキップする]** を選びます。 リソースを作るときは、次のパラメーターを指定します。  

|要件  |必須の値  |
|---------|---------|
|Location | "米国西部 2" または "西ヨーロッパ"         |
|Pricing tier     | Standard (**S**) 価格レベル        |

> [!IMPORTANT]
> **[Custom Named Entity Recognition (NER) & Custom Classification (Preview)]\(カスタム固有表現認識 (NER) とカスタム分類 (プレビュー)\)** セクションで、既存のストレージ アカウントを選ぶか、または新しく作ります。 カスタム テキスト分類を使うには、ストレージ アカウントが必要です。 ストレージ アカウントは後でも指定できますが、今のうちに指定する方が簡単です。 
