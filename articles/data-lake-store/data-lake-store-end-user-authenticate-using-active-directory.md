---
title: "Active Directory を使用して Data Lake Store を認証する | Microsoft Docs"
description: "Data Lake Store で Active Directory を使用して認証する方法について説明します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7e7c9fd2fe7e6327cd39c4c26583c8fd556c9044


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store での Azure Active Directory を使用したエンドユーザーの認証
> [!div class="op_single_selector"]
> * [サービス間認証](data-lake-store-authenticate-using-active-directory.md)
> * [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store または Azure Data Lake Analytics と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 
* サービス間認証 

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Store または Azure Data Lake Analytics に対するすべての要求にアタッチされます。

この記事では、エンドユーザー認証用の Azure AD Web アプリケーションの作成方法について説明します。 サービス間認証用に Azure AD アプリケーションを構成する方法については、「[Service-to-service authentication with Data Lake Store using Azure Active Directory (Data Lake Store での Azure Active Directory を使用したサービス間認証)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* サブスクリプション ID。 これは Azure ポータルから取得できます。 たとえば、[Data Lake Store アカウント] ブレードから入手できます。
  
    ![サブスクリプション ID の取得](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* Azure AD ドメイン名。 Azure ポータルの右上隅にマウスを置くことで取得できます。 次のスクリーンショットでは、ドメイン名は **contoso.microsoft.com** であり、丸かっこ内の GUID はテナント ID です。 
  
    ![AAD ドメインの取得](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>エンドユーザー認証
これは、エンドユーザーに Azure AD 経由でアプリケーションにログインしてもらう場合に推奨する方法です。 アプリケーションは、ログインしたエンド ユーザーと同じアクセス レベルで Azure リソースにアクセスできます。 エンド ユーザーは、アプリケーションのアクセスを維持するために、資格情報を定期的に入力する必要があります。

エンドユーザーがログインすると、アクセス トークンと更新トークンがアプリケーションに与えられます。 アクセス トークンは Data Lake Store または Data Lake Analytics に対するすべての要求にアタッチされ、既定では 1 時間有効です。 更新トークンは、新しいアクセス トークンを取得するために使用でき、定期的に使用されるのであれば、既定では最大 2 週間有効です。 エンド ユーザーのログインには、2 つの異なる方法を使用できます。

### <a name="using-the-oauth-20-pop-up"></a>OAuth 2.0 ポップアップの使用
アプリケーションで、エンドユーザーが資格情報を入力できる OAuth 2.0 認証ポップアップをトリガーできます。 このポップアップは、必要であれば、Azure AD の 2 要素認証 (2FA) プロセスでも機能します。 

> [!NOTE]
> この方法は、Python または Java 用の Azure AD Authentication Library (ADAL) ではまだサポートされていません。
> 
> 

### <a name="directly-passing-in-user-credentials"></a>ユーザーの資格情報を直接渡す
アプリケーションで、ユーザーの資格情報を Azure AD に直接提供できます。 この方法は、組織 ID ユーザー アカウントのみで機能します。@outlook.com や @live.com. で終わる個人/"Live ID" ユーザー アカウントには対応しません。さらに、この方法は、Azure AD の 2 要素認証 (2FA) を必要とするユーザー アカウントには対応しません。

### <a name="what-do-i-need-to-use-this-approach"></a>この方法を使用するには何が必要か
* Azure AD ドメイン名。 これは、この記事の前提条件で既に示されています。
* Azure AD **Web アプリケーション**。
* Azure AD Web アプリケーションのクライアント ID。
* Azure AD Web アプリケーションの応答 URI。
* 委任されたアクセス許可を設定する

AD Web アプリケーションを作成し、上記の要件に合わせて構成する方法については、次の「[Active Directory アプリケーションを作成する](#create-an-active-directory-application)」を参照してください。 

## <a name="create-an-active-directory-application"></a>Active Directory アプリケーションを作成する
このセクションでは、Azure Active Directory を使用して Azure Data Lake Store でのエンドユーザーの認証を行う Azure AD Web アプリケーションを作成および構成する方法について説明します。

### <a name="step-1-create-an-azure-active-directory-application"></a>手順 1. Azure Active Directory アプリケーションを作成する
> [!NOTE]
> 以下の手順では、Azure Portal を使用します。 Azure AD アプリケーションは、[Azure PowerShell](../resource-group-authenticate-service-principal.md) または [Azure CLI](../resource-group-authenticate-service-principal-cli.md) を使用して作成することもできます。
> 
> 

1. [クラシック ポータル](https://manage.windowsazure.com/)で Azure アカウントにログインします。
2. 左側のペインで **[Active Directory]** を選択します。
   
     ![Active Directory の選択](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. 新しいアプリケーションの作成に使用する Active Directory を選択します。 Active Directory が複数ある場合、通常は、該当するサブスクリプションがあるディレクトリにアプリケーションを作成することをお勧めします。 アクセス権を付与できるリソースは、該当するサブスクリプションと同じディレクトリ内にあるアプリケーションのサブスクリプションに含まれるもののみです。  
   
     ![ディレクトリの選択](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. ディレクトリ内のアプリケーションを表示するには、 **[アプリケーション]**をクリックします。
   
     ![アプリケーションの表示](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. そのディレクトリにアプリケーションを作成したことがない場合、次の図のようなものが表示されます。 **[アプリケーションの追加]** をクリックします。
   
     ![[アプリケーションの追加]](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     または下のペインの **[追加]** をクリックします。
   
     ![追加](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. アプリケーションの名前を入力し、作成するアプリケーションの種類を選択します。 このチュートリアルでは、 **[Web アプリケーションや Web API]** を作成し、[次へ] をクリックします。
   
     ![アプリケーションの名前指定](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. アプリのプロパティを入力します。 **[サインオン URL]**には、アプリケーションについて説明する Web サイトの URI を指定します。 Web サイトの存在は検証されません。 
   **[アプリケーション ID/URI]**には、アプリケーションを識別する URI を指定します。
   
     ![アプリケーションのプロパティ](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    チェック マークをクリックしてウィザードを完了して、アプリケーションを作成します。

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>手順 2. クライアント ID と応答 URI を取得し、委任されたアクセス許可を設定する
1. **[構成]** タブをクリックし、アプリケーションのパスワードを構成します。
   
     ![アプリケーションの構成](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. **[クライアント ID]**の値をコピーします。
   
     ![[クライアント ID]](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. **[シングル サインオン]** セクションで、**応答 URI** をコピーします。
   
    ![[クライアント ID]](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. **[他のアプリケーションに対するアクセス許可]** で、**[アプリケーションの追加]** をクリックします。
   
    ![[クライアント ID]](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. **他のアプリケーションに対するアクセス許可**ウィザードで、**[Azure Data Lake]** と **[Windows** **Azure Service Management API]** を選択し、チェックマークをクリックします。
6. 既定では、新しく追加されたサービスの **[委任されたアクセス許可]** は 0 に設定されます。 Azure Data Lake と Windows Azure Management Service の **[Delegated Permissions (委任されたアクセス許可)]** ドロップダウンをクリックし、[利用可能] チェック ボックスをオンにして、値を 1 に設定します。 結果は次のようになります。
   
     ![[クライアント ID]](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. [ **Save**] をクリックします。

## <a name="next-steps"></a>次のステップ
この記事では、Azure AD Web アプリケーションを作成し、.NET SDK、Java SDK などを使用して作成するクライアント アプリケーションに必要な情報を収集しました。これで、以下の記事に進むことができます。これらの記事では、Azure AD Web アプリケーションを使用して、最初に Data Lake Store で認証を行ってからストアで他の操作を実行する方法について説明しています。

* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
* [Java SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO3-->


