---
title: Azure AD Reporting API と証明書を使ってデータを取得する | Microsoft Docs
description: Azure AD Reporting API と証明書の資格情報を使い、ユーザーの介入なしでディレクトリからデータを取得する方法について説明します。
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 54e661284c539b835089e858ba7b5e0016e89a83
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Azure Active Directory Reporting API と証明書を使用したデータの取得

[Azure Active Directory (Azure AD) Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) は、一連の REST ベースの API を使用してデータへのプログラムによるアクセスを提供します。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

ユーザーの介入なしに Azure AD Reporting API にアクセスする場合は、証明書を使用するようにアクセスを構成できます。

この記事の内容は次のとおりです。

- 証明書を使用して Azure AD Reporting API にアクセスするために必要な手順を説明します。
- [Azure Active Directory Reporting API にアクセスするための前提条件](active-directory-reporting-api-prerequisites-azure-portal.md)を満たしていることを前提としています。 


証明書を使用して Reporting API にアクセスするには、次の手順を実行する必要があります。

1. 前提条件をインストールする
2. 目的のアプリで証明書を設定する 
3. アクセス許可を付与する
4. アクセス トークンを取得する




ソース コードについては、[Report API モジュールの活用](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils)に関するページを参照してください。 

## <a name="install-prerequisites"></a>必須コンポーネントをインストールする

Azure AD PowerShell V2 と AzureADUtils モジュールがインストールされている必要があります。

1. [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) についての手順に従い、Azure AD Powershell V2 をダウンロードしてインストールします。

2. [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1) から Azure AD Utils モジュールをダウンロードします。 
  このモジュールには、いくつかのユーティリティ コマンドレットが用意されています。その例を次に示します。
    - Nuget を使用して最新バージョンの ADAL を入手
    - ADAL を使用してユーザー キー、アプリケーション キー、証明書からアクセス トークンを取得
    - Graph API でページ単位の結果を処理

**Azure AD Utils モジュールをインストールするには:**

1. ユーティリティ モジュールの保存先ディレクトリ (c:\azureAD など) を作成し、GitHub からモジュールをダウンロードします。
2. PowerShell セッションを開いて、先ほど作成したディレクトリに移動します。 
3. 対象のモジュールをインポートし、Install-AzureADUtilsModule コマンドレットを使用して PowerShell モジュールのパスにインストールします。 

このセッションは次のような画面になります。

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>目的のアプリで証明書を設定する

**目的のアプリで証明書を設定するには:**

1. Azure portal からアプリの[オブジェクト ID を取得](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id)します。 

  ![Azure ポータル](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. PowerShell セッションを開き、Connect-AzureAD コマンドレットを使って Azure AD に接続します。

  ![Azure ポータル](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. AzureADUtils の New-AzureADApplicationCertificateCredential コマンドレットを使って証明書の資格情報をそこに追加します。 

>[!Note]
>先ほど取得したアプリケーションのオブジェクト ID のほか、証明書オブジェクト (Cert: ドライブを使用して取得) を指定する必要があります。
>


  ![Azure ポータル](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>アクセス トークンを取得する

アクセス トークンを取得するには、AzureADUtils の **Get-AzureADGraphAPIAccessTokenFromCert** コマンドレットを使用します。 

>[!NOTE]
>前のセクションで使用したオブジェクト ID ではなくアプリケーション ID を使う必要があります。
>

 ![Azure ポータル](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>アクセス トークンを使用して Graph API を呼び出す

これでスクリプトを作成できるようになりました。 AzureADUtils の **Invoke-AzureADGraphAPIQuery** コマンドレットの使用例を次に示します。 複数のページにわたる結果を処理し、それらの結果を PowerShell パイプラインに送っています。 

 ![Azure ポータル](./media/active-directory-report-api-with-certificates/script-completed.png)

ここから CSV をエクスポートして SIEM システムに保存することができます。 また、スケジュールされたタスクにスクリプトをラップすれば、ソース コードにアプリケーション キーを保存せずに Azure AD データをテナントから定期的に取得することができます。 

## <a name="next-steps"></a>次の手順

- [Reporting API の概要を掴む](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [独自のソリューションを作成する](active-directory-reporting-api-getting-started-azure-portal.md#customize)




