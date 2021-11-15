---
title: チュートリアル - Azure Active Directory B2C テナントを作成する
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory B2C テナントを作成し、アプリケーションの登録の準備をする方法について説明します。
services: B2C
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 0ff64806531f1d99ced2ad2b5747ae73e54114bd
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505102"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>チュートリアル:Azure Active Directory B2C テナントの作成

アプリケーションが Azure Active Directory B2C (Azure AD B2C) とやりとりできるようにするには、管理しているテナントに登録する必要があります。 

> [!NOTE]
> サブスクリプションあたり最大 20 個のテナントを作成できます。 この制限は、サービス拒否攻撃などのリソースに対する脅威からの保護に役立ち、Azure portal と基盤となるテナント作成 API の両方で適用されます。 20 を超えるテナントを作成する必要がある場合は、[Microsoft サポート](support-options.md)にお問い合わせください。
> 
> 以前に削除しようとしたテナント名を再利用する場合に、ドメイン名を入力したときに "Already in use by another directory (既に別のディレクトリによって使用されています)" というエラーが表示された場合は、[これらの手順に従って最初にテナントを完全に削除する](./faq.yml?tabs=app-reg-ga#how-do-i-delete-my-azure-ad-b2c-tenant-)必要があります。 少なくともサブスクリプション管理者のロールが必要です。 テナントを削除した後、ドメイン名を再利用する前にサインアウトしてから再度サインインすることが必要になる場合もあります。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * Azure AD B2C テナントが含まれているディレクトリに切り替える
> * Azure portal で Azure AD B2C リソースを "**お気に入り**" として追加する

次のチュートリアルでは、アプリケーションを登録する方法を学習します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- サブスクリプション内、またはサブスクリプション内のリソース グループ内で[共同作成者](../role-based-access-control/built-in-roles.md)以上のロールが割り当てられている Azure アカウントが必要です。 

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。 

1. お使いのサブスクリプションが含まれているディレクトリに切り替えます。
    1. Azure portal ツールバーで、 **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** フィルター アイコンを選択します。 
    
        ![[Directories + subscriptions]\(ディレクトリ + サブスクリプション\) フィルター アイコン](media/tutorial-create-tenant/directories-subscription-filter-icon.png)

    1. サブスクリプションが含まれるディレクトリを見つけ、その隣にある **[切り替え]** ボタンを選択します。 ディレクトリを切り替えると、ポータルが再読み込みされます。

        ![[Directories + subscriptions]\(ディレクトリ + サブスクリプション\) と [切り替え] ボタン](media/tutorial-create-tenant/switch-directory.png)

1. 使用している Azure サブスクリプションのリソース プロバイダーとして **Microsoft.AzureActiveDirectory** を追加します ([詳細](../azure-resource-manager/management/resource-providers-and-types.md?WT.mc_id=Portal-Microsoft_Azure_Support#register-resource-provider-1))。

    1. Azure portal で、 **[サブスクリプション]** を検索して選択します。
    2. お使いのサブスクリプションを選択し、左側のメニューで **[リソース プロバイダー]** を選択します。 左側にメニューが表示されない場合は、ページの左上にある **[<お使いのサブスクリプションの名前> のメニューを表示する]** アイコンを選択して展開します。
    3. **Microsoft AzureActiveDirectory** 行に **[登録済み]** の状態が表示されていることを確認します。 そうでない場合は、行を選択して **[登録]** を選択します。

1. Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

   ![[リソースの作成] ボタンを選択する](media/tutorial-create-tenant/create-a-resource.png)

1. **[Azure Active Directory B2C]** を検索して、 **[作成]** を選択します。
2. **[Create a new Azure AD B2C Tenant]\(新しい Azure AD B2C テナントの作成\)** を選択します。

    ![Azure portal で選択された新しい Azure AD B2C テナントを作成する](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. **[ディレクトリの作成]** ページで、次の情報を入力します。

   - **[組織名]** - Azure AD B2C テナントの名前を入力します。
   - **[初期ドメイン名]** - Azure AD B2C テナントのドメイン名を入力します。
   - **[国またはリージョン]** - 該当する国またはリージョンを一覧から選択します。 この選択を後から変更することはできません。
   - **[サブスクリプション]** - 一覧からサブスクリプションを選択します。
   - **[リソース グループ]** - テナントが含まれるリソース グループを選択または検索します。

    ![Azure portal でのサンプル値が含まれているテナントの作成フォーム](media/tutorial-create-tenant/review-and-create-tenant.png)

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. ディレクトリの設定を確認します。 **[作成]** を選択します。 デプロイ エラーの解決方法については[こちら](../azure-resource-manager/templates/common-deployment-errors.md)をご覧ください。

課金のために、複数の Azure AD B2C テナントを 1 つの Azure サブスクリプションにリンクすることができます。 テナントをリンクするユーザーは、Azure AD B2C テナントの管理者であること、また、Azure サブスクリプション内で共同作成者以上のロールが割り当てられていることが必要です。 「[Azure AD B2C テナントをサブスクリプションにリンクする](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)」を参照してください。

> [!NOTE]
> Azure AD B2C ディレクトリが作成されるとき、`b2c-extensions-app` というアプリケーションがその新しいディレクトリ内に自動で作成されます。 これを変更したり削除したりしないでください。 このアプリケーションは、ユーザー データを保存するために Azure AD B2C によって使用されます。 Azure AD B2C: Extensions アプリについては、[こちら](extensions-app.md)を参照してください。

## <a name="select-your-b2c-tenant-directory"></a>B2C テナント ディレクトリを選択する

新しい Azure AD B2C テナントの使用を開始するには、テナントが含まれているディレクトリに切り替える必要があります。
1. Azure portal ツールバーで、 **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** フィルター アイコンを選択します。
1. **[すべてのディレクトリ]** タブで Azure AD B2C テナントが含まれるディレクトリを見つけ、その隣にある **[切り替え]** ボタンを選択します。

最初の状態で一覧に新しい Azure B2C テナントが表示されていない場合、お使いのブラウザーのウィンドウを更新するか、一旦サインアウトしてからサインインし直してください。 次に Azure portal ツールバーで、 **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** フィルターをもう一度選択します。


## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C をお気に入りとして追加する (省略可能)

この省略可能な手順を実行すると、次のチュートリアルおよび以降のすべてのチュートリアルでの Azure AD B2C テナントの選択が容易になります。

テナントの操作が必要になるたびに **[すべてのサービス]** から「*Azure AD B2C*」を検索する代わりに、そのリソースをお気に入りとして設定することができます。 これにより、ポータル メニューの **[お気に入り]** セクションから選択することで、Azure AD B2C テナントを素早く参照することができます。

この操作は 1 回だけ実行する必要があります。 これらの手順を実行する前に、前の「[B2C テナントのディレクトリを選択する](#select-your-b2c-tenant-directory)」セクションの説明に従って Azure AD B2C テナントを含むディレクトリに切り替えていることを確認してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal のメニューで、 **[すべてのサービス]** を選択します。
1. **[すべてのサービス]** 検索ボックスで、「**Azure AD B2C**」を検索し、検索結果の上にマウス ポインターを移動して、ヒント内の星のアイコンを選択します。 **Azure AD B2C** が Azure portal の **[お気に入り]** に表示されるようになりました。
1. 新しいお気に入りの位置を変更する場合は、Azure portal のメニューに移動し、 **[Azure AD B2C]** を選択して、目的の位置まで上下にドラッグします。

    ![Azure AD B2C、[お気に入り] メニュー、Microsoft Azure portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * Azure AD B2C テナントが含まれているディレクトリに切り替える
> * Azure portal で Azure AD B2C リソースを "**お気に入り**" として追加する

次に、新しいテナントに Web アプリケーションを登録する方法を学びます。

> [!div class="nextstepaction"]
> [アプリケーションの登録 >](tutorial-register-applications.md)
