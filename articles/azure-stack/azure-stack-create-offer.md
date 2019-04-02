---
title: Azure Stack でのオファーの作成 | Microsoft Docs
description: クラウド管理者が Azure Stack でユーザーのオファーを作成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 59c69477e120facec1fbf132ce7017ca21aa8748
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092618"
---
# <a name="create-an-offer-in-azure-stack"></a>Azure Stack でのオファーの作成

[オファー](azure-stack-key-features.md)は、プロバイダーがユーザーに提示する 1 つまたは複数のプランのグループで、ユーザーはそれを購入またはサブスクライブします。 この記事では、[作成したプラン](azure-stack-create-plan.md)を含むオファーを作成する方法について説明します。 このオファーのサブスクライバーは仮想マシン (VM) を設定できます。

## <a name="create-an-offer-1902-and-later"></a>オファーの作成 (1902 以降)

1. [Azure Stack 管理者ポータル](https://adminportal.local.azurestack.external)にサインインし、**[+ リソースの作成]**、**[オファー + プラン]**、**[オファー]** の順に選択します。

   ![オファーの作成](media/azure-stack-create-offer/offers.png)

2. タブ付きのユーザー インターフェイスが表示され、オファー名の定義、既存の基本プランとアドオン プランの追加、新しい基本プランとアドオン プランの作成を行うことができます。 最も重要なのは、作成を決定する前に、作成するオファーの詳細を確認できることです。

   **[基本]** タブの **[新しいオファー]** の下で **[表示名]** と **[リソース名]** を入力し、**[リソース グループ]** の下で **[新規作成]** または **[既存のものを使用]** を選択します。 表示名は、オファーのフレンドリ名です。 このフレンドリ名は、ユーザーがユーザー ポータル内でオファーをサブスクライブするときに表示されるオファーに関する唯一の情報です。 ユーザーがオファーで提供される内容を理解できるように、わかりやすい名前を付けてください。 リソース名は、管理者のみが表示できます。 この名前を使用して、管理者はオファーを Azure リソース マネージャーのリソースとして操作します。 このタブでは、このオファーをパブリックにするか、プライベート (既定) のままにするかを選択することもできます。 後で[オファーのパブリックまたはプライベートの状態を変更する](#change-the-state-of-an-offer)ことも可能です。

   ![新しいオファー](media/azure-stack-create-offer/new-offer.png)
  
3. **[基本プラン]** タブを選択します。オファーに含めるプランを選択します。

   ![プランの選択](media/azure-stack-create-offer/select-plan.png)

4. この時点で、アドオン プランを作成して基本プランを変更できますが、これはオプションです。 次の記事「[Azure Stack のアドオン プラン](create-add-on-plan.md)」では、アドオン プランを作成します。

5. **[確認および作成]** タブを選択します。オファーの概要に目を通し、値がすべて正しいことを確認します。 このインターフェイスでは、選択したプランのクォータを一度に 1 つずつ展開して、プランの各クォータの詳細を表示し、戻って必要な編集を加えることができます。

6. **[作成]** を選択して、オファーを作成します。

   ![[Review and create] (確認および作成)](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>オファーの状態の変更

オファーの作成後、その状態を変更することができます。 ユーザーがサブスクライブ時にすべての情報を見ることができるように、オファーは "**パブリック**" にする必要があります。 オファーの状態:

- **パブリック:** ユーザーに表示されます。
- **プライベート**:クラウド管理者にのみ表示されます。 この設定は、プランまたはオファーの下書き作成や、クラウド管理者が[ユーザーの各サブスクリプションを作成する](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)場合に便利です。
- **使用停止**:新しいサブスクライバーに公開されません。 クラウド管理者は、オファーを使用停止することによって、既存のサブスクライバーに影響を及ぼすことなく、以降のサブスクリプションを回避することができます。

  > [!TIP]  
  > オファーの変更は、ユーザーにすぐに表示されるわけではありません。 変更された新しいオファーをユーザーが見るには、サインアウトしてから、もう一度ユーザー ポータルにサインインする必要がある場合があります。

オファーの状態を変更するには 2 つの方法があります。

1. **[すべてのリソース]** で、オファーの名前を選択します。 オファーの **[概要]** 画面で、**[状態の変更]** を選択します。 使用する状態 (**[パブリック]** など) を選択します。

   ![状態の選択](media/azure-stack-create-offer/change-state.png)

2. **[すべてのリソース]** で、オファーの名前を選択します。 次に、**[プランの設定]** を選択します。 使用する状態 (**[パブリック]** など) を選択し、**[保存]** を選択します。

   ![アクセシビリティの状態を選択する](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>オファーの作成 (1901 以前)

1. [Azure Stack 管理者ポータル](https://adminportal.local.azurestack.external)にサインインし、**[+ リソースの作成]**、**[テナントのプラン]**、**[オファー]** の順に選択します。

   ![オファーの作成](media/azure-stack-create-offer/image01.png)
  
2. **[新しいオファー]** で **[表示名]** と **[リソース名]** を入力し、**[リソース グループ]** の下で **[新規作成]** または **[既存のものを使用]** を選択します。 表示名は、オファーのフレンドリ名です。 このフレンドリ名は、ユーザーがオファーをサブスクライブするときに表示されるオファーに関する唯一の情報です。 ユーザーがオファーで提供される内容を理解できるように、わかりやすい名前を付けてください。 リソース名は、管理者のみが表示できます。 この名前を使用して、管理者はオファーを Azure リソース マネージャーのリソースとして操作します。

   ![新しいオファー](media/azure-stack-create-offer/image01a.png)
  
3. **[基本プラン]** を選択して **[プラン]** を開きます。 オファーに含めるプランを選択し、**[選択]** を選択します。 オファーを作成するには、**[作成]** を選択します。

   ![プランの選択](media/azure-stack-create-offer/image02.png)
  
4. オファーの作成後、その状態を変更することができます。 ユーザーがサブスクライブ時にすべての情報を見ることができるように、オファーは "**パブリック**" にする必要があります。 オファーの状態:

   - **パブリック:** ユーザーに表示されます。
   - **プライベート**:クラウド管理者にのみ表示されます。 この設定は、プランまたはオファーの下書き作成や、クラウド管理者が[ユーザーの各サブスクリプションを作成する](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)場合に便利です。
   - **使用停止**:新しいサブスクライバーに公開されません。 クラウド管理者は、オファーを使用停止することによって、既存のサブスクライバーに影響を及ぼすことなく、以降のサブスクリプションを回避することができます。

   > [!TIP]  
   > オファーの変更は、ユーザーにすぐに表示されるわけではありません。 変更された新しいオファーをユーザーが見るには、サインアウトしてから、もう一度ユーザー ポータルにサインインする必要がある場合があります。

   オファーの概要画面で、**[アクセシビリティの状態]** を選択します。 使用する状態 (**[パブリック]** など) を選択し、**[保存]** を選択します。

     ![状態の選択](media/azure-stack-create-offer/change-stage-1807.png)

     または、**[状態の変更]** を選択して、状態を選択します。

    ![アクセシビリティの状態を選択する](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> PowerShell を使用して、既定のオファー、プラン、およびクォータを作成することもできます。 詳しくは、「[Azure Stack PowerShell Module 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [サブスクリプションを作成する](azure-stack-subscribe-plan-provision-vm.md)
- [仮想マシンのプロビジョニング](azure-stack-provision-vm.md)
