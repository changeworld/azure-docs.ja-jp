---
title: Azure Active Directory B2C を使用して Google アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Google アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dd0bf50d73b70e37195e8e5e45336b68e4e883e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915641"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Google アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-google-application"></a>Google アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Google アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Google アカウントを持っていない場合は、[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) で取得できます。

1. Google アカウントの資格情報で [Google Developers Console](https://console.developers.google.com/) にサインインします。
2. **[Create project (プロジェクトの作成)]** を選択し、**[作成]** をクリックします。 前にプロジェクトを作成した場合、プロジェクトの一覧を選択し、**[New Project (新しいプロジェクト)]** を選択します。
3. **[プロジェクト名]** に入力し、**[作成]** をクリックします。
3. 左側のメニューで **[Credentials (資格情報)]** を選択して、**[Create credentials (資格情報を作成)]**  >  **[Oauth client ID (Oauth クライアント ID)]** を選択します。
4. **[Configure consent screen (同意画面の構成)]** を選択します。
5. 有効な **[メール アドレス]** を選択または指定し、**[Product name shown to users (ユーザーに表示する製品名)]** を入力して、**[保存]** をクリックします。
6. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。
7. アプリケーションの**名前**を指定します。**[承認済みの JavaScript 生成元]** に「`https://login.microsoftonline.com`」と入力し、**[承認済みのリダイレクト URI]** に「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。
8. **Create** をクリックしてください。
9. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google を構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。

## <a name="configure-a-google-account-as-an-identity-provider"></a>ID プロバイダーとして Google アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。 

    ![Azure AD B2C テナントに切り替え](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Google*」などと入力します。
6. **[ID プロバイダーの種類]** を選択し、**[Google]** を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したクライアント ID を入力し、前に作成した Google アカウント アプリケーションの **[クライアント シークレット]** として記録したクライアント シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Google の構成を保存します。

