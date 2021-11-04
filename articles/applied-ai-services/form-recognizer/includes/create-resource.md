---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f4a863cb0c3ecf087d5efdcde37ce907a7a7d41
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021708"
---
Azure portal に移動し、<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="新しい Form Recognizer リソースを作成します" target="_blank">新しい Form Recognizer リソースを作成します </a>。 **[作成]** ウィンドウには以下の情報が表示されます。

| プロジェクトの詳細   | 説明   |
|--|--|
| **サブスクリプション** | アクセスが許可されている Azure サブスクリプションを選択します。 |
| **リソース グループ** | ご利用のリソースを含んだ Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |
| **リージョン** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
| **名前** | リソースのわかりやすい名前。 わかりやすい名前 (*MyNameFaceAPIAccount* など) を使用することをお勧めします。 |
| **価格レベル** | リソースのコストは、選択した価格レベルと使用量に依存します。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。

## <a name="retrieve-the-key-and-endpoint"></a>キーとエンドポイントを取得する

Form Recognizer リソースがデプロイが完了すると、ポータルの **[すべてのリソース]** の一覧からこれを検索して選択します。 キーとエンドポイントは、リソースの **[キーとエンドポイント]** ページの **[リソース管理]** に配置されます。 これらの両方を一時的な場所に保存してから、先に進んでください。
