---
title: Google を Azure Active Directory B2B の ID プロバイダーとして追加する | Microsoft Docs
description: Google とのフェデレーションを行って、ゲスト ユーザーが独自の Gmail アカウントを使用して Azure AD アプリにサインインできるようにします
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: 5bc94b6fe69a9ffec11fcbab952a6f8aa3e2259a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569007"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する

Google とのフェデレーションを設定することで、招待されたユーザーが Microsoft アカウント (MSA) または Azure AD アカウントを作成することなく、独自の Google アカウントを使用して共有アプリおよびリソースにサインインできるようにできます。  
> [!NOTE]
> Google ゲスト ユーザーは、テナント コンテキストが含まれたリンク (例: `https://myapps.microsoft.com/?tenantid=<tenant id>`) を使用してサインインする必要があります。 アプリケーションとリソースへの直接リンクも、テナント コンテキストが含まれている限り機能します。 ゲスト ユーザーは現在、テナント コンテキストが含まれていないエンドポイントを使用してサインインできません。 たとえば、`https://myapps.microsoft.com`、`https://portal.azure.com`、またはチームの共有エンドポイントを使用すると、エラーが発生します。
 
## <a name="what-is-the-experience-for-the-google-user"></a>Google ユーザーのエクスペリエンスの内容
Google Gmail ユーザーに招待を送信すると、そのゲスト ユーザーは、テナント コンテキストが含まれているリンクを使用して共有アプリまたはリソースにアクセスする必要があります。 Google に既にサインインしているかどうかによって、エクスペリエンスが異なります。
  - ゲスト ユーザーが Google にサインインしていない場合、Google にサインインするよう求められます。
  - ゲスト ユーザーが Google に既にサインインしている場合、使用するアカウントを選択するよう求められます。 招待に使用されたアカウントを選択する必要があります。

"ヘッダーが長すぎる" エラーが発生したら、ゲスト ユーザーは Cookie のクリアを試すことができます。または、プライベートまたは匿名のウィンドウを開いて、もう一度サインインを試すことができます。

![Google でサインイン](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>手順 1: Google 開発者プロジェクトを構成する
最初に、Google Developers Console で新しいプロジェクトを作成して、Azure AD に後で追加するクライアント ID とクライアント シークレットを取得します。 
1. https://console.developers.google.com で Google API に移動し、Google アカウントでサインインします。 共有のチーム Google アカウントを使用することをお勧めします。
2. 新しいプロジェクトを作成します。ダッシュボードで **[プロジェクトを作成]**、**[作成]** の順に選択します。 [新しいプロジェクト] ページで、**[プロジェクト名]** に入力を行ってから **[作成]** を選択します。
   
   ![新しい Google プロジェクト](media/google-federation/google-new-project.png)

3. 新しいプロジェクトがプロジェクト メニューで選択されていることを確認します。 次に、左上隅にあるメニューを開いて、**[API とサービス]** > **[認証情報]** の順に選択します。

   ![Google API 資格情報](media/google-federation/google-api.png)
 
4. **[OAuth consent screen]\(OAuth 同意画面\)** タブを選択して、**[Application name]\(アプリケーション名\)** を入力します。 (他の設定はそのままにします)。

   ![Google の [OAuth 同意画面]](media/google-federation/google-oauth-consent-screen.png)

5. **[Authorized domains]\(承認済みドメイン\)** セクションまでスクロールし、「microsoftonline.com」と入力します。

   ![[Authorized domains]\(承認済みドメイン\) セクション](media/google-federation/google-oauth-authorized-domains.png)

6. **[保存]** を選択します。

7. **[認証情報]** タブを選択します。**[認証情報を作成]** メニューで、**[OAuth クライアント ID]** を選択します。

   ![Google API 資格情報](media/google-federation/google-api-credentials.png)

8. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択し、**[承認済みのリダイレクト URI]** で次の URI を入力します。
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(`<directory id>` は自分のディレクトリ ID に置き換えます)
   
    > [!NOTE]
    > ディレクトリ ID を見つけるには、 https://portal.azure.com に移動します。**[Azure Active Directory]** で **[プロパティ]** を選択して、**ディレクトリ ID** をコピーします。

   ![OAuth クライアント ID の作成](media/google-federation/google-create-oauth-client-id.png)

9. **作成**を選択します。 クライアント ID とクライアント シークレットをコピーします。これらは、Azure AD ポータルで ID プロバイダーを追加する際に使用します。

   ![OAuth クライアント ID とクライアント シークレット](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>手順 2: Azure AD で Google フェデレーションを構成する 
次に、Google クライアント ID とクライアント シークレットを設定します。Azure AD ポータルで入力するか、または PowerShell を使用します。 Gmail アドレスを使って自分自身を招待したうえで、招待した Google アカウントを使って招待を利用してみて、Google フェデレーション構成をテストするようにしてください。 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Azure AD ポータルで Google フェデレーションを構成するには 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左ウィンドウで、**[Azure Active Directory]** を選択します。 
2. **[組織の関係]** を選択します。
3. **[ID プロバイダー]** を選択し、**[Google]** ボタンをクリックします。
4. 名前を入力します。 次に、先ほど取得したクライアント ID とクライアント シークレットを入力します。 **[保存]** を選択します。 

   ![Google ID プロバイダーの追加](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>PowerShell を使用して Google フェデレーションを構成するには
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. `Connect-AzureAD` というコマンドを実行します。
3. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
4. 次のコマンドを実行します。 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > 「手順 1: Google 開発者プロジェクトを構成する」で作成したアプリのクライアント ID とクライアント シークレットを使用します。 詳細については、記事「[New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview)」を参照してください。 
 
## <a name="how-do-i-remove-google-federation"></a>Google フェデレーションを削除する方法
Google フェデレーション セットアップは削除できます。 これを行うと、招待を既に利用している Google ゲスト ユーザーはサインインできなくなります。しかし、ディレクトリからこれらのユーザーを削除して招待し直すことで、もう一度リソースへのアクセス権を付与できます。 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Azure AD ポータルで Google フェデレーションを削除するには: 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左ウィンドウで、**[Azure Active Directory]** を選択します。 
2. **[組織の関係]** を選択します。
3. **[ID プロバイダー]** を選択し、**[Google]** ボタンをクリックします。
4. **[Google]** を選択してから、**[削除]** を選択します。 
   
   ![ソーシャル ID プロバイダーの削除](media/google-federation/google-social-identity-providers.png)

1. **[はい]** を選択して削除を確定します。 

### <a name="to-delete-google-federation-by-using-powershell"></a>PowerShell を使用して Google フェデレーションを削除するには: 
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. `Connect-AzureAD` を実行します。  
4. ログイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
5. 次のコマンドを入力します。

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 詳細については、「[Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)」を参照してください。 