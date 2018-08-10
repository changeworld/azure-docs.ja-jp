---
title: チュートリアル - Azure Active Directory B2C を使用してアカウントによるデスクトップ アプリの認証を有効にする | Microsoft Docs
description: Azure Active Directory B2C を使用して .NET デスクトップ アプリのユーザー ログインを提供する方法に関するチュートリアル。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 2/28/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 18db911782e03d17f0b2e2ace3f8b00ddfdebf70
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599934"
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用してアカウントによるデスクトップ アプリの認証を有効にする

このチュートリアルでは、Azure Active Directory (Azure AD) B2C を使用して Windows Presentation Foundation (WPF) デスクトップ アプリケーションでユーザーをサインインおよびサインアップする方法を紹介します。 Azure AD B2C を使用すると、アプリは、オープンな標準プロトコルを使用してソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対して認証することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントにサンプル デスクトップ アプリを登録する。
> * ユーザーのサインアップ、サインイン、プロファイルの編集、パスワードのリセットに関するポリシーを作成する。
> * Azure AD B2C テナントを使用するようにサンプル アプリケーションを構成する。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 独自の [Azure AD B2C テナント](active-directory-b2c-get-started.md)を作成する。
* **.NET デスクトップ開発**と **ASP.NET および Web 開発**のワークロードと共に、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールする。

## <a name="register-desktop-app"></a>デスクトップ アプリの登録

