---
title: 'サービス間認証: Data Lake Store と Azure Active Directory | Microsoft Docs'
description: Data Lake Store での Azure Active Directory を使用したサービス間認証を行う方法について説明します
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 1e59ed093417d8761135b946e2fa3f183bb085c9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215973"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store での Azure Active Directory を使用したサービス間認証
> [!div class="op_single_selector"]
> * [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [サービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store と連携して動作するアプリケーションを作成するときは、Azure Active Directory (Azure AD) に対するアプリケーションの認証方法を事前に決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 
* サービス間認証 (この記事) 

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Store に対するすべての要求にアタッチされます。

この記事では、**サービス間認証用の Azure AD Web アプリケーション**の作成方法について説明します。 エンド ユーザー認証用に Azure AD アプリケーションを構成する方法については、「[Data Lake Store での Azure Active Directory を使用したエンド ユーザーの認証](data-lake-store-end-user-authenticate-using-active-directory.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="step-1-create-an-active-directory-web-application"></a>手順 1: Active Directory Web アプリケーションを作成する

Azure Active Directory を使用して Azure Data Lake Store でのサービス間認証を行う Azure AD Web アプリケーションを作成および構成する方法について説明します。 手順については、[Microsoft Azure での Ruby アプリケーションの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関するページを参照してください。

前出のリンクの指示に従うときは、次のスクリーンショットに示すように、アプリケーションの種類として **[Web アプリ/API]** を必ず選択してください。

![Web アプリの作成](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Web アプリの作成")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>手順 2: アプリケーション ID、認証キー、テナント ID を取得する
プログラムによってログインするときは、アプリケーションの ID が必要です。 アプリケーションがその独自の資格情報で動作する場合は、さらに認証キーが必要となります。

* アプリケーションのアプリケーション ID と認証キー (クライアント シークレットとも呼ばれる) を取得する方法については、「[アプリケーション ID と認証キーを取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)」を参照してください。

* テナント ID を取得する方法については、「[テナント ID を取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)」を参照してください。

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>手順 3: Azure AD アプリケーションを Azure Data Lake Store アカウントのファイルまたはフォルダーに割り当てる


1. [Azure Portal](https://portal.azure.com) にサインオンします。 上記で作成した Azure Active Directory アプリケーションに関連付ける Azure Data Lake Store アカウントを開きます。
2. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]** をクリックします。
   
    ![Data Lake Store アカウントにディレクトリを作成する](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Data Lake Store アカウントにディレクトリを作成する")
3. **[データ エクスプローラー]** ブレードで、Azure AD アプリケーションへのアクセスを付与するファイルまたはフォルダーをクリックし、**[アクセス]** をクリックします。 ファイルへのアクセスを構成する場合は、**[ファイルのプレビュー]** ブレードから **[アクセス]** をクリックします。
   
    ![Data Lake ファイル システムに ACL を設定する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Data Lake ファイル システムに ACL を設定する")
4. **[アクセス]** ブレードには、既にルートに割り当てられている標準アクセスとカスタム アクセスが一覧表示されます。 **[追加]** アイコンをクリックして、カスタムレベルの ACL を追加します。
   
    ![標準アクセスとカスタム アクセスを一覧表示する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "標準アクセスとカスタム アクセスを一覧表示する")
5. **[追加]** アイコンをクリックして、**[カスタム アクセスの追加]** ブレードを開きます。 このブレードの **[ユーザーまたはグループの選択]** をクリックし、**[ユーザーまたはグループの選択]** ブレードで、作成しておいた Azure Active Directory アプリケーションを探します。 検索対象のグループが多数存在する場合は、上部にあるテキスト ボックスを使用してグループ名をフィルター処理できます。 追加するグループをクリックして、 **[選択]** をクリックします。
   
    ![グループを追加する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "グループを追加する")
6. **[アクセス許可の選択]** をクリックして、アクセス許可を選択するともに、このアクセス許可に既定の ACL、アクセス ACL、またはその両方のいずれを割り当てるか選択します。 Click **OK**.
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "グループにアクセス許可を割り当てる")
   
    Data Lake Store でのアクセス許可と既定/アクセス ACL の詳細については、「[Data Lake Store のアクセス制御](data-lake-store-access-control.md)」を参照してください。
7. **[カスタム アクセスの追加]** ブレードで **[OK]** をクリックします。 新しく追加されたグループは、関連付けられたアクセス許可と一緒に **[アクセス]** ブレードに一覧表示されます。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "グループにアクセス許可を割り当てる")

> [!NOTE]
> Azure Active Directory アプリケーションを特定のフォルダーに制限する予定の場合は、その同じ Azure Active ディレクトリ アプリケーションに、root に対する**実行**アクセス許可も付与する必要があります。これにより、.NET SDK 経由でファイル作成アクセスが可能になります。

> [!NOTE]
> SDK を使用して Data Lake Store アカウントを作成する場合、その作成先となるリソース グループに Azure AD Web アプリケーションをロールとして割り当てる必要があります。
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>手順 4: OAuth 2.0 トークン エンドポイントを取得する (Java ベースのアプリケーションのみ)

1. [Azure Portal](https://portal.azure.com) にサインオンし、左ウィンドウの [Active Directory] をクリックします。

2. 左ウィンドウで、**[アプリの登録]** をクリックします。

3. [アプリの登録] ブレードの上部にある **[エンドポイント]** をクリックします。

    ![OAuth トークン エンドポイント](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth トークン エンドポイント")

4. エンドポイントの一覧から、Oauth 2.0 トークン エンドポイントをコピーします。

    ![OAuth トークン エンドポイント](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth トークン エンドポイント")   

## <a name="next-steps"></a>次の手順
この記事では、Azure AD Web アプリケーションを作成し、.NET SDK、Java、Python、REST API などを使用して作成するクライアント アプリケーションに必要な情報を収集しました。これで、以下の記事に進むことができます。これらの記事では、Azure AD ネイティブ アプリケーションを使用して、Data Lake Store に対し、まず認証を行ってからその他の操作を実行する方法について説明しています。

* [Data Lake Store に対する Java を使用したサービス間認証](data-lake-store-service-to-service-authenticate-java.md)
* [Data Lake Store に対する .NET SDK を使用したサービス間認証](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Data Lake Store に対する Python を使用したサービス間認証](data-lake-store-service-to-service-authenticate-python.md)
* [Data Lake Store に対する REST API を使用したサービス間認証](data-lake-store-service-to-service-authenticate-rest-api.md)


