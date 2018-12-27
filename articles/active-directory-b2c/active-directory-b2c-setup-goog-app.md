---
title: Azure Active Directory B2C を使用して Google アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Google アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5f6ce3564cf4056dc9295e1885be425bbe3d7701
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165302"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Google アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-google-application"></a>Google アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Google アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Google アカウントを持っていない場合は、[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) で取得できます。

1. Google アカウントの資格情報で [Google Developers Console](https://console.developers.google.com/) にサインインします。
2. **[Create project (プロジェクトの作成)]** を選択し、**[作成]** をクリックします。 前にプロジェクトを作成した場合、プロジェクトの一覧を選択し、**[New Project (新しいプロジェクト)]** を選択します。
3. **プロジェクト名**を入力し、**[作成]** をクリックして、新しいプロジェクトを使用していることを確認します。
3. 左側のメニューで **[Credentials (資格情報)]** を選択して、**[Create credentials (資格情報を作成)]**  >  **[Oauth client ID (Oauth クライアント ID)]** を選択します。
4. **[Configure consent screen (同意画面の構成)]** を選択します。
5. 有効な**メール アドレス**を選択または指定し、ユーザーに表示する**製品名**を入力します。**[認証済みドメイン]** に「`b2clogin.com`」を追加し、**[保存]** をクリックします。
6. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。
7. アプリケーションの**名前**を指定します。**[承認済みの JavaScript 生成元]** に「`https://your-tenant-name.b2clogin.com`」と入力し、**[承認済みのリダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
8. **Create** をクリックしてください。
9. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google を構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。

## <a name="configure-a-google-account-as-an-identity-provider"></a>ID プロバイダーとして Google アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Google*」などと入力します。
6. **[ID プロバイダーの種類]** を選択し、**[Google]** を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したクライアント ID を入力し、前に作成した Google アカウント アプリケーションの **[クライアント シークレット]** として記録したクライアント シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Google の構成を保存します。

