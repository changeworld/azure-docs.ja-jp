---
title: Azure Active Directory B2C を使用して Microsoft アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Microsoft アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16e4dbac4c8146b048d4d9b76544677a6111e2a5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900831"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Microsoft アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-microsoft-account-application"></a>Microsoft アカウント アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Microsoft アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Microsoft アカウントを持っていない場合は、[https://www.live.com/](https://www.live.com/) で取得できます。

1. Microsoft アカウントの資格情報で [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)にサインインします。
2. 右上隅から **[Add an app (アプリの追加)]** を選択します。
3. アプリケーションの**名前**を入力し、**[Create (作成)]** をクリックします。
4. 登録ページで、**[アプリケーション ID]** の値をコピーします。この値は、テナントの ID プロバイダーとして Microsoft アカウントを構成するために使用します。
5. **[プラットフォームの追加]** を選択し、**[Web]** を選択します。
6. **[リダイレクト URI]** に「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。
7. **[アプリケーション シークレット]** で **[新しいパスワードを生成]** をクリックします。 画面に表示される新しいパスワードをコピーします。 この値は、テナントの ID プロバイダーとして Microsoft アカウントを構成するために必要となります。 このパスワードは重要なセキュリティ資格情報です。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>ID プロバイダーとして Microsoft account アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。 

    ![Azure AD B2C テナントに切り替え](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*MSA*」と入力します。
6. **[ID プロバイダーの種類]** を選択し、**[Microsoft アカウント]** を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したアプリケーション ID を入力し、前に作成した Microsoftアカウント アプリケーションの **[クライアント シークレット]** として記録したパスワードを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Microsoft アカウントの構成を保存します。

