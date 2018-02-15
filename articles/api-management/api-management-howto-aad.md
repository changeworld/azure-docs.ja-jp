---
title: "Azure Active Directory を使用して開発者アカウントを認証する - Azure API Management | Microsoft Docs"
description: "API Management で Azure Active Directory を使用してユーザーを認証する方法について説明します。"
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Azure API Management で Azure Active Directory を使用して開発者アカウントを認証する方法

このガイドでは、Azure Active Directory 内のユーザーに対して開発者ポータルへのアクセスを有効にする方法について説明します。 また、Azure Active Directory のユーザーが含まれた外部グループを追加することで Azure Active Directory ユーザーのグループを管理する方法についても説明します。

> [!WARNING]
> Azure Active Directory 統合は、[Developer、Standard、および Premium](https://azure.microsoft.com/pricing/details/api-management/) の各レベルでのみ使用可能です。

## <a name="prerequisites"></a>前提条件

- [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
- API Management インスタンスをインポートして発行します。 詳細については、[インポートと発行](import-and-publish.md)に関するページを参照してください。

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Azure Active Directory を使用して開発者アカウントを認証する方法

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. elect ![矢印](./media/api-management-howto-aad/arrow.png)が必要です。
3. 検索ボックスに、「api」と入力します。
4. **[API Management サービス]** をクリックします。
5. APIM サービス インスタンスを選びます。
6. **[セキュリティ]** の **[ID]** を選択します。

    ![[外部 ID]](./media/api-management-howto-aad/api-management-with-aad001.png)
7. 一番上にある **[+追加]** をクリックします。

    **[ID プロバイダーの追加]** ウィンドウが右側に表示されます。
8. **[プロバイダーの種類]** で **[Azure Active Directory]** を選択します。

    その他必要な情報を入力するためのコントロールがウィンドウに表示されます。 そのようなコントロールとして、**[クライアント ID]** や **[クライアント シークレット]** があります (必要な情報は、後でこのチュートリアルの中で取得します)。
9. **[リダイレクト URL]** をメモします。  
10. ブラウザーで別のタブを開きます。 
11. [Azure Portal](https://portal.azure.com) を開きます。
12. elect ![矢印](./media/api-management-howto-aad/arrow.png)が必要です。
13. 「active」と入力して、**Azure Active Directory** を表示します。
14. **[Azure Active Directory]**を選択します。
15. **[管理]** の **[アプリの登録]** を選択します。

    ![アプリの登録](./media/api-management-howto-aad/api-management-with-aad002.png)
16. **[新しいアプリケーションの登録]** をクリックします。

    **[作成]** ウィンドウが右側に表示されます。 ここに AAD アプリの関連情報を入力します。
17. アプリケーションの名前を入力します。
18. アプリケーション タイプとして **[Web アプリ/API]** を選択します。
19. [サインオン URL] には、開発者ポータルのサインオン URL を入力します。 この例では、https://apimwithaad.portal.azure-api.net/signin がサインオン URL です。
20. **[作成]** をクリックして、アプリケーションを作成します。
21. アプリを検索するには、**[アプリの登録]** を選択して名前で検索します。

    ![アプリの登録](./media/api-management-howto-aad/find-your-app.png)
22. アプリケーションの登録後、**[応答 URL]** に移動し、[リダイレクト URL] が、手順 9. でメモした値に設定されていることを確認します。 
23. アプリケーションを構成する場合 (**アプリ ID の URL** を変更するなど)、**[プロパティ]** を選択します。

    ![アプリの登録](./media/api-management-howto-aad/api-management-with-aad004.png)

    このアプリケーションで複数の Azure Active Directory を使用する場合は、**[アプリケーションはマルチテナントです]** で **[はい]** をクリックします。 既定値は **[いいえ]**です。
24. **[必要なアクセス許可]** を選択して、アプリケーションのアクセス許可を設定します。
25. アプリケーションを選択し、**[ディレクトリ データの読み取り]** と **[サインインとユーザー プロファイルの読み取り]** のチェック ボックスをオンにします。

    ![アプリの登録](./media/api-management-howto-aad/api-management-with-aad005.png)

    アプリケーションと委任されたアクセス許可の詳細については、「[Graph API にアクセスする][Accessing the Graph API]」を参照してください。
26. 左側のウィンドウで、**[アプリケーション ID]** の値をコピーします。

    ![アプリの登録](./media/api-management-howto-aad/application-id.png)
27. API Management アプリケーションに戻ります。 **[ID プロバイダーの追加]** ウィンドウが表示されます。 <br/>**[クライアント ID]** ボックスに **[アプリケーション ID]** の値を貼り付けます。
28. Azure Active Directory の構成に戻り、**[キー]** をクリックします。
29. 名前と期間を指定して新しいキーを作成します。 
30. **[Save]** をクリックします。 キーが生成されます。

    キーをクリップボードにコピーします。

    ![アプリの登録](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > このキーを書き留めておきます。 Azure Active Directory の構成ウィンドウを閉じると、キーは再表示できません。
    > 
    > 
31. API Management アプリケーションに戻ります。 <br/>**[ID プロバイダーの追加]** ウィンドウの **[クライアント シークレット]** ボックスにキーを貼り付けます。
32. **[ID プロバイダーの追加]** ウィンドウの **[許可されているテナント]** ボックスで、API Management サービス インスタンスの API にアクセスできるディレクトリを指定します。 <br/>アクセス許可を付与する Azure Active Directory インスタンスのドメインを指定します。 複数のドメインを指定する場合は、改行文字、スペース、またはコンマで区切ります。

    **[許可されたテナント]** セクションには、複数のドメインを指定できます。 アプリケーションが登録されている元のドメインとは別のドメインからユーザーがログインするには、別のドメインの全体管理者がアプリケーションにディレクトリ データへのアクセス許可を付与する必要があります。 アクセス許可を付与するには、全体管理者は `https://<URL of your developer portal>/aadadminconsent` (たとえば、https://contoso.portal.azure-api.net/aadadminconsent) に移動し、アクセス許可する Active Directory テナントのドメイン名を入力して、[送信] をクリックします。 次の例では、`miaoaad.onmicrosoft.com` の全体管理者がこの特定の開発者ポータルの権限を付与しようとしています。 

33. 必要な構成を指定したら、**[追加]** をクリックします。

    ![アプリの登録](./media/api-management-howto-aad/api-management-with-aad007.png)

変更が保存さたら、指定された Azure Active Directory 内のユーザーは、「[Azure Active Directory アカウントを使用して開発者ポータルにログインする方法](#log_in_to_dev_portal)」の手順に従って開発者ポータルにサインインできます。

![アクセス許可](./media/api-management-howto-aad/api-management-aad-consent.png)

次の画面で、アクセス許可の付与を確認するメッセージが全体管理者に表示されます。 

![アクセス許可](./media/api-management-howto-aad/api-management-permissions-form.png)

全体管理者がアクセス許可を付与する前に全体管理者以外のユーザーがログインしようとすると、ログインに失敗し、エラー画面が表示されます。

## <a name="how-to-add-an-external-azure-active-directory-group"></a>外部の Azure Active Directory グループを追加する方法

Azure Active Directory 内のユーザーのアクセスを有効にした後は、Azure Active Directory のグループを API Management に追加することで、グループ内の開発者と目的の成果物の関連付けを管理しやすくすることができます。

外部の Azure Active Directory グループを構成するには、先に前のセクションの手順を実行して、[外部 ID] タブで Azure Active Directory を構成する必要があります。 

API Management インスタンスの **[グループ]** タブから、外部の Azure Active Directory グループが追加されます。

![グループ](./media/api-management-howto-aad/api-management-with-aad008.png)

1. **[グループ]** タブを選択します。
2. **[AAD グループの追加]** ボタンをクリックします。
3. 追加するグループを選択します。
4. **[選択]** ボタンを押します。

Azure Active Directory グループの作成後、追加された外部グループのプロパティを確認して構成するには、**[グループ]** タブでグループの名前をクリックします。

ここでは、グループの **[名前]** と **[説明]** を編集できます。
 
構成した Azure Active Directory のユーザーは、開発者ポータルにサインインし、次のセクションの指示に従って、可視性を有効化されたグループの表示とサブスクライブを実行できます。

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Azure Active Directory アカウントを使用して開発者ポータルにログインする方法

前のセクションで構成した Azure Active Directory アカウントを使用して開発者ポータルにログインするには、Active Directory のアプリケーション構成の**サインオン URL** を使用して新しいブラウザー ウィンドウを開き、**[Azure Active Directory]** をクリックします。

![開発者ポータル][api-management-dev-portal-signin]

Azure Active Directory 内のいずれかのユーザーの資格情報を入力し、 **[サインイン]**をクリックします。

![[サインイン]][api-management-aad-signin]

追加情報が必要な場合は、登録フォームが表示されることがあります。 登録フォームに入力し、 **[サインアップ]**をクリックします。

![登録][api-management-complete-registration]

ユーザーが API Management サービス インスタンスの開発者ポータルにログインしました。

![登録の完了][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
