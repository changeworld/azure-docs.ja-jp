---
title: 期間限定試用版プロジェクトを Azure に移行する
titlesuffix: Azure Cognitive Services
description: 期間限定試用版プロジェクトを Azure に移行する方法について説明します。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821311"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>CustomVision.ai サイトを使用して期間限定試用版プロジェクトを Azure に移行する方法


Custom Vision Service が [Azure プレビュー](https://azure.microsoft.com/services/preview/)の段階になったため、Azure の外部での期間限定試用版プロジェクトのサポートは終了します。 このドキュメントでは、[Custom Vision Web サイト](https://customvision.ai)を使用して、期間限定試用版プロジェクトを移行して Azure リソースに関連付ける方法について説明します。 

> [!NOTE]
> Custom Vision プロジェクトは、Azure リソースに移行されると、その Azure リソースの基になる[アクセス許可]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)を継承します。 組織内の他のユーザーが、プロジェクトが属している Azure リソースの所有者である場合、それらのユーザーは [Custom Vision Web サイト](https://customvision.ai)でそのプロジェクトにアクセスできます。 同様に、リソースを削除すると、そのプロジェクトも削除されます。  


Azure のサブスクリプションやリソースの概念の概要については、[Azure 開発者向けガイド](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)を参照してください。


## <a name="prerequisites"></a>前提条件

[Custom Vision Web サイト](https://customvision.ai)にログインするために使用するのと同じ Microsoft アカウントまたは Azure Active Directory (AAD) アカウントに関連付けられた有効な Azure サブスクリプションが必要です。 

Azure アカウントを持っていない場合は、無料で[アカウントを作成](https://azure.microsoft.com/free/)します。


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure Portal で Custom Vision リソースを作成する
Azure で Custom Vision Service を使用するには、[Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) で Custom Vision Training および Prediction リソースを作成する必要があります。 

 期間限定試用版プロジェクトはすべて米国中南部でホストされるため、この [Custom Vision Web サイト](https://customvision.ai) エクスペリエンスを使用してプロジェクトを移行するには、米国中南部リージョンでリソースを作成する必要があります。 

1 つのリソースに複数のプロジェクトを関連付けることができます。 [料金と制限](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)に関する詳細情報を参照できます。 Custom Vision Service を引き続き無料で使用するには、Azure Portal で F0 レベルを選択できます。 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>期間限定試用版プロジェクトを Azure リソースに移行する

1.  Web ブラウザーで、[Custom Vision Web サイト](https://customvision.ai)に移動し、__[サインイン]__ を選択します。 Azure アカウントに移行するプロジェクトを開きます。 
2.  画面の右上隅にある歯車アイコンをクリックして、プロジェクトの [設定] ページを開きます。 

    ![プロジェクト設定は、プロジェクト ページの右上にある歯車アイコンです。](./media/move-your-project-to-azure/settings-icon.png)


3. __[Move to Azure] (Azure に移行)__ をクリックします。

    ![[Move to Azure] (Azure に移行) ボタンは、[プロジェクト設定] ページの左下にあります。](./media/move-your-project-to-azure/move-to-azure.jpg)


4. __[Move to Azure] (Azure に移行)__ ボタンのドロップダウンから、プロジェクトを移行する先の Azure リソースを選択します。 __[Move] (移行)__ をクリックします。 

5. Custom Vision Service のために前に作成した Azure リソースが表示されない場合は、別のディレクトリにある可能性があります。 プロジェクトを別のディレクトリ内のリソースに移行するには、次の手順に従います。 

    ![[プロジェクトの移行] ウィンドウ。](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>プロジェクトを別の Azure ディレクトリに移行する 

> [!NOTE]
> Azure Portal と CustomVision.ai の両方で、画面の右上隅にあるドロップダウンのユーザー メニューからディレクトリを選択できます。   


1. Azure リソースが属しているディレクトリを識別します。 Azure Portal のメニュー バーの右上にあるユーザー名の下に表示されているディレクトリを見つけることができます。 

    ![ディレクトリは Azure Portal のメニュー バーの右上にあるユーザー名の下に表示されています。 。](./media/move-your-project-to-azure/identify_directory.jpg)

2. Custom Vision Training リソースのリソース ID を見つけます。 これは、Azure Portal で Custom Vision Training リソースを開き、[リソース管理] セクションで [プロパティ] を選択することによって見つけることができます。 リソース ID はそこにあります。 

    ![リソース ID は、Azure Portal で Custom Vision Training リソースを開き、[リソース管理] セクションで [プロパティ] を選択することによって見つけます。](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. あるいは、Custom Vision Web サイトの [[設定] ページ]( https://www.customvision.ai/projects#/settings)で直接 Custom Vision リソースのリソース ID を見つけることができます。 Azure リソースが属しているのと同じディレクトリに切り替える必要があります。

    ![リソース ID は、Custom Vision Web サイトの設定ページにリソースごとに表示されています。](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. これでリソース ID が取得できたので、期間限定試用版から Azure リソースに移行しようとしている Custom Vision プロジェクトに戻ります。 そのプロジェクトを見つけるには、元のディレクトリへの切り替えが必要になる場合があることに注意してください。 [上](#move-your-limited-trial-project-to-an-azure-resource)に示されている手順に従って [プロジェクト設定] ページを開き、__[Move to Azure] (Azure に移行)__ を選択します。 


5. [Move to Azure] (Azure に移行) ウィンドウで、[Move to a different Azure directory?] (別の Azure ディレクトリに移行する) のチェックボックスをオンにします。 プロジェクトを移行する先のディレクトリを選択し、プロジェクトを移行する先のリソースのリソース ID を入力します。 __[Move] (移行)__ をクリックします。 



5. このプロジェクトは現在、別のディレクトリにあることに注意してください。 このプロジェクトを見つけるには、Custom Vision Web ポータルで、このプロジェクトが属しているのと同じディレクトリに切り替える必要があります。 Azure Portal と [Custom Vision Web サイト](https://customvision.ai)の両方で、画面の右上隅にあるドロップダウンのアカウント メニューからディレクトリを選択できます。 

## <a name="next-steps"></a>次の手順

プロジェクトが Azure リソースに移行されました。 これまでに記述したすべてのアプリケーションで Training および Prediction キーを更新する必要があります。
