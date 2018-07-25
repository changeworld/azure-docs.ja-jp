---
title: Azure Active Directory B2C を使用して Amazon アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Amazon アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5fb6289f75f0c98cc218233d8adb900484ee4a17
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916498"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Amazon アカウントでのサインアップおよびサインインを設定する

## <a name="create-an-amazon-application"></a>Amazon アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Amazon アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Amazon アカウントを持っていない場合は、[http://www.amazon.com/](http://www.amazon.com/) で取得できます。

1. Amazon アカウントの資格情報で [Amazon Developer Center](https://login.amazon.com/) にサインインします。
2. まだ行っていない場合は、 **[Sign Up (サインアップ)]** をクリックして、開発者登録手順に従い、ポリシーを受け入れます。
3. **[Register new application (新しいアプリケーションの登録)]** を選択します。
4. **[Name (名前)]**、**[Description (説明)]**、および **[Privacy Notice URL (プライバシーに関する声明の URL)]** を入力して、**[Save (保存)]** をクリックします。
5. **[Web Settings (Web 設定)]** セクションで、**[Client ID (クライアント ID)]** の値をコピーします。 **[Show Secret (シークレットの表示)]** を選択して、クライアント シークレットを取得しコピーします。 テナントで ID プロバイダーとして Amazon アカウントを構成するには、この両方が必要です。 **[Client Secret]** は、重要なセキュリティ資格情報です。
6. **[Web Settings (Web 設定)]** セクションで、**[Edit (編集)]** を選択し、**[Allowed JavaScript Origins (許可される JavaScript の配信元)]** で「`https://login.microsoftonline.com`」を入力し、**[Allowed Return URLs (許可されるリターン URL)]** で「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」を入力します。 **{tenant}** は、実際のテナントの名前 (例: contoso.onmicrosoft.com) に置き換えます。 
7. **[Save]** をクリックします。

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>ID プロバイダーとして Amazon アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。 

    ![Azure AD B2C テナントに切り替え](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Amazon*」などと入力します。
6. **[ID プロバイダーの種類]** を選択し、**[Amazon]** を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したクライアント ID を入力し、前に作成した Amazon アカウント アプリケーションの **[クライアント シークレット]** として記録したクライアント シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Amazon の構成を保存します。

