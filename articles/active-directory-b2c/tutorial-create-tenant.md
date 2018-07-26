---
title: チュートリアル - Azure Active Directory B2C テナントを作成する | Microsoft Docs
description: Azure portal を使用して Azure Active Directory B2C テナントを作成し、アプリケーションの登録の準備をする方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc48cd3eb40d93c26a68caf843a89f7bbfb46c6c
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236895"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>チュートリアル: Azure Active Directory B2C テナントを作成する

アプリケーションが Azure Active Directory (Azure AD) B2C と対話できるようにするには、管理しているテナントに登録する必要があります。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク

次のチュートリアルでは、アプリケーションを登録する方法を学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する

1. Azure portal の左上にある **[リソースの作成]** を選択します。
2. Azure Marketplace リソースの一覧の上にある検索ボックスで、**Active Directory B2C** を検索して選択し、**[作成]** をクリックします。
3. **[新しい Azure AD B2C テナントを作成する]** を選択し、テナント名に使用される組織名と初期のドメイン名を入力し、国を選択して、**[作成]** をクリックします。 テナントの国は後で変更することができないので、気を付けてください。

    ![テナントの作成](./media/tutorial-create-tenant/create-tenant.png)

    この例では、テナント名は contoso0522Tenant.onmicrosoft.com です

新しいテナントの管理を開始するには、"**Click here, to manage your new directory**" (新しいディレクトリを管理するには、ここをクリックしてください) の "**here**" (ここ) をクリックします。 新しいテナントにサブスクリプションをリンクする必要があることを示す**トラブルシューティング** メッセージが表示されます。 

## <a name="link-your-tenant-to-your-subscription"></a>サブスクリプションへのテナントのリンク

すべての機能を有効にして使用料を支払うには、Azure AD B2C テナントを Azure サブスクリプションにリンクする必要があります。 テナントをサブスクリプションにリンクしないと、アプリケーションは正常に動作しません。

1. Azure portal の右上でディレクトリを切り替えて、新しいテナントに関連付けるサブスクリプションを含むディレクトリを使用していることを確認してください。

    ![ディレクトリの切り替え](./media/tutorial-create-tenant/switch-directories.png)

    サブスクリプションを含むディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/tutorial-create-tenant/select-directory.png)

2. Azure portal の左上にある **[リソースの作成]** を選択します。
3. Azure Marketplace リソースの一覧の上にある検索ボックスで、**Active Directory B2C** を検索して選択し、**[作成]** をクリックします。
4. **[既存の Azure AD B2C テナントを Azure サブスクリプションにリンクする]** を選択し、作成したテナントを選択し、サブスクリプションを選択します。リソース グループ名に「*myContosoTenantRG*」と入力し、**[作成]** をクリックします。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク

> [!div class="nextstepaction"]
> [Web アプリケーションのアカウントの認証を有効にする](active-directory-b2c-tutorials-web-app.md)