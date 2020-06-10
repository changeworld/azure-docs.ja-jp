---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996799"
---
Azure portal に移動し、<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="新しい Form Recognizer リソースを作成します" target="_blank">新しい Form Recognizer リソースを作成します <span class="docon docon-navigate-external x-hidden-focus"></span></a>。 **[作成]** ウィンドウには以下の情報が表示されます。

|    |    |
|--|--|
| **名前** | リソースのわかりやすい名前。 わかりやすい名前 (*MyNameFaceAPIAccount* など) を使用することをお勧めします。 |
| **サブスクリプション** | アクセスが許可されている Azure サブスクリプションを選択します。 |
| **場所** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
| **価格レベル** | リソースのコストは、選択した価格レベルと使用量に依存します。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
| **リソース グループ** | リソースを含む [Azure リソース グループ](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |

> [!NOTE]
> 通常、Azure portal で Cognitive Service リソースを作成するときに、マルチ サービスのサブスクリプション キー (複数の Cognitive Services で使用) または 単一サービスのサブスクリプション キー (特定の Cognitive Services でのみ使用) を作成するオプションがあります。 ただし、Form Recognizer はプレビュー リリースなので、複数のサービス サブスクリプションに含まれず、ウェルカム メールに記載されているリンクを使用しない限り、単一サービスのサブスクリプションを作成できません。

Form Recognizer リソースがデプロイが完了すると、ポータルの **[すべてのリソース]** の一覧からこれを検索して選択します。 キーとエンドポイントは、リソースの [key and endpoint]\(キーとエンドポイント\) ページの [リソース管理] にあります。 これらの両方を一時的な場所に保存してから、先に進んでください。
