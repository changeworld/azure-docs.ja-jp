---
title: "Data Lake Store で Active Directory を使用して認証する | Microsoft Docs"
description: "Data Lake Store で Active Directory を使用して認証する方法について説明します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 35cde786bbc091c58f4dcb341cd47ce4c4f4b46c
ms.openlocfilehash: 02e52c3aba82ab8e3a8b1dc921731c29e505e23e


---
# <a name="service-to-serivce-authentication-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store での Azure Active Directory を使用したサービス間認証
> [!div class="op_single_selector"]
> * [サービス間認証](data-lake-store-authenticate-using-active-directory.md)
> * [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store または Azure Data Lake Analytics と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 
* サービス間認証 

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Store または Azure Data Lake Analytics に対するすべての要求にアタッチされます。

この記事では、サービス間認証用の Azure AD Web アプリケーションの作成方法について説明します。 エンドユーザー認証用に Azure AD アプリケーションを構成する方法については、「[End-user authentication with Data Lake Store using Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md)」 (Data Lake Store での Azure Active Directory を使用したエンドユーザー認証) を参照してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* サブスクリプション ID。 これは Azure ポータルから取得できます。 たとえば、[Data Lake Store アカウント] ブレードから入手できます。
  
    ![サブスクリプション ID の取得](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* Azure AD ドメイン名。 Azure ポータルの右上隅にマウスを置くことで取得できます。 次のスクリーンショットでは、ドメイン名は **contoso.microsoft.com** であり、丸かっこ内の GUID はテナント ID です。 
  
    ![AAD ドメインの取得](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="service-to-service-authentication"></a>サービス間認証
これは、アプリケーションで Azure AD を使用して自動的に認証する場合に推奨する方法であり、エンドユーザーが資格情報を提供する必要はありません。 アプリケーションは、資格情報が有効である限り自身を認証でき、年の単位で有効になるようにカスタマイズできます。

### <a name="what-do-i-need-to-use-this-approach"></a>この方法を使用するには何が必要か
* Azure AD ドメイン名。 これは、この記事の前提条件で既に示されています。
* Azure AD **Web アプリケーション**。
* Azure AD Web アプリケーションのクライアント ID。
* Azure AD Web アプリケーションのクライアント シークレット。
* Azure AD Web アプリケーションのトークン エンドポイント。
* 組み合わせて使用する Data Lake Store ファイル/フォルダーまたは Data Lake Analytics アカウントで、Azure AD Web アプリケーションへのアクセスを有効にします。

AD Web アプリケーションを作成し、上記の要件に合わせて構成する方法については、次の「[Active Directory アプリケーションを作成する](#create-an-active-directory-application)」を参照してください。

> [!NOTE]
> 既定では、Azure AD アプリケーションはクライアント シークレットを使用するように構成されます。シークレットは Azure AD アプリケーションから取得できます。 ただし、Azure AD アプリケーションで代わりに証明書を使用する場合は、「[Create a service principal with certificate (証明書を使用してサービス プリンシパルを作成する)](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)」の説明に従い、Azure PowerShell を使って Azure AD Web アプリケーションを作成する必要があります。
> 
> 

## <a name="create-an-active-directory-application"></a>Active Directory アプリケーションを作成する
このセクションでは、Azure Active Directory を使用して Azure Data Lake Store でのサービス間認証を行う Azure AD Web アプリケーションを作成および構成する方法について説明します。 

### <a name="step-1-create-an-azure-active-directory-application"></a>手順 1: Azure Active Directory アプリケーションを作成する
> [!NOTE]
> 以下の手順では、Azure Portal を使用します。 Azure AD アプリケーションは、[Azure PowerShell](../resource-group-authenticate-service-principal.md) または [Azure CLI](../resource-group-authenticate-service-principal-cli.md) を使用して作成することもできます。
> 
> 

1. [クラシック ポータル](https://manage.windowsazure.com/)で Azure アカウントにログインします。
2. 左側のペインで **[Active Directory]** を選択します。
   
     ![Active Directory の選択](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. 新しいアプリケーションの作成に使用する Active Directory を選択します。 Active Directory が複数ある場合、通常は、該当するサブスクリプションがあるディレクトリにアプリケーションを作成することをお勧めします。 アクセス権を付与できるリソースは、該当するサブスクリプションと同じディレクトリ内にあるアプリケーションのサブスクリプションに含まれるもののみです。  
   
     ![ディレクトリの選択](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. ディレクトリ内のアプリケーションを表示するには、 **[アプリケーション]**をクリックします。
   
     ![アプリケーションの表示](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. そのディレクトリにアプリケーションを作成したことがない場合、次の図のようなものが表示されます。 **[アプリケーションの追加]** をクリックします。
   
     ![[アプリケーションの追加]](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     または下のペインの **[追加]** をクリックします。
   
     ![追加](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. アプリケーションの名前を入力し、作成するアプリケーションの種類を選択します。 このチュートリアルでは、 **[Web アプリケーションや Web API]** を作成し、[次へ] をクリックします。
   
     ![アプリケーションの名前指定](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > 検索しやすくなるようなアプリケーションの名前を指定してください。 チュートリアルの後半で、このアプリケーションを検索して Data Lake Store アカウントに割り当てます。
    > 
    > 

7. アプリのプロパティを入力します。 **[サインオン URL]**には、アプリケーションについて説明する Web サイトの URI を指定します。 Web サイトの存在は検証されません。 
   **[アプリケーション ID/URI]**には、アプリケーションを識別する URI を指定します。
   
     ![アプリケーションのプロパティ](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    チェック マークをクリックしてウィザードを完了し、アプリケーションを作成します。

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>手順 2: クライアント ID、クライアント シークレット、トークン エンドポイントを取得する
プログラムによってログインするときは、アプリケーションの ID が必要です。 アプリケーションがその独自の資格情報で動作する場合は、さらに認証キーが必要となります。

1. **[構成]** タブをクリックし、アプリケーションのパスワードを構成します。
   
     ![アプリケーションの構成](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. **[クライアント ID]**の値をコピーします。
   
     ![[クライアント ID]](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. アプリケーションがその独自の資格情報で動作する場合は、下にスクロールして**[キー]** セクションを表示し、パスワードを有効にする期間を選択します。
   
     ![キー](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. **[保存]** を選択してキーを作成します。
   
    ![[保存]](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    保存されたキーが表示され、それをコピーすることができます。 キーを後から取得することはできないため、この時点でコピーしてください。
   
    ![保存されたキー](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. 次のように、画面の下部にある **[エンドポイントの表示]** を選択し、**[OAuth 2.0 トークン エンドポイント]** フィールドの値を取得して、トークン エンドポイントを取得します。  
   
    ![テナント ID](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>手順 3: Azure AD アプリケーションを Azure Data Lake Store アカウントのファイルまたはフォルダー (サービス間認証用のみ) に割り当てる
1. 新しい [Azure Portal](https://portal.azure.com) にサインオンし、上記で作成した Azure Active Directory アプリケーションに関連付ける Azure Data Lake Store アカウントを開きます。
2. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]**をクリックします。
   
    ![Data Lake Store アカウントにディレクトリを作成する](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Create directories in Data Lake account")
3. **[データ エクスプローラー]** ブレードで、Azure AD アプリケーションへのアクセスを付与するファイルまたはフォルダーをクリックし、**[アクセス]** をクリックします。 ファイルへのアクセスを構成する場合は、**[ファイルのプレビュー]** ブレードから **[アクセス]** をクリックします。
   
    ![Data Lake ファイル システムに ACL を設定する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Set ACLs on Data Lake file system")
4. **[アクセス]** ブレードには、既にルートに割り当てられている標準アクセスとカスタム アクセスが一覧表示されます。 **[追加]** アイコンをクリックして、カスタムレベルの ACL を追加します。
   
    ![標準アクセスとカスタム アクセスを一覧表示する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "List standard and custom access")
5. **[追加]** アイコンをクリックして、**[カスタム アクセスの追加]** ブレードを開きます。 このブレードの **[ユーザーまたはグループの選択]** をクリックし、**[ユーザーまたはグループの選択]** ブレードで、作成しておいた Azure Active Directory アプリケーションを探します。 検索対象のグループが多数存在する場合は、上部にあるテキスト ボックスを使用してグループ名をフィルター処理できます。 追加するグループをクリックして、 **[選択]**をクリックします。
   
    ![グループを追加する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Add a group")
6. **[アクセス許可の選択]** をクリックして、アクセス許可を選択するともに、このアクセス許可に既定の ACL、アクセス ACL、またはその両方のいずれを割り当てるか選択します。 **[OK]**をクリックします。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Assign permissions to group")
   
    Data Lake Store でのアクセス許可と既定/アクセス ACL の詳細については、「[Data Lake Store のアクセス制御](data-lake-store-access-control.md)」を参照してください。
7. **[カスタム アクセスの追加]** ブレードで **[OK]** をクリックします。 新しく追加されたグループは、関連付けられたアクセス許可と一緒に **[アクセス]** ブレードに一覧表示されます。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Assign permissions to group")    

## <a name="next-steps"></a>次のステップ
この記事では、Azure AD Web アプリケーションを作成し、.NET SDK、Java SDK などを使用して作成するクライアント アプリケーションに必要な情報を収集しました。これで、以下の記事に進むことができます。これらの記事では、Azure AD Web アプリケーションを使用して、最初に Data Lake Store で認証を行ってからストアで他の操作を実行する方法について説明しています。

* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
* [Java SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO5-->


