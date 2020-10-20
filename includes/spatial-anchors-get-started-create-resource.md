---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b83951cd743afc7e249e3ee37182007bbe40cb5c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971297"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors リソースを作成する

<a href="https://portal.azure.com" target="_blank">Azure ポータル</a>にアクセスします。

左側のウィンドウで、 **[リソースの作成]** を選択します。

検索ボックスを使用して、「**Spatial Anchors**」を検索します。

![「Spatial Anchors」の検索結果を示すスクリーンショット。](./media/spatial-anchors-get-started-create-resource/portal-search.png)

**[Spatial Anchors]** を選択し、 **[作成]** を選択します。

**[Spatial Anchors アカウント]** ウィンドウで次を行います。

* 通常の英数字を使用して一意のリソース名を入力します。  
* リソースをアタッチするサブスクリプションを選択します。  
* **[新規作成]** を選択して、リソース グループを作成します。 「**myResourceGroup**」と名前を付け、 **[OK]** を選択します。  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* リソースを配置する場所 (リージョン) を選択します。  
* **[新規]** を選択して、リソースの作成を開始します。

![リソースを作成するための [Spatial Anchors] ウィンドウのスクリーンショット。](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

リソースが作成されると、Azure portal に、デプロイが完了したことが表示されます。 
   
![リソースのデプロイが完了したことを示すスクリーンショット。](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

**[リソースに移動]** を選択します。 これでリソースのプロパティを表示できます。 
   
リソースの **[アカウント ID]** 値は後で使用するためにテキスト エディターにコピーしておきます。

![[リソース プロパティ] ウィンドウのスクリーンショット。](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

また、リソースの **[アカウント ドメイン]** 値を後で使用するためにテキスト エディターにコピーします。

![リソースのアカウント ドメイン値を示すスクリーンショット。](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

**[設定]** で **[キー]** を選択します。 **[主キー]** 値の **[アカウント キー]** を後で使用するためにテキスト エディターにコピーします。

![アカウントの [キー] ウィンドウのスクリーンショット。](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
