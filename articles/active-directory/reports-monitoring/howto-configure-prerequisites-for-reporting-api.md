---
title: Azure Active Directory レポート API にアクセスするための前提条件 | Microsoft Docs
description: Azure AD Reporting API にアクセスするための前提条件の詳細
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f72d15707d9f56b9e9b5a5d527d1204007c40afa
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621974"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Azure Active Directory レポート API にアクセスするための前提条件

[Azure Active Directory (Azure AD) レポート API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) は、一連の REST ベースの API を使用してデータへのプログラムによるアクセスを提供します。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

Reporting API は、 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して Web API へのアクセスを承認します。

レポート API へのアクセスを準備するには、次が必要です。

1. [ロールの割り当て](#assign-roles)
2. [アプリケーションを登録する](#register-an-application)
3. [アクセス許可を付与する](#grant-permissions)
4. [構成設定を収集する](#gather-configuration-settings)

## <a name="assign-roles"></a>ロールを割り当てる

API を介してレポート データにアクセスするには、次のいずれかのロールが割り当てられている必要があります。

- セキュリティ閲覧者

- セキュリティ管理者

- グローバル管理者


## <a name="register-an-application"></a>アプリケーションを登録する

スクリプトを使用してレポート API にアクセスする場合でも、アプリケーションを登録する必要があります。 これにより、承認の呼び出しと、コードによるトークンの受信に必要な**アプリケーション ID** が得られます。

Azure AD Reporting API にアクセスするようにディレクトリを構成するには、Azure AD テナントの**グローバル管理者**ディレクトリ ロールのメンバーでもある Azure 管理者アカウントで [Azure portal](https://portal.azure.com) にサインインする必要があります。

> [!IMPORTANT]
> このような管理者特権を備えた資格情報で実行されるアプリケーションは、きわめて強力です。アプリケーションの IDとシークレットの資格情報は必ず安全な場所に保管してください。
> 

**Azure AD アプリケーションを登録するには:**

1. [Azure portal](https://portal.azure.com) で、左側のナビゲーション ウィンドウから **[Azure Active Directory]** を選択します。
   
    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **[Azure Active Directory]** ページで、**[アプリの登録]** を選択します。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. **[アプリの登録]** ページから、**[新しいアプリケーションの登録]** を選択します。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. **[作成]** ページで、以下の手順を実行します。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. **[名前]** ボックスに、「`Reporting API application`」と入力します。

    b. **アプリケーションの種類**として **[Web アプリ/API]** を選択します。

    c. **[サインオン URL]** ボックスに、「`https://localhost`」と入力します。

    d. **作成**を選択します。 


## <a name="grant-permissions"></a>アクセス許可を付与する 

アクセスする API によっては、アプリに次のアクセス許可を付与する必要があります。  

| API | アクセス許可 |
| --- | --- |
| Windows Azure Active Directory | ディレクトリ データの読み取り |
| Microsoft Graph | すべての監査ログ データの読み取り |


![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/36.png)

次のセクションでは、両方の API の手順を示します。 どちらかの API にアクセスしない場合は、関連する手順を省略できます。

**API を使用するためのアクセス許可をアプリケーションに付与するには:**

1. **[アプリの登録]** ページからアプリケーションを選択して、**[設定]** を選択します。 

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. **[設定]** ページで、**[必要なアクセス許可]** を選択します。 

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. **[必要なアクセス許可]** ページの **[API]** の一覧で、**[Windows Azure Active Directory]** をクリックします。 

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. **[アクセスの有効化]** ページで、**[ディレクトリ データの読み取り]** をオンにして、**[サインインとユーザー プロファイルの読み取り]** をオフにします。 

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 上部のツール バーで **[保存]** をクリックします。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. **[必要なアクセス許可]** ページで、上部ツール バーの **[追加]** をクリックします。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. **[API アクセスの追加]** ページで、**[API を選択します]** をクリックします。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. **[API を選択します]** ページで、**[Microsoft Graph]** をクイックして、**[選択]** をクリックします。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. **[アクセスの有効化]** ページで、**[すべての監査ログ データの読み取り]** をオンにして、**[選択]** をクリックします。  

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. **[API アクセスの追加]** ページで、**[完了]** をクリックします。  

11. **[必要なアクセス許可]** ページで、上部のツール バーの **[アクセス許可の付与]** をクリックし、**[はい]** をクリックします。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>構成設定を収集する 

このセクションでは、ディレクトリから次の設定を取得する方法を示しています。

- ドメイン名
- クライアント ID
- クライアント シークレット

Reporting API への呼び出しを構成するときに、これらの値が必要です。 

### <a name="get-your-domain-name"></a>ドメイン名を取得する

**ドメイン名を取得するには:**

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** を選択します。
   
    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **[Azure Active Directory]** ページで、**[カスタム ドメイン名]** を選択します。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. ドメインの一覧からドメイン名をコピーします。


### <a name="get-your-applications-client-id"></a>アプリケーションのクライアント ID を取得する

**アプリケーションのクライアント ID を取得するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。
   
    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **[アプリの登録]** ページから、アプリケーションを選択します。

3. アプリケーション ページから、**[アプリケーション ID]** へ移動して、**[Click to copy]\(クリックしてコピー\)** を選択します。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>アプリケーションのクライアント シークレットを取得する
アプリケーションのクライアント シークレットを取得するには、新しいキーを作成し、その新しいキーを保存するときにその値を保存する必要があります。これは、後でこの値を取得することができないためです。

**アプリケーションのクライアント シークレットを取得するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。
   
    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  **[アプリの登録]** ページから、アプリケーションを選択します。

3. アプリケーション ページの上部のツール バーで、**[設定]** を選択します。 

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. **[設定]** ページの **[API アクセス]** セクションで、**[キー]** をクリックします。 

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. **[キー]** ページで、次の手順を実行します。

    ![アプリケーションを登録する](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. **[説明]** ボックスに、「`Reporting API`」と入力します。

    b. **[有効期限]** として、**[2 年]** を選択します。

    c. **[Save]** をクリックします。

    d. キー値をコピーします。


## <a name="next-steps"></a>次の手順

* [Azure Active Directory レポート API と証明書を使用してデータを取得します](tutorial-access-api-with-certificates.md)
* [監査 API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [サインイン アクティビティ レポート API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
