---
title: この記事では、Azure Stack のオファーとプランを更新する方法について説明します | Microsoft Docs
description: この記事では、既存の Azure Stack のオファーとプランを表示および変更する方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 00bb17eadfee32e9b0d006ac76bb8e1cd614f13e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780390"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack のアドオン プラン

基本プランの初期オファーを超えて、"*コンピューター*"、"*ストレージ*"、"*ネットワーク*" のクォータを拡張したり、追加のサービスを提供したりするには、Azure Stack のオペレーターがアドオン プランを作成して[基本プラン](azure-stack-create-plan.md)に変更を加えます。 アドオン プランは、基本プランに変更を加えるものであり、ユーザーが任意でサブスクライブできる拡張機能です。

1 つのプランにすべてをまとめることが最適な場合もありますが、 基本プランを用意し、追加のサービスはアドオン プランを使用して提供する方がよい場合もあります。 たとえば、IaaS サービスを基本プランの一部として提供し、すべての PaaS サービスをアドオン プランとして扱うことができます。

アドオン プランを使用するもう 1 つの理由は、リソース使用量の監視に役立つからです。 そのためには、必要なサービスに応じて、比較的小さなクォータを含んだ基本プランから開始します。 ユーザーが上限に達すると、指定されたプランに基づく割り当てリソースを使い切ったという警告が表示されます。 その後、ユーザーは、追加のリソースが用意されているアドオン プランを選択することができます。

> [!NOTE]
> アドオン プランを使ってクォータを拡張することが望ましくない場合には、[クォータの元の構成を編集](azure-stack-quota-types.md#edit-a-quota)することもできます。

既存のオファーのサブスクリプションにアドオン プランを追加すると、追加のリソースが表示されるまでに最大で 1 時間かかることがあります。

アドオン プランは、既存のオファーを変更して作成します。

## <a name="create-an-add-on-plan-1902-and-later"></a>アドオン プランを作成する (1902 以降)

1. Azure Stack 管理者ポータルにクラウド管理者としてサインインします。
2. [新しい基本プランを作成](azure-stack-create-plan.md)したときと同じ手順で、これまでは提供されていなかったサービスを提供する新しいプランを作成します。
3. 管理者ポータルで **[オファー]** をクリックし、アドオン プランで更新するオファーを選択します。

   ![アドオン プランを作成する](media/create-add-on-plan/add-on1.png)

4. オファーのプロパティの一番下までスクロールし、**[アドオン プラン]** を選択します。 **[追加]** をクリックします。

    ![アドオン プランを作成する](media/create-add-on-plan/add-on2.png)

5. 追加するプランを選択します。 この例では、プランの名前は **20-storageaccounts** です。 プランの選択後、**[選択]** をクリックして、オファーにプランを追加します。 プランがオファーに正常に追加されたという通知が届きます。

    ![アドオン プランを作成する](media/create-add-on-plan/add-on3.png)

6. オファーに含まれているアドオン プランの一覧を見て、新しいアドオン プランが一覧に表示されていることを確認します。

    [![アドオン プランを作成する](media/create-add-on-plan/add-on4.png "アドオン プランを作成する")](media/create-add-on-plan/add-on4lg.png#lightbox)

## <a name="create-an-add-on-plan-1901-and-earlier"></a>アドオン プランを作成する (1901 以前)

1. Azure Stack 管理者ポータルにクラウド管理者としてサインインします。
2. [新しい基本プランを作成](azure-stack-create-plan.md)したときと同じ手順で、これまでは提供されていなかったサービスを提供する新しいプランを作成します。 この例では、Key Vault (**Microsoft.KeyVault**) サービスが新しいプランに含められます。
3. 管理者ポータルで **[オファー]** をクリックし、アドオン プランで更新するオファーを選択します。

   ![アドオン プランを作成する](media/create-add-on-plan/1.PNG)

4. オファーのプロパティの一番下までスクロールし、**[アドオン プラン]** を選択します。 **[追加]** をクリックします。

    ![アドオン プランを作成する](media/create-add-on-plan/2.PNG)

5. 追加するプランを選択します。 この例では、プランの名前は **[Key vault plan]** です。 プランの選択後、**[選択]** をクリックして、オファーにプランを追加します。 プランがオファーに正常に追加されたという通知が届きます。

    ![アドオン プランを作成する](media/create-add-on-plan/3.PNG)

6. オファーに含まれているアドオン プランの一覧を見て、新しいアドオン プランが一覧に表示されていることを確認します。

    ![アドオン プランを作成する](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>次の手順

* [オファーの作成](azure-stack-create-offer.md)