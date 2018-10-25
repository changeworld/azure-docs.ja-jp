---
title: Azure Portal で Cognitive Services API アカウントを作成する
titlesuffix: Azure Cognitive Services
description: Azure Portal で Microsoft Cognitive Services API アカウントを作成する方法。
services: cognitive-services
author: garyericson
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 37f53303a3b0c224c1286fb488a796fd5cdee0e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386418"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>クイック スタート: Azure portal で Cognitive Services アカウントを作成する

このクイック スタートを使用して、Azure Cognitive Services の使用を開始します。 これらのサービスは Azure [リソース](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)によって表され、利用できる多数の Cognitive Services API の中の 1 つまたは複数の API に接続できます。

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション。 無料で[アカウントを作成](https://azure.microsoft.com/free/)できます。

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Azure Cognitive Services リソースを作成してサブスクライブする

1. [Azure portal](http://portal.azure.com) にサインインし、**[+リソースの作成]** をクリックします。
    
    ![Cognitive Services API の選択](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. [Azure Marketplace] で **[AI + 機械学習]** を選択します。 関心があるサービスが見つからない場合は、**[すべて]** をクリックして、Cognitive Services API のカタログ全体を表示します。

    ![Cognitive Services API の選択](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. **[作成]** ページで、次の情報を指定します。

    |    |    |
    |--|--|
    | **名前** | Cognitive Sservices リソースのわかりやすい名前。 わかりやすい名前を使用することをお勧します (*MyNameFaceAPIAccount* など)。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **場所** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
    | **価格レベル** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含む [Azure リソース グループ](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>リソースにアクセスする 

> [!NOTE]
> サブスクリプションの所有者は、リソース グループとサブスクリプション用の Cognitive Services アカウントの作成を無効にすることができます。これを行うには、[Azure ポリシー](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)を適用し、[許可されていないリソースの種類] ポリシー定義を割り当て、ターゲットのリソースの種類として **Microsoft.CognitiveServices/accounts** を指定します。

リソースを作成した後、Azure ダッシュ ボードにピン留めした場合は、そこからアクセスできます。 それ以外の場合は、**[リソース グループ]** 内で検索できます。

Cognitive Services リソースの中で、**[概要]** セクションのエンドポイント URL とキーを使用して、アプリケーションでの API の呼び出しを開始できます。

![リソース画面](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Computer Vision API の C# のチュートリアル](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>関連項目

* [クイック スタート: 画像から手書きのテキストを抽出する](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [チュートリアル: 画像の中にある顔を検出してフレームに収めるアプリを作成する](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [カスタム検索 Web ページを作成する](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Bot Framework を使用して Language Understanding (LUIS) とボットを統合する ](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
