---
title: "Azure AD アプリケーション プロキシを使用して、公開されたアプリケーションのカスタム ホーム ページを設定する | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ コネクタの基本について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>Azure AD アプリケーション プロキシを使用して、公開されたアプリケーションのカスタム ホーム ページを設定する

この記事では、Azure AD アクセス パネルと Office 365 アプリ起動ツールからユーザーがアプリケーションにアクセスした場合に、ユーザーをカスタム ホーム ページに移動させるようにアプリケーションを構成する方法について説明します。

>[!NOTE]
>アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
> 
 
ユーザーをアプリケーション内の特定のページに移動させたい場合などに、Azure AD Powershell モジュールを使用すると、ホーム ページのカスタム URL (https://expenseApp-contoso.msappproxy.net/login/login.aspx など) を定義できます。

>[!NOTE]
>公開されたアプリケーションにユーザーがアクセスできるようにする場合は、[Azure AD アクセス パネル](active-directory-saas-access-panel-introduction.md)と [Office 365 アプリ起動ツール](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)にアプリが表示されます。 
>

ユーザーがアプリを起動すると、既定では、公開されたアプリのルート ドメイン URL にリダイレクトされます。 通常、ランディング ページはホーム ページの URL に設定されます。 たとえば、バックエンド アプリケーション http://ExpenseApp が、https://expenseApp-contoso.msappproxy.net として公開されているとします。 既定では、ホーム ページの URL は https://expenseApp-contoso.msappproxy.net に設定されます。

## <a name="determine-the-home-page-url"></a>ホーム ページの URL を決定する

ホーム ページの URL を設定する前にいくつかのことを認識しておく必要があります。

* 指定するパスは、ルート ドメイン URL のサブドメインであることを確認してください。

 たとえば、公開したアプリケーションがホーム ページの URL https://intranet-contoso.msappproxy.net/ からアクセスできる場合、構成するホーム ページの URLは、https://intranet-contoso.msappproxy.net/ で始まる必要があります。 
 
* ホーム ページの URL が https://apps.contoso.com/app1/ である場合は、ホーム ページの URL は https://apps.contoso.com/app1/ で始まる必要があります。

* 公開されたアプリケーションに変更を加えると、ホーム ページの URL の値がリセットされる可能性があります。 したがって、アプリケーションを更新する場合は、ホーム ページの URL を再確認し、必要であれば更新してください。


次のセクションでは、公開されたアプリケーション向けのホーム ページのカスタム URL を設定する方法を説明します。 

## <a name="install-the-azure-ad-powershell-module"></a>Azure AD Powershell モジュールをインストールする

Powershell を使用してホーム ページのカスタム URL を定義する前に、Azure AD PowerShell モジュールの非標準パッケージをインストールする必要があります。  GRAPH API エンドポイントを使用して、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)からこのパッケージをダウンロードできます。 

**Powershell を使用してパッケージをインストールする方法:**

1. 標準の PowerShell を開きます。
2. 次のコマンドを実行します。

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 非管理者として実行している場合は、_-scope currentuser_ オプションを使用してください。
3. インストール中に "Y" を選択して、Nuget.org から&2; つのパッケージをインストールします。  パッケージを使用するには、どちらも必要です。 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>Azure AD Powershell モジュールを使用してホーム ページのカスタム URL を設定する

これで Azure AD の Powershell モジュールのインストールが完了し、2 つの簡単な手順でホーム ページの URL を設定する準備ができました。

1. 更新するアプリケーションを探します。
2. アプリケーションのホーム ページの URL を更新します。

###<a name="step-1--find-the-objectid-of-the-application"></a>手順 1 - アプリケーションの ObjectID を取得する

まず、アプリケーションの ObjectID を取得して、ホーム ページでアプリケーションを検索してください。

1. PowerShell を開きます。
2. Azure AD モジュールをインポートします。
  
 ```
 Import-Module AzureAD
 ```
3. Azure AD モジュールにログインします。  以下のコマンドレットを使用し、画面の指示に従います。 必ずテナント管理者としてログインしてください。
 
 ```
 Connect-AzureAD
 ```
4. 次のコマンドレットによって、_sharepoint-iddemo_ を含むホーム ページをもとに、アプリケーションが検索されます。 これが編集対象のアプリケーションです。 この値は、自分のアプリケーションに該当する値に置き換えてください。
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. 次の応答のような結果が表示されます。 GUID (下の ObjectID の値) をコピーする必要があります。
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. GUID (ObjectID) の値をコピーします。 これは、次の手順で必要です。


###<a name="step-2--update-the-homepage-url"></a>手順 2 – ホーム ページの URL を更新する

同じ PowerShell モジュールを使用して、アプリケーション ID を検索した場合と同様に、ホーム ページの URL を更新します。 PowerShell にログインした後、次の手順に従ってください。

1. 正しいアプリケーションであることを確認して、_8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ を、上記の手順 1 でコピーしたアプリケーションの ObjectID 値に置き換えます。 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 これでアプリケーションの確認が完了し、次の手順に従ってホーム ページを更新する準備ができました。
 
2. 修正する変更箇所を保持しておくために、空のアプリケーション オブジェクトを作成します。 これは、アップデート対象の値を保持するための変数であるため、実際には何も作成されません。
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. ホーム ページを目的の値に設定します。 この値が公開済みアプリケーションのサブドメイン パスであることに留意してください。 たとえば、ホーム ページを _https://sharepoint-iddemo.msappproxy.net/_ から _https://sharepoint-iddemo.msappproxy.net/hybrid/_ に変更すると、ユーザーはカスタム ホーム ページに直接移動します。
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. 最後に更新を実行します。 上記の手順 1 からコピーした GUID を使用してください。
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. 変更が成功したことを確認するには、アプリケーションを再度起動して、カスタム ホーム ページを確認してください。
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>アプリに変更を加えると、ホーム ページの URL がリセットされる場合があることに注意してください。 その場合は、このプロセスを繰り返してください。

##<a name="next-steps"></a>次のステップ

[Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-enable-remote-access-sharepoint.md)<br>
[Azure Portal でアプリケーション プロキシを有効にする](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