Azure Active Directory から[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)を受け取ることができるように、アプリケーションをテナントに[登録](../active-directory/develop/developer-glossary.md#application-registration)しておく必要があります。 アプリの登録によって、テナント内のアプリの[アプリケーション ID](../active-directory/develop/developer-glossary.md#application-id-client-id) が作成されます。 

Azure AD B2C テナントの全体管理者として、[Azure Portal](https://portal.azure.com/) にログインします。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal のサービス一覧から **[Azure AD B2C]** を選択します。 

2. B2C の設定で、**[アプリケーション]** をクリックし、**[追加]** をクリックします。 

    テナントにサンプル Web アプリを登録するには、以下の設定を使用します。
    
    ![新しいアプリの追加](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | My Sample WPF App | 使用者がアプリの機能を把握できる**名前**を入力します。 | 
    | **Web アプリ/Web API を含める** | いいえ  | デスクトップ アプリの場合は **[いいえ]** を選択してください。 |
    | **ネイティブ クライアントを含める** | [はい] | これはデスクトップ アプリのため、ネイティブ クライアントとみなされます。 |
    | **リダイレクト URI** | 既定値 | Azure AD B2C が OAuth 2.0 応答のユーザー エージェントをリダイレクトする一意識別子。 |
    | **カスタム リダイレクト URI** | `com.onmicrosoft.contoso.appname://redirect/path` | `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path` ポリシーを入力すると、トークンがこの URI に送信されます。 |
    
3. **[作成]** をクリックしてアプリを登録します。

登録されたアプリは、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧からデスクトップ アプリを選択します。 登録済みのデスクトップ アプリのプロパティ ウィンドウが表示されます。

![デスクトップ アプリのプロパティ](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

**[アプリケーション クライアント ID]** をメモします。 この ID はアプリを一意に識別するため、このチュートリアルの後半でアプリを構成する際に必要になります。

## <a name="create-policies"></a>ポリシーの作成

Azure AD B2C ポリシーでは、ユーザー ワークフローを定義します。 たとえば、サインイン、サインアップ、パスワードの変更、プロファイルの編集は一般的なワークフローです。

### <a name="create-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを作成する

デスクトップ アプリにアクセスしてサインインするためにユーザーのサインアップを実行するには、**サインアップまたはサインイン ポリシー**を作成します。

1. Azure AD B2C ポータル ページから、**[サインアップまたはサインイン ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    ![サインアップまたはサインイン ポリシーの追加](media/active-directory-b2c-tutorials-desktop-app/add-susi-policy.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SiUpIn | ポリシーの**名前**を入力します。 ポリシー名の先頭には **B2C_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **B2C_1_SiUpIn** を使用します。 | 
    | **ID プロバイダー** | 電子メールのサインアップ | ユーザーを一意に識別するために使用される ID プロバイダー。 |
    | **サインアップ属性** | 表示名、郵便番号 | サインアップ中にユーザーから収集する属性を選択します。 |
    | **アプリケーション要求** | 表示名、郵便番号、新規ユーザー、ユーザーのオブジェクト ID | [アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)に含める[要求](../active-directory/develop/developer-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

### <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する

ユーザーが自身でユーザー プロファイル情報をリセットできるようにするには、**プロファイルの編集ポリシー**を作成します。

1. Azure AD B2C ポータルページから、**[プロファイルの編集ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SiPe | ポリシーの**名前**を入力します。 ポリシー名の先頭には **B2C_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **B2C_1_SiPe** を使用します。 | 
    | **ID プロバイダー** | ローカル アカウント サインイン | ユーザーを一意に識別するために使用される ID プロバイダー。 |
    | **プロファイル属性** | 表示名、郵便番号 | ユーザーがプロファイルの編集中に変更できる属性を選択します。 |
    | **アプリケーション要求** | 表示名、郵便番号、ユーザーのオブジェクト ID | プロファイル編集が成功した後に[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)に含める[要求](../active-directory/develop/developer-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

### <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する

アプリケーションでパスワードのリセットを有効にするには、**パスワードのリセット ポリシー**を作成する必要があります。 このポリシーでは、パスワードのリセット時のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンの内容を記述します。

1. Azure AD B2C ポータル ページから、**[パスワードのリセット ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SSPR | ポリシーの**名前**を入力します。 ポリシー名の先頭には **B2C_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **B2C_1_SSPR** を使用します。 | 
    | **ID プロバイダー** | Reset password using email address (電子メール アドレスを使用してパスワードをリセットする) | これは、ユーザーを一意に識別するために使用される ID プロバイダーです。 |
    | **アプリケーション要求** | ユーザーのオブジェクト ID | パスワードのリセットが成功した後に[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)に含める[要求](../active-directory/develop/developer-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

## <a name="update-desktop-app-code"></a>デスクトップ アプリのコードの更新

デスクトップ アプリの登録とポリシーの作成が完了したら、Azure AD B2C テナントを使用するようアプリを構成する必要があります。 このチュートリアルでは、サンプル デスクトップ アプリを構成します。 

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)するか、GitHub からサンプルを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

サンプルの WPF デスクトップ アプリでは、デスクトップ アプリでユーザーのサインアップ、サインイン、保護された Web API の呼び出しに Azure AD B2C をどのように使用できるかを示します。

テナントでアプリ登録を使用し、作成したポリシーを構成するには、アプリを変更する必要があります。 

アプリの設定を変更するには、次の手順に従います。

1. Visual Studio で `active-directory-b2c-wpf` ソリューションを開きます。

2. `active-directory-b2c-wpf` プロジェクトの **App.xaml.cs** ファイルを開き、次の更新を行います。

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. 前の手順で作成した "*サインアップまたはサインイン ポリシー*" という名前で **PolicySignUpSignIn** 変数を更新します。 必ず *B2C_1_* プレフィックスを含めてください。

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>サンプル デスクトップ アプリケーションの実行

**F5** キーを押し、デスクトップ アプリをビルドして実行します。 

このサンプル アプリでは、サインアップ、サインイン、プロファイルの編集、パスワードのリセットがサポートされています。 このチュートリアルでは、アプリを使用するためのサインアップをユーザーがメール アドレスを使って行う方法に焦点を当てています。 その他のシナリオについては、ご自身でお試しください。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[サインイン]** ボタンをクリックして、デスクトップ アプリのユーザーとしてサインアップします。 これは、前の手順で定義した **B2C_1_SiUpIn** ポリシーを使用します。

2. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、**[今すぐサインアップ]** リンクをクリックします。 

3. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ポリシーで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。 

    ![サインアップ ワークフロー](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. **[作成]** をクリックして、Azure AD B2C テナントにローカル アカウントを作成します。

これで、ユーザーはメール アドレスを使用してサインインし、デスクトップ アプリを使用できるようになりました。

> [!NOTE]
> **[Call API]** ボタンをクリックすると、"許可されていません" というエラーが表示されます。 デモ テナントからリソースにアクセスしようとしているため、このエラーが発生します。 アクセス トークンは Azure AD テナントのみに有効であるため、API 呼び出しは許可されていません。 次のチュートリアルに進み、テナント用に保護された Web API を作成してください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他の Azure AD B2C チュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)ことができます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure AD B2C テナントを作成し、ポリシーを作成して、Azure AD B2C テナントを使用するようサンプル デスクトップ アプリを更新する方法について学習しました。 保護されている Web API の登録、構成、呼び出しをデスクトップ アプリから実行する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> 