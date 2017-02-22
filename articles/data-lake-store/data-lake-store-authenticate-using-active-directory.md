---
title: "サービス間認証: Data Lake Store と Azure Active Directory | Microsoft Docs"
description: "Data Lake Store での Azure Active Directory を使用したサービス間認証を行う方法について説明します"
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
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store での Azure Active Directory を使用したサービス間認証
> [!div class="op_single_selector"]
> * [サービス間認証](data-lake-store-authenticate-using-active-directory.md)
> * [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store または Azure Data Lake Analytics と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 
* サービス間認証 (この記事) 

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Store または Azure Data Lake Analytics に対するすべての要求にアタッチされます。

この記事では、サービス間認証用の Azure AD Web アプリケーションの作成方法について説明します。 エンドユーザー認証用に Azure AD アプリケーションを構成する方法については、「[End-user authentication with Data Lake Store using Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md)」 (Data Lake Store での Azure Active Directory を使用したエンドユーザー認証) を参照してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="create-an-active-directory-application"></a>Active Directory アプリケーションを作成する
このセクションでは、Azure Active Directory を使用して Azure Data Lake Store でのサービス間認証を行う Azure AD Web アプリケーションを作成および構成する方法について説明します。 "Active Directory アプリケーション" を作成すると、サービス プリンシパルが作成されます。アプリやコードが実際に作成されるわけではないことに注意してください。

### <a name="step-1-create-an-azure-active-directory-application"></a>手順 1: Azure Active Directory アプリケーションを作成する
1. [クラシック ポータル](https://manage.windowsazure.com/)で Azure アカウントにログインします。
2. 左側のペインで **[Active Directory]** を選択します。
   
     ![Active Directory の選択](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. 新しいアプリケーションの作成に使用する Active Directory を選択します。 Active Directory が複数ある場合、通常は、該当するサブスクリプションがあるディレクトリにアプリケーションを作成することをお勧めします。 アクセス権を付与できるリソースは、該当するサブスクリプションと同じディレクトリ内にあるアプリケーションのサブスクリプションに含まれるもののみです。  
   
     ![ディレクトリの選択](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. ディレクトリ内のアプリケーションを表示するには、 **[アプリケーション]**をクリックします。
   
     ![アプリケーションの表示](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. そのディレクトリにアプリケーションを作成したことがない場合、次の画像のように表示されます。 **[アプリケーションの追加]** をクリックします。
   
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

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>次の手順で必要となる次のプロパティをメモしておきます。
1. 上記の手順 1.6. で作成した Web アプリケーション ID の名前
2. 上記の手順 2.2 で取得したクライアント ID
3. 上記の手順 2.4 で作成したキー
4. 上記の手順 2.5 で取得したテナント ID

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>手順 3: Azure AD アプリケーションを Azure Data Lake Store アカウントのファイルまたはフォルダー (サービス間認証用のみ) に割り当てる
1. 新しい [Azure Portal](https://portal.azure.com) にサインオンし、上記で作成した Azure Active Directory アプリケーションに関連付ける Azure Data Lake Store アカウントを開きます。
2. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]**をクリックします。
   
    ![Data Lake Store アカウントにディレクトリを作成する](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Data Lake Store アカウントにディレクトリを作成する")
3. **[データ エクスプローラー]** ブレードで、Azure AD アプリケーションへのアクセスを付与するファイルまたはフォルダーをクリックし、**[アクセス]** をクリックします。 ファイルへのアクセスを構成する場合は、**[ファイルのプレビュー]** ブレードから **[アクセス]** をクリックします。
   
    ![Data Lake ファイル システムに ACL を設定する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Data Lake ファイル システムに ACL を設定する")
4. **[アクセス]** ブレードには、既にルートに割り当てられている標準アクセスとカスタム アクセスが一覧表示されます。 **[追加]** アイコンをクリックして、カスタムレベルの ACL を追加します。
   
    ![標準アクセスとカスタム アクセスを一覧表示する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "標準アクセスとカスタム アクセスを一覧表示する")
5. **[追加]** アイコンをクリックして、**[カスタム アクセスの追加]** ブレードを開きます。 このブレードの **[ユーザーまたはグループの選択]** をクリックし、**[ユーザーまたはグループの選択]** ブレードで、作成しておいた Azure Active Directory アプリケーションを探します。 検索対象のグループが多数存在する場合は、上部にあるテキスト ボックスを使用してグループ名をフィルター処理できます。 追加するグループをクリックして、 **[選択]**をクリックします。
   
    ![グループを追加する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "グループを追加する")
6. **[アクセス許可の選択]** をクリックして、アクセス許可を選択するともに、このアクセス許可に既定の ACL、アクセス ACL、またはその両方のいずれを割り当てるか選択します。 **[OK]**をクリックします。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "グループにアクセス許可を割り当てる")
   
    Data Lake Store でのアクセス許可と既定/アクセス ACL の詳細については、「[Data Lake Store のアクセス制御](data-lake-store-access-control.md)」を参照してください。
7. **[カスタム アクセスの追加]** ブレードで **[OK]** をクリックします。 新しく追加されたグループは、関連付けられたアクセス許可と一緒に **[アクセス]** ブレードに一覧表示されます。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "グループにアクセス許可を割り当てる")    

## <a name="next-steps"></a>次のステップ
この記事では、Azure AD Web アプリケーションを作成し、.NET SDK、Java SDK などを使用して作成するクライアント アプリケーションに必要な情報を収集しました。これで、以下の記事に進むことができます。これらの記事では、Azure AD Web アプリケーションを使用して、最初に Data Lake Store で認証を行ってからストアで他の操作を実行する方法について説明しています。

* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
* [Java SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-java-sdk.md)

この記事では、アプリケーションに対してユーザー プリンシパルを実行するうえで必要な基本的な手順を説明しました。 さらに詳しい情報については、次の記事をご覧ください。
* [PowerShell を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [サービス プリンシパル認証に証明書認証を使用する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [他の方法で Azure AD に対して認証する](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)





<!--HONumber=Jan17_HO4-->


