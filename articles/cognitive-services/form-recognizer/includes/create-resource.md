---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: dce9b4ffa05b48fc1789859328b221ac1a0f3a27
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594445"
---
Form Recognizer を使用するためのアクセスが認められている場合、複数のリンクおよびリソースを含むウェルカム メールを受信します。 そのメッセージ内の "Azure portal" リンクを使用して、Azure portal を開き、Form Recognizer リソースを作成します。 **[作成]** ウィンドウには以下の情報が表示されます。

|    |    |
|--|--|
| **Name** | リソースのわかりやすい名前。 わかりやすい名前 (*MyNameFaceAPIAccount* など) を使用することをお勧めします。 |
| **サブスクリプション** | アクセスが許可されている Azure サブスクリプションを選択します。 |
| **Location** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
| **[価格レベル]** | リソースのコストは、選択した価格レベルと使用量に依存します。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
| **リソース グループ** | リソースを含む [Azure リソース グループ](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |

> [!IMPORTANT]
> 通常、Azure portal で Cognitive Service リソースを作成するときに、マルチ サービスのサブスクリプション キー (複数の Cognitive Services で使用) または 単一サービスのサブスクリプション キー (特定の Cognitive Services でのみ使用) を作成するオプションがあります。 ただし、Form Recognizer はプレビュー リリースなので、複数のサービス サブスクリプションに含まれず、ウェルカム メールに記載されているリンクを使用しない限り、単一サービスのサブスクリプションを作成できません。

Form Recognizer リソースがデプロイが完了すると、ポータルの **[すべてのリソース]** の一覧からこれを検索して選択します。 続いて、 **[キー]** タブを選択してサブスクリプション キーを表示します。 どちらのキーも、リソースへのアクセス権をアプリに与えます。 **KEY 1** の値をコピーします。