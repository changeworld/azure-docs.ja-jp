---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: f1024edf7d05d47ba14478a1561c29f7859a245c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322891"
---
Azure portal に移動し、<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="新しい Form Recognizer リソースを作成します" target="_blank">新しい Form Recognizer リソースを作成します </a>。 **[作成]** ウィンドウには以下の情報が表示されます。

| プロジェクトの詳細   | 説明   |
|--|--|
| **サブスクリプション** | アクセスが許可されている Azure サブスクリプションを選択します。 |
| **リソース グループ** | リソースを含む [Azure リソース グループ](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |
| **リージョン** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
| **名前** | リソースのわかりやすい名前。 わかりやすい名前 (*MyNameFaceAPIAccount* など) を使用することをお勧めします。 |
| **価格レベル** | リソースのコストは、選択した価格レベルと使用量に依存します。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。

> [!NOTE]
> Azure portal で Cognitive Service リソースを作成するときに、マルチ サービスのサブスクリプション キー (複数の Cognitive Services で使用) または単一サービスのサブスクリプション キー (特定の Cognitive Services でのみ使用) を作成するオプションがあります。 現在、マルチサービス サブスクリプションには、Form Recognizer は含まれていません。

## <a name="retrieve-the-key-and-endpoint"></a>キーとエンドポイントを取得する

Form Recognizer リソースがデプロイが完了すると、ポータルの **[すべてのリソース]** の一覧からこれを検索して選択します。 キーとエンドポイントは、リソースの **[キーとエンドポイント]** ページの **[リソース管理]** に配置されます。 これらの両方を一時的な場所に保存してから、先に進んでください。

