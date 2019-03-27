---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752854"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors リソースを作成する

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> に移動します。

2. Azure portal の左側のメニューから **[リソースの作成]** を選択します。

3. 検索バーで「Spatial Anchors」を検索します。

   ![Spatial Anchors の検索](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. **[Spatial Anchors (プレビュー)]** を選択してダイアログを開き、**[作成]** を選択します。

5. **[Spatial Anchors アカウント]** フォームで以下を行います。

   1. 一意のリソース名を指定します。
   2. リソースをアタッチするサブスクリプションを選択します。
   3. **[新規作成]** を選択してリソース グループを作成し、リソース グループに **[myResourceGroup]** という名前を付け、**[OK]** を選択します。
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. リソースが配置される場所 (リージョン) を選択します。
   5. **[新規]** を選択して、リソースの作成を開始します。

   ![リソースの作成](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. リソースの作成が正常に完了したら、リソースのプロパティを表示できます。 後で必要なので、リソースの **[アカウント ID]** 値をメモしておきます。

   ![リソースのプロパティの表示](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. **[設定]** の下の **[キー]** を選択し、**[プライマリ キー]** 値をメモしておきます。 この値は `Account Key` であり、後で使用されます。

   ![アカウント キーの表示](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
