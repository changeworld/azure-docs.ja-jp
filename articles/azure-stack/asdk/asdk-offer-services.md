---
title: このチュートリアルでは、Azure Stack オファーを作成します | Microsoft Docs
description: プランとクォータを含む Azure Stack オファーを作成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8bcc2f3077e79ff83ac2e90db0bb0fa53ae83adc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>チュートリアル: Azure Stack の IaaS サービスをオファーする
Azure Stack のクラウド管理者は、ユーザー (テナントとも呼ばれます) がサブスクライブできるオファーを作成できます。 ユーザーはサブスクリプションを使用して Azure Stack サービスを利用できます。

このチュートリアルでは、オファーを作成して、[前のチュートリアル](asdk-marketplace-item.md)でアップロードした Azure Stack マーケットプレースの Windows Server 2016 Datacenter イメージに基づいてユーザーが仮想マシンを作成できるようにする方法を説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * オファーの作成
> * プランの作成
> * クォータを設定する
> * オファーをパブリックに設定する

Azure Stack では、サブスクリプション、オファー、プランを使用してサービスがユーザーに配信されます。 ユーザーは複数のオファーにサブスクライブできます。 次の図に示すように、オファーは 1 つまたは複数のプランを含むことができ、プランは 1 つまたは複数のサービスを含むことができます。

![サブスクリプション、オファー、およびプラン](media/asdk-offer-services/sop.png)

サービスを提供する Azure Stack オペレーターは、最初にオファーを、次にプランを、最後にクォータを作成するように求められます。 オファーが作成されると、Azure Stack ユーザーは、ユーザー ポータルを使用してオファーにサブスクライブできるようになります。

## <a name="create-an-offer"></a>オファーの作成
**オファー**は、Azure Stack オペレーターがユーザーに提供する 1 つまたは複数のプランのグループです。ユーザーはそれを購入またはサブスクライブします。

1. [Azure Stack ポータル](https://adminportal.local.azurestack.external)にクラウド管理者としてサインインします。

2. **[新規]** > **[プラン]** > **[オファー]** の順にクリックします。

   ![新しいプラン](media/asdk-offer-services/new-offer.png)

2. **[新しいプラン]** セクションの **[表示名]** と **[リソース名]** に入力し、新規または既存の **[リソース グループ]** を選択します。 表示名は、ユーザーに表示される、オファーのパブリック フレンドリ名です。 クラウド オペレーターのみがリソース名を見ることができます。リソース名は、Azure Resource Manager リソースとしてオファーを操作するために、管理者が使用します。

   ![表示名](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>プランの作成
基本的なオファー情報を入力した後、少なくとも 1 つの基本プランをオファーに追加する必要があります。 

**プラン**を使用すると、Azure Stack オペレーターは、ユーザーに提供するサービスとその関連クォータをグループ化することができます。

1. **[基本プラン]** をクリックし、**[プラン]** セクションで **[追加]** をクリックして新しいプランをオファーに追加します。

   ![プランの追加](media/asdk-offer-services/new-plan.png)

2. **[新しいプラン]** セクションで **[表示名]** と **[リソース名]** を入力します。 表示名は、ユーザーに表示されるプランのフレンドリ名です。 クラウド オペレーターのみが、Azure Resource Manager リソースとしてプランを操作するためにクラウド オペレーターが使用するリソース名を見ることができます。

   ![プランの表示名](media/asdk-offer-services/plan-display-name.png)

3. 次に、プランに含めるサービスを選択します。 IaaS サービスを提供するには、**[サービス]** をクリックし、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** を選択し、**[選択]** をクリックします。

   ![プランのサービス](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>クォータを設定する
これで、サービスを含むプランがオファーにできたので、各サービスにクォータを設定する必要があります。 

**クォータ**は、提供されるプランに含まれている各サービスでユーザーが使用できるリソースの量を決定します。

1. **[クォータ]** をクリックしてから、クォータを作成するサービスを選択します。 

   この作業では、まず、コンピューティング サービスのクォータを作成します。 名前空間の一覧で、**[Microsoft.Compute]** 名前空間を選択し、**[新しいクォータの作成]** をクリックします。
   
   ![新しいクォータの作成](media/asdk-offer-services/create-quota.png)

2. **[クォータの作成]** セクションで、クォータの名前を入力し、そのクォータに必要なパラメーターを設定して **[OK]** をクリックします。

   ![クォータ名](media/asdk-offer-services/quota-properties.png)

3. **Microsoft.Compute** サービス用に作成したクォータを選択します。

   ![クォータの選択](media/asdk-offer-services/set-quota.png)

4. ネットワーク サービスとストレージ サービスのために手順 1. から 3. を繰り返し、[クォータ] セクションの [OK] をクリックします。 次に、**[新しいプラン]** セクションの **[OK]** をクリックして、プランのセットアップを終了します。 

   ![設定されたすべてのクォータ](media/asdk-offer-services/all-quotas-set.png)

5. [プラン] セクションの **[作成]** をクリックして、オファーの作成を完了します。 オファーが正常に作成され、使用可能なオファーとして表示されるときに、通知が表示されます。

   ![作成されたオファー](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>オファーをパブリックに設定する
オファーは、ユーザーがサブスクライブするオファーを選択する際に表示されるように、パブリックにする必要があります。 

オファーの状態:
- **パブリック**: すべてのユーザーに表示されます。
- **プライベート**: クラウド管理者にのみ表示されます。 プランまたはオファーの下書き作成や、クラウド管理者がユーザーの各サブスクリプションを作成する場合に便利です。
- **使用停止**: 新しいサブスクライバーに公開されません。 クラウド管理者は [使用停止] を使って、既存のサブスクライバーを変更することなく、以降のサブスクリプションを回避することができます。

> [!TIP]
> オファーの変更は、ユーザーにすぐに表示されるわけではありません。 変更された新しいオファーをユーザーが見るには、ログアウトしてから、もう一度[ユーザー ポータル](https://portal.local.azurestack.external)にログインしなければならない場合があります。

新しいオファーをパブリックに設定するには、次のように操作します。 
   - バージョン 1803 以降: 
     1. ダッシュ ボード メニューの **[Offer]** (オファー) をクリックしてから、作成したオファーをクリックします。

     2. **[アクセシビリティの状態]** をクリックし、**[パブリック]** をクリックします。

        ![状態の変更](media/asdk-offer-services/change-state.png)

     3. これで、オファーを Azure Stack ユーザー ポータルで使用できるようになりました。


   - バージョン 1803 よりも前:  
     1. ダッシュ ボード メニューの **[Offer]** (オファー) をクリックしてから、作成したオファーをクリックします。

     2. **[状態の変更]** をクリックし、**[パブリック]** をクリックします。

        ![[パブリック] の状態](media/asdk-offer-services/set-public.png)

     3. これで、オファーを Azure Stack ユーザー ポータルで使用できるようになりました。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * オファーの作成
> * プランの作成
> * クォータを設定する
> * オファーをパブリックに設定する

次のチュートリアルに進み、作成したオファーに Azure Stack ユーザーとしてサブスクライブする方法を学習してください。

> [!div class="nextstepaction"]
> [プランへのサブスクライブ](asdk-subscribe-services.md)