---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504291"
---
## <a name="steps-to-enable-single-sign-on"></a>シングル サインオンを有効にする手順
クラウド プロビジョニングはシングル サインオンで動作します。  現時点では、エージェントのインストール時に SSO を有効にするオプションはありませんが、以下の手順を使用して SSO を有効にし、使用することができます。 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>手順 1:Azure AD Connect ファイルのダウンロードと抽出
1.  まず、最新バージョンの [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) をダウンロードします
2.  管理者特権を使用してコマンド プロンプトを開き、ダウンロードした msi に移動します。
3.  次のコマンドを実行します: `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. ファイル パスおよび抽出フォルダーの名前に一致するように filepath と extractfolder を変更します。  これで、コンテンツが抽出フォルダーに配置されます。

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>手順 2:Seamless SSO PowerShell モジュールのインポート

1. [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) をダウンロードしてインストールします。
2. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーを参照します。
3. `Import-Module .\AzureADSSO.psd1` コマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>手順 3:シームレス SSO が有効になっている Active Directory フォレストのリストを取得する

1. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 求められたら、テナントのグローバル管理者の資格情報を入力します。
2. `Get-AzureADSSOStatus` を呼び出します。 このコマンドでは、この機能が有効になっている Active Directory フォレストの一覧 ("ドメイン" リストを参照) が表示されます。

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>手順 4:各 Active Directory フォレストのシームレス SSO を有効にする

1. `Enable-AzureADSSOForest` を呼び出します。 求められたら、目的の Active Directory フォレストのドメイン管理者の資格情報を入力します。

   > [!NOTE]
   >ドメイン管理者の資格情報ユーザー名は、SAM アカウント名の形式 (contoso\johndoe または contoso.com\johndoe) で入力する必要があります。 Microsoft はユーザー名のドメイン部分を使用して、DNS を使用してドメイン管理者のドメイン コントローラーを検索します。

   >[!NOTE]
   >使用するドメイン管理者アカウントは、保護されているユーザー グループのメンバーであってはなりません。 そうである場合、操作は失敗します。

2. 機能を設定する Active Directory フォレストごとに、前の手順を繰り返します。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>手順 5. テナントで機能を有効にする

テナントで機能を有効にするには、`Enable-AzureADSSO -Enable $true`を呼び出します。
