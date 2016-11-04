---
title: Active Directory を使用して Data Lake Store を認証する | Microsoft Docs
description: Data Lake Store で Active Directory を使用して認証する方法について説明します
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme

---
# <a name="authenticate-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store で Azure Active Directory を使用して認証する
Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store または Azure Data Lake Analytics と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 
* サービス間認証 

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Store または Azure Data Lake Analytics に対するすべての要求にアタッチされます。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* サブスクリプション ID。 これは Azure ポータルから取得できます。 たとえば、[Data Lake Store アカウント] ブレードから入手できます。
  
    ![サブスクリプション ID の取得](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* Azure AD ドメイン名。 Azure ポータルの右上隅にマウスを置くことで取得できます。 
  
    ![AAD ドメインの取得](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>エンドユーザー認証
これは、エンドユーザーに Azure AD 経由でアプリケーションにログインしてもらう場合に推奨する方法です。 アプリケーションは、ログインしたエンド ユーザーと同じアクセス レベルで Azure リソースにアクセスできます。 エンド ユーザーは、アプリケーションのアクセスを維持するために、資格情報を定期的に入力する必要があります。

エンドユーザーがログインすると、アクセス トークンと更新トークンがアプリケーションに与えられます。 アクセス トークンは Data Lake Store または Data Lake Analytics に対するすべての要求にアタッチされ、既定では 1 時間有効です。 更新トークンは、新しいアクセス トークンを取得するために使用でき、定期的に使用されるのであれば、既定では最大 2 週間有効です。 エンド ユーザーのログインには、2 つの異なる方法を使用できます。

### <a name="using-the-oauth-2.0-pop-up"></a>OAuth 2.0 ポップアップの使用
アプリケーションで、エンドユーザーが資格情報を入力できる OAuth 2.0 認証ポップアップをトリガーできます。 このポップアップは、必要であれば、Azure AD の 2 要素認証 (2FA) プロセスでも機能します。 

> [!NOTE]
> この方法は、Python または Java 用の Azure AD Authentication Library (ADAL) ではまだサポートされていません。
> 
> 

### <a name="directly-passing-in-user-credentials"></a>ユーザーの資格情報を直接渡す
アプリケーションで、ユーザーの資格情報を Azure AD に直接提供できます。 この方法は、組織 ID ユーザー アカウントのみで機能します。@outlook.com や @live.com. で終わる個人/"Live ID" ユーザー アカウントには対応しません。さらに、この方法は、Azure AD の 2 要素認証 (2FA) を必要とするユーザー アカウントには対応しません。

### <a name="what-do-i-need-to-use-this-approach?"></a>この方法を使用するには何が必要か
* Azure AD ドメイン名 (この記事の前提条件で既に示されています)。
* Azure AD **ネイティブ クライアント アプリケーション**。 
* Azure AD ネイティブ クライアント アプリケーションのクライアント ID。
* Azure AD ネイティブ クライアント アプリケーションのリダイレクト URI。 

Azure AD アプリケーションの作成およびクライアント ID の取得方法については、 [Active Directory アプリケーションの作成に関する記事](../resource-group-create-service-principal-portal.md#create-an-active-directory-application)を参照してください。 

> [!NOTE]
> 上記のリンクの手順は、Azure AD Web アプリケーションのためのものです。 ネイティブ クライアント アプリケーションを作成する場合でも、手順はまったく同じです。
> 
> 

## <a name="service-to-service-authentication"></a>サービス間認証
これは、アプリケーションで Azure AD を使用して自動的に認証する場合に推奨する方法であり、エンドユーザーが資格情報を提供する必要はありません。 アプリケーションは、資格情報が有効である限り自身を認証でき、年の単位で有効になるようにカスタマイズできます。

### <a name="what-do-i-need-to-use-this-approach?"></a>この方法を使用するには何が必要か
* Azure AD ドメイン名 (この記事の前提条件で既に示されています)。
* Azure AD **Web アプリケーション**。
* Azure AD Web アプリケーションのクライアント ID。
  
  > [!NOTE]
  > Azure AD アプリケーションの作成およびクライアント ID の取得方法については、 [Active Directory アプリケーションの作成に関する記事](../resource-group-create-service-principal-portal.md#create-an-active-directory-application)を参照してください。
  > 
  > 
* クライアント シークレットまたは証明書を使用するように Azure AD Web アプリケーションを構成します。 証明書を使用して Web アプリケーションを作成するには、「 [証明書を使用したサービス プリンシパルの作成](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)」を参照してください。
* 組み合わせて使用する Data Lake Store ファイル/フォルダーまたは Data Lake Analytics アカウントで、Azure AD Web アプリケーションへのアクセスを有効にします。 Azure AD アプリケーションへのアクセスを Data Lake Store ファイル/フォルダーに指定する方法については、「 [ユーザーまたはセキュリティ グループを ACL として Azure Data Lake Store ファイル システムに割り当てる](data-lake-store-secure-data.md#filepermissions)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
* [Java SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-java-sdk.md)

<!--HONumber=Oct16_HO2-->


