---
title: Enterprise Azure dev/Test サブスクリプションの作成
description: チームEnterprise組織向けおよび組織の Azure Dev/Test サブスクリプションを作成します。
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 5897e60a2c3c5d46dac48973d78072364af27336
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092051"
---
# <a name="creating-enterprise-and-organization-azure-devtest-subscriptions"></a>組織Enterprise Azure Dev/Test サブスクリプションの作成

Enterprise開発/テスト サブスクリプションは、大規模な組織でのチーム開発に使用できます

- 大企業でのチーム開発の場合  
- 企業全体での課金 - 個別の支払いは不要  
- Azure Enterprise Portal を使用してエンタープライズ開発/テスト オファー サブスクリプションを作成する  

## <a name="prerequisites"></a>前提条件

エンタープライズ Azure 開発/テスト サブスクリプションを作成するには、サブスクリプションを作成するために、登録アカウントに対するアカウント所有者ロールが必要です。 このロールにアクセスするには、次の 2 つの方法があります:  

- 登録Enterprise管理者は、アカウント[所有者にできます](../../cost-management-billing/manage/grant-access-to-create-subscription.md)  
- アカウントの既存の所有者は、 [にアクセス権を付与できます](../../cost-management-billing/manage/grant-access-to-create-subscription.md)。  

### <a name="important-information-before-you-add-account-owners"></a>アカウント所有者を追加する前の重要な情報

EA アカウント所有者は **、** 他の Azure オファーと同じサインイン アカウントEA Portal使用することはできません。 アカウント所有者として EA Portalに追加され、個々の特典に使用したのと同じ資格情報を使用Visual Studioします。 その場合、そのVisual Studioサブスクリプションは EA Dev/Test オファーに変換されます。  

> [!Note]  
> アカウント所有者としてアカウントにEA Portal初めてサインインすると、警告ポップアップが表示されます。 読んで理解することが不可欠です。 既存のサブスクリプションは、請求可能な EA オファーに変換されます。 アカウント所有者として追加Visual Studioサブスクライバーである場合は、さらにアクションを実行するまで、個々の月単位の Azure クレジットが失われる可能性があります。

### <a name="to-recover-your-individual-visual-studio-azure-benefits"></a>Azure 特典を使用してVisual Studio回復するには  

EA アカウント所有者としてロールを認証した後は、次のいずれかを実行できます：  

- 所有している Azure サブスクリプションを削除または移動する  
- アカウントのアカウント所有者としてロールを削除EA Portal  
- Azure 特典に対する個々のVisual Studio再度サインアップする  
- または、VLSC の Visual Studio 管理者サイトからサブスクライバーを削除し、別のサインインを使用してサブスクリプションを再割り当てします。その後、Azure の特典を新たにVisual Studioにサインアップできます  

## <a name="create-your-subscription"></a>サブスクリプションを作成する

エンタープライズ開発/テスト サブスクリプションを作成するには、まず、開発/テスト アカウントとして登録アカウントを選択します。  

1. 開始するには、 に移動[Ea.azure.com。](https://ea.azure.com) Azure Enterprise ポータルでは、会社の登録を管理できます  
2. [管理] タブで、契約番号を選択します  

    > [!Note]
    > 登録は、更新サイクルに基づいてEnterprise Agreement変更できます。
    
    ![管理ポータル ページEnterprise Agreementのスクリーンショット。](media/quickstart-create-enterprise-devtest-subscriptions/ea-manage-portal.png "Azure Enterprise Portal から会社の登録を管理します。")
    
3. 適切な登録番号を選択した後、"Account "タブを選択します  
4. ここでは、アカウントを編集し、dev/testフラグを "Yes "にすることができます。  

アカウントを追加するには:  

1. Azure エンタープライズ ポータルで、左側のナビゲーション領域の [管理] を選択します。  
2. [アカウント] ページの [アカウント] タブを選択し、[+ アカウントの追加] を選択します。  
3. 部門を選択するか、空白のままにします。
4. 目的の認証の種類を選択します。  
5. レポートのアカウントを識別する表示名を入力します。  
6. 新しいアカウントに関連付けるアカウント所有者の電子メール アドレスを入力します。  
7. メール アドレスを確認し、 [追加] を選択します。  

![[+ アカウントの追加] がEnterprise Azure Enterprise ポータル管理ページのスクリーンショット。](media/quickstart-create-enterprise-devtest-subscriptions/add-account.png "[アカウントの追加] をクリックします。")

## <a name="add-your-azure-enterprise-devtest-subscription"></a>Azure Enterprise Dev/Test サブスクリプションを追加する

エンタープライズ Azure 開発/テスト サブスクリプションを作成するアカウントを選択したら、適切なサブスクリプション オファーを追加します。 2 つの方法があります。 何を選択しても、エクスペリエンスは同じです。  

1. 各ポータルのサブスクリプションタブには、「＋／追加」ボタンが表示されます。 
    1. サインイン ページに移動します。
    1. 自分の資格情報でMicrosoft Azureします。
    1. サインインすると、利用可能なオファーの一覧を示すページが表示されます。 EA ポータル **で [は** い] を切り替え、エンタープライズ オファーを表示します。
    
1. エンタープライズ開発/テスト オファーを選択し、サブスクリプションを作成します。
    1. ID に関連付けられているアクセス許可とアクセス許可が必要です。
    1. 登録ポータル内で、アカウントを開発/テスト アカウントとして指定する必要があります。

### <a name="next-steps"></a>次の手順  

- [Azure EA Portal の管理](../../cost-management-billing/manage/ea-portal-administration.md)
- [Azure エンタープライズ ポータルを使い始める](../../cost-management-billing/manage/ea-portal-get-started.md)
