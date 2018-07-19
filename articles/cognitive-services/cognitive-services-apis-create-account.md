---
title: Azure Portal で Cognitive Services APIs アカウントを作成する | Microsoft Docs
description: Azure Portal で Microsoft Cognitive Services APIs アカウントを作成する方法。
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036155"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Azure Portal で Cognitive Services APIs アカウントを作成する

Microsoft Cognitive Service API を使用するには、まず、Azure Portal でアカウントを作成する必要があります。

1. [Azure ポータル](http://portal.azure.com)にサインインします。

2. **[+ リソースの作成]** をクリックします。

3. Azure Marketplace で、**[AI + Cognitive Services]** を選択し、使用可能な API の一覧を探します。 Cognitive Services APIs の完全な一覧を表示するには、**[すべて表示]** をクリックします。 選択する API をクリックして、先に進みます。

    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. **[作成]** ページで、次の情報を指定します。

   - **アカウント名:** アカウントの名前。 わかりやすい名前を使用することをお勧します (*AFaceAPIAccount* など)。

   - **サブスクリプション:** 使用可能な Azure サブスクリプションの中から、少なくとも共同作業者アクセス許可を持っているサブスクリプションを選択します。

   - **API の種類:** 使用する Cognitive Services API を選択します。 使用できるさまざまな Cognitive Services APIs の詳細については、[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) サイトを参照してください。

   - **価格レベル:** Cognitive Services アカウントのコストは、実際の使用量と選択しているオプションによって異なります。 各 API の価格の詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/)を参照してください。

   - **リソース グループ :** リソース グループとは、同じライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 リソース グループの詳細については、「[ポータルを使用した Azure リソースの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)」を参照してください。

   - **リソース グループの場所:** これは、選択されている API がグローバルである (特定の場所にバインドされていない) 場合にのみ必要です。 API がグローバルであり、特定の場所にバインドされていない場合は、Cognitive Services API アカウントに関連付けられたメタデータが配置されるリソース グループの場所を指定する必要があります。 この場所は、アカウントの実行時の可用性には影響しません。 リソース グループの詳細については、「[ポータルを使用した Azure リソースの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)」を参照してください。

   - **オンライン サービスの使用条件の明確な承認:** アカウントを作成するには、サブスクリプションの所有者または共同作成者 ([Azure のロールベースのアクセス制御によって定義されます](https://docs.microsoft.com/azure/role-based-access-control/overview)) が、[オンラインサービスの利用条件](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx)の Cognitive Services に適用される条件を明確に承認する必要があります。 

     サブスクリプションの所有者は、特定のリソース グループまたはサブスクリプション用の Cognitive Services アカウントの作成を、[Azure ポリシー](../azure-policy/azure-policy-introduction.md)を通して無効にすることができます。これを行うには、[Azure Portal を使用したリソース ポリシーの割り当てと管理](../azure-policy/assign-policy-definition.md)に関する記事に従って、[許可されていないリソースの種類] ポリシー定義を割り当て、**Microsoft.CognitiveServices/accounts** をターゲットのリソース タイプとして指定します。

     アカウントの作成が無効になっている場合は、アカウントの作成時に次のエラーが表示されます。

     ![アカウント作成エラー](media/cognitive-services-apis-create-account/error-message.png)

5. Azure Portal ダッシュボードにアカウントをピン留めするには、**[ダッシュボードにピン留めする]** をクリックします。

6. **[作成]** をクリックしてアカウントを作成します。

Cognitive Services アカウントが正常にデプロイされたら、ダッシュボードのタイルをクリックしてアカウント情報を表示します。

**[概要]** セクションの **[エンドポイント URL]** と **[キー]** セクションのキーを使用して、アプリケーションでの API の呼び出しを開始できます。

![アカウント情報の表示](media/cognitive-services-apis-create-account/display-account.png)

![アカウント キーの表示](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>次の手順

使用できるすべての Microsoft Cognitive Services の詳細については、「[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)」を参照してください。

サンプルの Cognitive Services APIs を使用するためのクイック スタート ガイドについては、以下を参照してください。

 - [Computer Vision C# クイック スタート](computer-vision/quickstarts/csharp.md)
 - [Text Analytics と Python](text-analytics/quickstarts/python.md)
 - [Face API と JavaScript](face/quickstarts/javascript.md)
