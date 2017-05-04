---
title: "Azure AD アプリケーション プロキシを使用して、発行されたアプリのカスタム ホーム ページを設定する | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ コネクタの基本について説明します"
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
ms.date: 04/15/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 31e8e39580ed83f13fd3ffb9981221765063a0b7
ms.lasthandoff: 04/21/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用して、発行されたアプリのカスタム ホーム ページを設定する

この記事では、ユーザーが Azure Active Directory (Azure AD) アクセス パネルと Office 365 アプリ起動ツールからアプリにアクセスした場合に、ユーザーをカスタム ホーム ページに移動させるようにアプリを構成する方法について説明します。

ユーザーがアプリを起動すると、既定では、発行されたアプリのルート ドメイン URL に移動します。 通常、ランディング ページがホーム ページの URL として設定されます。 たとえば、バックエンド アプリ http://ExpenseApp の URL が *https://expenseApp-contoso.msappproxy.net* として発行されているとします。 既定では、ホーム ページの URL は *https://expenseApp-contoso.msappproxy.net* に設定されます。

アプリ ユーザーをアプリ内の特定のページに移動させたい場合などに、Azure AD PowerShell モジュールを使用すると、ホーム ページのカスタム URL (*https://expenseApp-contoso.msappproxy.net/login/login.aspx* など) を定義できます。

>[!NOTE]
>発行されたアプリにユーザーがアクセスできるようにすると、[Azure AD アクセス パネル](active-directory-saas-access-panel-introduction.md)と [Office 365 アプリ起動ツール](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)にアプリが表示されます。

## <a name="before-you-start"></a>開始する前に

### <a name="determine-the-home-page-url"></a>ホーム ページの URL を決定する

ホーム ページの URL を設定する前に、次の点にご注意ください。

* 指定するパスがルート ドメイン URL のサブドメインのパスであることを確認してください。

  ルート ドメイン URL が https://apps.contoso.com/app1/ である場合、構成するホーム ページの URL は https://apps.contoso.com/app1/ で始まる必要があります。

* 発行されたアプリに変更を加えると、ホーム ページの URL の値がリセットされる可能性があります。 後でアプリを更新する場合は、ホーム ページの URL を再確認し、必要であれば更新してください。

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell モジュールをインストールする

PowerShell を使用してホーム ページのカスタム URL を定義する前に、Azure AD PowerShell モジュールの非標準パッケージをインストールします。 このパッケージは、Graph API エンドポイントを使用する [PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)からダウンロードできます。 

このパッケージをインストールするには、次の手順を実行します。

1. 標準の PowerShell ウィンドウを開き、次のコマンドを実行します。

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    コマンドを非管理者として実行している場合は、`-scope currentuser` オプションを使用します。
2. インストール中に **Y** を選択して、Nuget.org から 2 つのパッケージをインストールします。 両方のパッケージが必要です。 

## <a name="step-1-find-the-objectid-of-the-app"></a>手順 1: アプリの ObjectID を取得する

アプリの ObjectID を取得し、ホーム ページでアプリを検索します。

1. PowerShell を開き、Azure AD モジュールをインポートします。

    ```
    Import-Module AzureAD
    ```

2. Azure AD モジュールにテナント管理者としてサインインします。

    ```
    Connect-AzureAD
    ```
3. ホーム ページの URL に基づいてアプリを検索します。 **[Azure Active Directory]** > **[エンタープライズ アプリケーション]** > **[All applications (すべてのアプリケーション)]** に移動して、ポータルの URL を確認できます。 この例では *sharepoint-iddemo* を使用します。

    ```
    Get-AzureADApplications | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. 次のような結果が表示されます。 次のセクションで使用するために ObjectID GUID をコピーします。

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

## <a name="step-2-update-the-home-page-url"></a>手順 2: ホーム ページの URL を更新する

手順 1 で使用したのと同じ PowerShell モジュールで、次の手順を実行します。

1. 正しいアプリであることを確認して、*8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* を、前の手順でコピーした GUID (ObjectID) に置き換えます。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 これでアプリの確認が完了し、次の手順に従ってホーム ページを更新する準備ができました。

2. 修正する変更箇所を保持しておくために、空のアプリケーション オブジェクトを作成します。  

 >[!NOTE]
 >これは、アップデート対象の値を保持するための変数であるため、実際には何も作成されません。

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. ホーム ページの URL を目的の値に設定します。 この値は、発行済みアプリのサブドメイン パスである必要があります。 たとえば、ホーム ページ URL を *https://sharepoint-iddemo.msappproxy.net/* から *https://sharepoint-iddemo.msappproxy.net/hybrid/* に変更すると、アプリ ユーザーはカスタム ホーム ページに直接移動します。

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. 「手順 1: アプリの ObjectID を取得する」でコピーした GUID (ObjectID) を使用して、更新を行います。

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. 変更が成功したことを確認するには、アプリを再起動します。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>アプリを変更すると、ホーム ページの URL がリセットされる可能性があります。 このような場合は、手順 2 を繰り返します。

## <a name="next-steps"></a>次のステップ

- [Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-enable-remote-access-sharepoint.md)
- [Azure Portal でアプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)

