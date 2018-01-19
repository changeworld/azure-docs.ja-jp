---
title: "Azure Active Directory とアプリケーションの統合"
description: "Azure Active Directory (Azure AD) でアプリケーションを追加、更新、または削除する方法。"
services: active-directory
documentationcenter: 
author: PatAltimore
manager: mtillman
editor: mbaldwin
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: e398536ff6f660c75e4e063040eab33a831d65c6
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="integrating-applications-with-azure-active-directory"></a>Azure Active Directory とアプリケーションの統合
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

エンタープライズ開発者とサービスとしてのソフトウェア (SaaS) プロバイダーは、自社のサービスのセキュリティで保護されたサインインと承認を実現するために、Azure Active Directory (Azure AD) と統合できる商用クラウド サービスまたは基幹業務アプリケーションを開発できます。 アプリケーションまたはサービスを Azure AD と統合するには、まず開発者がアプリケーションを Azure AD に登録しておく必要があります。

この記事では、Azure AD でアプリケーションの登録を追加、更新、または削除する方法について説明します。 また、Azure AD と統合できるさまざまな種類のアプリケーションや、他のリソース (Web API など) にアクセスするようにアプリケーションを構成する方法などについても説明します。

登録されたアプリケーションおよびそれらの間の関係を表す 2 つの Azure AD オブジェクトの詳細については、[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](active-directory-application-objects.md)に関するページを参照してください。Azure Active Directory でアプリケーションを開発するときに使用するブランド化ガイドラインの詳細については、[統合アプリケーションのブランド化に関するガイドライン](active-directory-branding-guidelines.md)を参照してください。

## <a name="adding-an-application"></a>アプリケーションの追加
Azure AD の機能を使用するアプリケーションは、まず Azure AD テナントに登録する必要があります。 この登録プロセスでは、アプリケーションが配置されている URL、ユーザーの認証後の応答の送信先となる URL、アプリケーションを識別する URI など、アプリケーションの詳細を Azure AD に提供します。

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Azure Portal を使用して新しいアプリケーションを登録するには
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数にアクセスできる場合は、右上隅でアカウントをクリックし、ポータル セッションを目的の Azure AD テナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]**、**[新しいアプリケーションの登録]** の順にクリックします。

   ![新しいアプリケーションの登録](./media/active-directory-integrating-applications/add-app-registration.png)

4. **[作成]** ページが表示されたら、アプリケーションの登録情報を入力します。 

  - **名前:** わかりやすいアプリケーション名を入力します
  - **アプリケーションの種類:** 
    - デバイスにローカルでインストールされた[クライアント アプリケーション](active-directory-dev-glossary.md#client-application)については、"ネイティブ" を選択します。 この設定は OAuth のパブリック [ネイティブ クライアント](active-directory-dev-glossary.md#native-client)に使用されます。
    - セキュリティで保護されたサーバーにインストールされた[クライアント アプリケーション](active-directory-dev-glossary.md#client-application)および[リソース/API アプリケーション](active-directory-dev-glossary.md#resource-server)については、"Web アプリ/API" を選択します。 この設定は、OAuth Confidential [Web クライアント](active-directory-dev-glossary.md#web-client)とパブリック [ユーザー エージェント ベースのクライアント](active-directory-dev-glossary.md#user-agent-based-client)で使用されます。 また、同じアプリケーションで、クライアントとリソース/API の両方を公開することもできます。
  - **サインオン URL:** "Web アプリ/API" アプリケーションの場合は、アプリのベース URL を入力します。 たとえば、`http://localhost:31544` は、ローカル マシンで実行されている Web アプリの URL である可能性があります。 ユーザーは、この URL を使用して、Web クライアント アプリケーションにサインインします。 
  - **リダイレクト URI:** "ネイティブ" アプリケーションの場合は、Azure AD がトークン応答を返すために使用する URI を入力します。 アプリケーション固有の値を入力します (`http://MyFirstAADApp` など)

   ![新しいアプリケーションの登録 - 作成](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Web アプリケーションまたはネイティブ アプリケーションの具体的な例については、[クイック スタート](active-directory-developers-guide.md#get-started)をご覧ください。

5. 完了したら、**[作成]** をクリックします。 Azure AD により一意のアプリケーション ID がアプリケーションに割り当てられ、アプリケーションのメインの登録ページが表示されます。 アプリケーションが Web アプリケーションとネイティブ アプリケーションのどちらであるかに応じて、アプリケーションに機能を追加するためのさまざまなオプションが表示されます。 同意の概要と、アプリケーション登録で追加の構成機能 (資格情報、アクセス許可、他のテナントからのユーザー サインインの有効化) を有効にする方法の詳細については、次のセクションを参照してください。

  > [!NOTE]
  > 既定では、新しく登録されたアプリケーションは、同じテナントのユーザー**のみ**がアプリケーションにサインインできるように構成されます。
  > 
  > 

## <a name="updating-an-application"></a>アプリケーションの更新
アプリケーションを Azure AD に登録したら、Web API にアクセスできるようにしたり、他の組織で使用できるようにしたりするために、アプリケーションを更新することが必要な場合があります。 このセクションでは、アプリケーションの各種構成方法について説明します。 最初に、同意フレームワークの概要について説明します。他のユーザーまたはアプリケーションが使用する必要があるアプリケーションを構築する場合は、これを理解しておくことが重要です。

### <a name="overview-of-the-consent-framework"></a>同意フレームワークの概要

Azure AD の同意フレームワークを使用すると、多層アプリケーションなど、マルチテナントの Web クライアント アプリケーションやネイティブ クライアント アプリケーションの開発が容易になります。 こうしたアプリケーションでは、アプリケーションが登録されている Azure AD テナントとは異なるテナントから、ユーザー アカウントでサインインできます。 また、独自の Web API だけでなく、Microsoft Graph API (Azure Active Directory、Intune、および Office 365 のサービスへのアクセスに使用)、その他の Microsoft サービス API など Web API にもアクセスする必要があります。 このフレームワークは、ディレクトリ データへのアクセスを必要とする場合がある、ディレクトリへの登録を要求するアプリケーションに同意するユーザーまたは管理者に基づいています。

たとえば、Web クライアント アプリケーションで Office 365 からユーザーに関するカレンダー情報を読み取る必要がある場合、そのユーザーは、最初にそのクライアント アプリケーションに同意する必要があります。 ユーザーが同意すると、クライアント アプリケーションはユーザーに代わって Microsoft Graph API を呼び出し、必要に応じてカレンダー情報を使用できるようになります。 [Microsoft Graph API](https://graph.microsoft.io) により、Office 365 のデータ (Exchange の予定表とメッセージ、SharePoint のサイトとリスト、OneDrive のドキュメント、OneNote のノートブック、Planner のタスク、Excel のブックなど) のほか、Azure AD のユーザーとグループ、Microsoft Cloud Services のその他のデータ オブジェクトにアクセスできます。 

同意フレームワークは、OAuth 2.0 と、パブリック クライアントまたは秘密のクライアントを使用した各種フロー (認証コードの付与やクライアント資格情報の付与など) を基盤としています。 Azure AD は、OAuth 2.0 を使用することで、スマートフォン、タブレット、サーバーなどで実行されるさまざまな種類のクライアント アプリケーションや Web アプリケーションを構築し、必要なリソースにアクセスすることを可能にします。

OAuth 2.0 承認付与における同意フレームワークの使用の詳細については、[OAuth 2.0 と Azure AD を使用した Web アプリケーションへのアクセスの承認](active-directory-protocols-oauth-code.md)に関するページと、「[Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」を参照してください。 また、Microsoft Graph 経由での Office 365 への承認アクセスの取得については、「[Microsoft Graph でのアプリ認証](https://graph.microsoft.io/docs/authorization/auth_overview)」を参照してください。

#### <a name="example-of-the-consent-experience"></a>同意エクスペリエンスの例

次の手順は、アプリケーション開発者とユーザーに対して、同意エクスペリエンスがどのように機能するかを示しています。

1. リソース/API にアクセスするために特定のアクセス許可を要求する必要がある Web クライアント アプリケーションがあると仮定します。 この構成を行う方法については次のセクションで説明しますが、実質的には、Azure Portal を使用して、構成時にアクセス許可要求が宣言されます。 他の構成設定と同様、これはアプリケーションの Azure AD 登録の一部になります。
   
  ![他のアプリケーションに対するアクセス許可](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. たとえば、アプリケーションのアクセス許可の更新後、アプリケーションが実行され、ユーザーがアプリケーションを初めて使用しようとしているとします。 最初に、アプリケーションは Azure AD の `/authorize` エンドポイントから承認コードを取得する必要があります。 その承認コードを使用して、新しいアクセスおよび更新トークンを取得できます。

3. ユーザーがまだ認証されていない場合、Azure AD の `/authorize` エンドポイントによってサインインするよう求められます。
   
  ![ユーザーまたは管理者による Azure AD へのサインイン](./media/active-directory-integrating-applications/usersignin.png)

4. ユーザーがサインインすると、ユーザーに同意ページを表示する必要があるかどうかが Azure AD により判別されます。 この判断は、ユーザー (または組織の管理者) がアプリケーションに既に同意しているかどうかに基づいています。 まだ同意していない場合、Azure AD によりユーザーに同意を求めるメッセージが表示され、アプリケーションが機能するために必要なアクセス許可が表示されます。 同意ダイアログに表示される一連のアクセス許可は、Azure Portal の [委任されたアクセス許可] で選択したものと一致します。
   
  ![ユーザーの同意エクスペリエンス](./media/active-directory-integrating-applications/consent.png)

5. ユーザーが同意すると、アプリケーションに認証コードが返されます。この認証コードを使用して、アクセス トークンと更新トークンを取得します。 このフローの詳細については、「Azure AD の認証シナリオ」の「[Web アプリケーション対 Web API](active-directory-authentication-scenarios.md#web-application-to-web-api)」を参照してください。

6. 管理者は、テナントのすべてのユーザーに代わって、アプリケーションの委任されたアクセス許可に同意できます。 管理者が同意すると、テナントの各ユーザーに同意ダイアログが表示されなくなります。この操作は、[Azure Portal](https://portal.azure.com) のアプリケーション ページで行います。 アプリケーションの **[設定]** ページで、**[必要なアクセス許可]**、**[アクセス許可の付与]** ボタンの順にクリックします。 

  ![明示的な管理者の同意としてアクセス許可を付与する](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > 現時点では、ADAL.js を使用するシングルページ アプリ (SPA) では、**[アクセス許可の付与]** ボタンを使用して明示的に同意を付与する必要があります。 これを行わないと、アクセス トークンが要求されたときに、アプリケーションでエラーが発生します。   

### <a name="configure-a-client-application-to-access-web-apis"></a>Web API にアクセスするためのクライアント アプリケーションの構成
Web/confidential クライアント アプリケーションが認証を必要とする承認付与フローに参加 (およびアクセス トークンを取得) できるようにするには、セキュリティで保護された資格情報を確立する必要があります。 Azure Portal でサポートされている既定の認証方法は、クライアント ID と秘密鍵の組み合わせです。 このセクションでは、秘密キーにクライアントの資格情報を提供するのに必要な構成手順について説明します。

さらに、クライアント アプリケーションがリソース アプリケーションによって公開されている Web API (Microsoft Graph API など) にアクセスする前に、同意フレームワークにより、要求されたアクセス許可に基づいてクライアントが必要なアクセス許可を取得することが保証されます。 既定では、すべてのアプリケーションが、"Windows Azure Active Directory" (Graph API) および "Windows Azure Service Management API" からアクセス許可を選択できます。 また、[Graph API の "サインインとユーザー プロファイルの読み取り" アクセス許可](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails)も既定で選択されています。 クライアントがテナントに登録されており、そこでアカウントが Office 365 に登録されている場合は、SharePoint Online および Exchange Online に対する Web API とアクセス許可を選択できます。 目的の Web API ごとに [2 種類のアクセス許可](active-directory-dev-glossary.md#permissions)から選択できます。

- アプリケーションのアクセス許可: クライアント アプリケーションは、(ユーザー コンテキストなしで) アプリケーションとして Web API に直接アクセスする必要があります。 この種類のアクセス許可には管理者の同意が必要であり、ネイティブ クライアント アプリケーションでは使用できません。

- 委任されたアクセス許可: クライアント アプリケーションは、サインインしているユーザーとして Web API にアクセスする必要がありますが、選択したアクセス許可によってアクセスが制限されます。 この種類のアクセス許可は、管理者の同意が要求されない限り、ユーザーが付与できます。 

  > [!NOTE]
  > 委任されたアクセス許可をアプリケーションに追加しても、テナント内のユーザーに同意が自動的に付与されるわけではありません。 委任されたアクセス許可が追加されたら、ユーザーは引き続き実行時に手動で同意する必要があります。ただし、管理者が Azure Portal のアプリケーション ページで **[必要なアクセス許可]** セクションの **[アクセス許可の付与]** をクリックした場合は、同意ダイアログが表示されなくなります。 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Web API にアクセスするためのアプリケーションの資格情報またはアクセス許可を追加するには
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数にアクセスできる場合は、右上隅でアカウントをクリックし、ポータル セッションを目的の Azure AD テナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。

   ![アプリケーションの登録の更新](./media/active-directory-integrating-applications/update-app-registration.png)

4. アプリケーションのメインの登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。 Web アプリケーションの資格情報の秘密鍵を追加するには、次の操作を行います。
  - **[設定]** ページの **[キー]** セクションをクリックします。  
  - キーの説明を追加します。
  - 期間として 1 年または 2 年を選択します。
  - **[Save]** をクリックします。 構成の変更を保存すると、右端の列にキーの値が格納されます。 クライアント アプリケーション コードで使用できるように、**必ずキーをコピーしてください**。このページを一度閉じるとキーにはアクセスできなくなります。

  ![アプリケーションの登録の更新 - キー](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. アクセス許可を追加して、クライアントからリソース API にアクセスするには
  - **[設定]** ページの **[必要なアクセス許可]** セクションをクリックします。 
  - **[Add] \(追加)** ボタンをクリックします。
  - **[API を選択します]** をクリックし、選択するリソースの種類を選択します。
  - 利用可能な API の一覧を確認するか、検索ボックスを使用して、ディレクトリ内の、Web API を公開している利用可能なリソース アプリケーションの中から選択できます。 目的のリソースをクリックし、**[選択]** をクリックします。
  - **[アクセスの有効化]** ページが表示されます。 API にアクセスするときにアプリケーションに必要な [アプリケーションのアクセス許可]/[委任されたアクセス許可] を選択します。
   
  ![アプリケーションの登録の更新 - アクセス許可 API](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![アプリケーションの登録の更新 - アクセス許可](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. 完了したら、**[アクセスの有効化]** ページで **[選択]** をクリックし、**[API アクセスの追加]** ページ で **[実行]** をクリックします。 **[必要なアクセス許可]** ページが表示されます。このページで、新しいリソースが API の一覧に追加されます。

  > [!NOTE]
  > **[完了]** ボタンをクリックすると、構成済みの他のアプリケーションに対するアクセス許可に基づいて、ディレクトリ内のアプリケーションのアクセス許可も自動的に設定されます。  このアプリケーションのアクセス許可は、アプリケーションの **[設定]** ページで確認できます。
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Web API を公開するためのリソース アプリケーションの構成

Web API を開発し、アクセス [スコープ](active-directory-dev-glossary.md#scopes)および[ロール](active-directory-dev-glossary.md#roles)を公開することによってクライアント アプリケーションで使用できるようにすることができます。 適切に構成された Web API は、Graph API や Office 365 API など、他の Microsoft Web API と同様に使用できます。 アクセス スコープおよびロールを公開するには、[アプリケーションのマニフェスト](active-directory-dev-glossary.md#application-manifest)を使用します。アプリケーション マニフェストは、アプリケーションの ID 構成を示す JSON ファイルです。 

次のセクションでは、リソース アプリケーションのマニフェストを変更してアクセス スコープを公開する方法について説明します。

#### <a name="adding-access-scopes-to-your-resource-application"></a>リソース アプリケーションへのアクセス スコープの追加

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数にアクセスできる場合は、右上隅でアカウントをクリックし、ポータル セッションを目的の Azure AD テナントに設定します。

3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。

   ![アプリケーションの登録の更新](./media/active-directory-integrating-applications/update-app-registration.png)

4. アプリケーションのメインの登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。 アプリケーションの登録ページで **[マニフェスト]** をクリックして、**[マニフェストの編集]** ページに切り替えます。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを**編集**できます。 必要に応じて、**[ダウンロード]** をクリックしてローカルで編集し、**[アップロード]** を使用して、アプリケーションを再適用します。

5. この例では、次の JSON 要素を `oauth2Permissions` コレクションに追加して、`Employees.Read.All` と呼ばれる新しいスコープをリソース/API で公開します。 既存の `user_impersonation` スコープは、登録時に既定で提供されます。 `user_impersonation` を使用すると、クライアント アプリケーションが、サインイン ユーザーの ID で、リソースにアクセスするためのアクセス許可を要求できます。 既存の `user_impersonation` スコープ要素の後に必ずコンマを追加し、リソースのニーズに合わせて、プロパティ値を変更してください。 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > "id" 値は、[guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) などの GUID 生成ツールまたはプログラムを使用して作成する必要があります。 これは、Web API によって公開されているスコープに対する一意識別子を表します。 Web API にアクセスするためのアクセス許可でクライアントが適切に構成されると、そのクライアントには、Azure AD によって OAuth 2.0 アクセス トークンが発行されます。Web API にアクセスするためのアクセス許可でクライアントが適切に構成されると、そのクライアントには、Azure AD によって OAuth 2.0 アクセス トークンが発行されます。 クライアントは、Web API を呼び出すときに、スコープ (scp) 要求が、アプリケーション登録で要求されたアクセス許可に設定されているアクセス トークンを提示します。
  >
  > 必要に応じて、他のスコープを後で公開することもできます。 たとえば、Web API で、さまざまな機能に関連付けられた複数のスコープを公開しているとします。 リソースは、受け取った OAuth 2.0 アクセス トークンのスコープ (`scp`) 要求を評価することで、実行時に Web API へのアクセスを制御できます。
  > 

6. 完了したら、**[保存]** をクリックします。 これで、ディレクトリ内の他のアプリケーションが使用できるように Web API が構成されました。  

  ![アプリケーションの登録の更新](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>テナント内の他のアプリケーションに Web API が公開されていることの確認
1. Azure AD テナントに戻って、**[アプリの登録]** をもう一度クリックし、構成するクライアント アプリケーションを検索/クリックします。

   ![アプリケーションの登録の更新](./media/active-directory-integrating-applications/update-app-registration.png)

2. 「[Web API にアクセスするためのクライアント アプリケーションの構成](#configure-a-client-application-to-access-web-apis)」で実行したように、手順 5. を繰り返します。 **API を選択**する手順で、検索フィールドにアプリケーション名を入力してリソースを検索し、**[選択]** をクリックします。 

3. **[アクセスの有効化]** ページに、クライアントのアクセス許可の要求に使用できる、新しいスコープが表示されます。

  ![新しい許可の表示](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>アプリケーション マニフェストの詳細

アプリケーション マニフェストは、実際にはアプリケーション エンティティを更新するためのメカニズムとして機能します。このエンティティでは、上記の API アクセス スコープなど、Azure AD アプリケーションの ID 構成のすべての属性が定義されています。 アプリケーション エンティティとそのスキーマの詳細については、[Graph API のアプリケーション エンティティに関するドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)を参照してください。 この記事では、API のアクセス許可を指定するために使用するアプリケーション エンティティ メンバーについての詳細な参照情報を確認できます。たとえば、次のような情報です。  

- appRoles メンバー: Web API の[アプリケーション アクセス許可](active-directory-dev-glossary.md#permissions)を定義するときに使用される、[AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) エンティティのコレクションです。 
- oauth2Permissions メンバー: Web API の[委任されたアクセス許可](active-directory-dev-glossary.md#permissions)を定義するときに使用される、[OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) エンティティのコレクションです。

アプリケーション マニフェストの一般的な概念の詳細については、「[Azure Active Directory のアプリケーション マニフェストについて](active-directory-application-manifest.md)」を参照してください。

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Microsoft Graph API による Azure AD Graph と Office 365 へのアクセス  

前に説明したように、独自のアプリケーションの API を公開してアクセスできるようにするだけでなく、Microsoft のリソースによって公開される API にアクセスするようにクライアント アプリケーションを登録できます。 Microsoft Graph API (ポータルのリソース/API リストでは "Microsoft Graph" と呼ばれます) は、Azure AD に登録されたすべてのアプリケーションが使用できます。 Office 365 サブスクリプションに対してサインアップしたアカウントを含むテナントで、クライアント アプリケーションを登録する場合は、さまざまな Office 365 リソースで公開されているスコープにもアクセスできます。

Microsoft Graph API によって公開されるスコープの詳細については、[アクセス許可スコープ | Microsoft Graph API の概念](https://graph.microsoft.io/docs/authorization/permission_scopes)に関する記事をご覧ください。

> [!NOTE]
> 現在の制限により、ネイティブ クライアント アプリケーションは、"組織のディレクトリにアクセスする" アクセス許可を使用する場合に、Azure AD Graph API しか呼び出すことはできません。 この制限は、Web アプリケーションには適用されません。
> 
> 

### <a name="configuring-multi-tenant-applications"></a>マルチテナント アプリケーションの構成

Azure AD でアプリケーションを登録するときに、組織のユーザーだけがアプリケーションにアクセスできるようにする場合があります。 また、外部組織のユーザーがアプリケーションにアクセスできるようにする場合もあります。 前者をシングルテナント アプリケーション、後者をマルチテナント アプリケーションと呼びます。 このセクションでは、シングルテナント アプリケーションの構成を変更して、マルチテナント アプリケーションにする方法について説明します。

シングルテナント アプリケーションとマルチテナント アプリケーションの違いに注意することが重要です。  

- シングルテナント アプリケーションは、1 つの組織で使用することを目的としています。 通常、このアプリケーションは、エンタープライズ開発者によって作成された基幹業務 (LOB) アプリケーションです。 シングルテナント アプリケーションには、アプリケーション登録と同じテナントのアカウントを持つユーザーのみがアクセスできます。 このため、1 つのディレクトリにプロビジョニングするだけで済みます。
- マルチテナント アプリケーションは、多数の組織で使用することを目的としています。 サービスとしてのソフトウェア (SaaS) Web アプリケーションと呼ばれ、通常は、独立系ソフトウェア ベンダー (ISV) によって作成されます。 マルチテナント アプリケーションは、ユーザーがアクセスする必要があるテナントごとにプロビジョニングする必要があります。 アプリケーションが登録されているテナント以外の場合、登録にはユーザーまたは管理者の同意が必要です。 ネイティブ クライアント アプリケーションは、リソース所有者のデバイスにインストールされるため、既定でマルチテナントになります。 同意フレームワークの詳細については、前の「[同意フレームワークの概要](#overview-of-the-consent-framework)」を参照してください。

アプリケーションをマルチテナントにするには、アプリケーション登録の変更と、Web アプリケーション自体への変更が必要です。 以下のセクションで両方について説明します。

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>アプリケーション登録の変更によるマルチテナントのサポート

組織の外部の顧客やパートナーが使用できるアプリケーションを作成する場合は、Azure Portal でアプリケーションの定義を更新する必要があります。

> [!IMPORTANT]
> Azure AD では、マルチテナント アプリケーションのアプリ ID URI を、グローバルに一意なものにする必要があります。 アプリケーション ID URI は、プロトコル メッセージでアプリケーションを識別する手段の 1 つです。 シングル テナント アプリケーションの場合、アプリケーション ID URI はそのテナント内で一意であれば十分です。 マルチテナント アプリケーションの場合、Azure AD ですべてのテナントからそのアプリケーションを検索できるように、アプリケーション ID URI はグローバルに一意である必要があります。 グローバルな一意性は、アプリケーション ID URI のホスト名を Azure AD テナントの検証済みドメインと一致するものに設定することで実現できます。 たとえば、テナントの名前が contoso.onmicrosoft.com である場合、有効なアプリケーション ID URI は https://contoso.onmicrosoft.com/myapp のようになります。 また、テナントの検証済みドメインが contoso.com である場合は、有効なアプリケーション ID URI は https://contoso.com/myapp のようになります。 アプリ ID URI がこのパターンに従っていない場合、アプリケーションのマルチテナントとしての設定は失敗します。
> 

外部ユーザーがアプリケーションにアクセスできるようにするには: 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数にアクセスできる場合は、右上隅でアカウントをクリックし、ポータル セッションを目的の Azure AD テナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。 アプリケーションのメインの登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
4. **[設定]** ページで、**[プロパティ]** をクリックし、**[マルチテナント]** スイッチを **[はい]** に変更します。

変更を行うと、他の組織のユーザーと管理者が、その組織のユーザーによるアプリケーションへのサインインを許可できるようになり、テナントによって保護されたリソースへのアプリケーション アクセスが許可されます。

#### <a name="changing-the-application-to-support-multi-tenant"></a>アプリケーションの変更によるマルチテナントのサポート

マルチテナント アプリケーションのサポートは、Azure AD の同意フレームワークに大きく依存します。 同意は、他のテナントのユーザーが、ユーザーのテナントによって保護されたリソースへのアプリケーション アクセスを許可できるようにするメカニズムです。 このエクスペリエンスは "ユーザーの同意" と呼ばれます。

Web アプリケーションによって、以下が提供される場合もあります。

- 管理者による "会社のサインアップ"。 このエクスペリエンスは "管理者の同意" と呼ばれ、管理者が組織内の "*すべてのユーザー*" に代わって同意を付与できるようにします。 管理者の同意を付与できるのは、全体管理者ロールに属するアカウントで認証したユーザーだけです。他のユーザーが付与すると、エラーが発生します。

- ユーザーのサインアップ エクスペリエンス。 ユーザーに "サインアップ" ボタンが表示されます。このボタンにより、Azure AD OAuth2.0 `/authorize` エンドポイントまたは OpenID Connect `/userinfo` エンドポイントにブラウザーがリダイレクトされます。 これらのエンドポイントでは、アプリケーションが id_token を調べて、新しいユーザーに関する情報を取得できます。 サインアップ フェーズの後、「[同意フレームワークの概要](#overview-of-the-consent-framework)」に示したプロンプトと同様の同意プロンプトがユーザーに表示されます。

マルチテナント アクセスとサインイン/サインアップ エクスペリエンスのサポートに必要なアプリケーションの変更の詳細については、次を参照してください。

- [マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法](active-directory-devhowto-multi-tenant-overview.md)
- [マルチテナント コード サンプル](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)の一覧。 
- [クイック スタート: Azure AD のサインイン ページに会社のブランドを追加する](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>シングル ページ アプリケーションでの OAuth 2.0 Implicit Grant の有効化

通常、シングル ページ アプリケーション (SPA) は、ブラウザーで実行される JavaScript ヘビーなフロント エンドで構成されており、このフロントエンドがアプリケーションの Web API バックエンドを呼び出してビジネス ロジックを実行します。 Azure AD でホストされている SPA では、Azure AD でのユーザーの認証と、アプリケーションの JavaScript クライアントからバックエンド Web API への呼び出しを保護するために使用できるトークンの取得に OAuth 2.0 Implicit Grant を使用します。 

ユーザーの同意後、この同じ認証プロトコルを使用して、クライアントと、アプリケーション用に構成された他の Web API リソースとの間での呼び出しを保護するトークンを取得できます。 暗黙的な認証付与の詳細と、この機能がご使用のアプリケーション シナリオに適しているかどうかについては、「 [Azure Active Directory での OAuth2 の暗黙的な許可フローについて](active-directory-dev-understanding-oauth2-implicit-grant.md)」を参照してください。

既定では、OAuth 2.0 Implicit Grant はアプリケーションに対して無効化されています。 [アプリケーション マニフェスト](active-directory-application-manifest.md)で `oauth2AllowImplicitFlow` の値を設定することにより、アプリケーションに対して OAuth 2.0 Implicit Grant を有効化できます。

#### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 Implicit Grant を有効化するには

> [!NOTE]
> アプリケーション マニフェストを編集する方法の詳細については、前の「[Web API を公開するためのリソース アプリケーションの構成](#configuring-a-resource-application-to-expose-web-apis)」を参照してください。
>

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数にアクセスできる場合は、右上隅でアカウントをクリックし、ポータル セッションを目的の Azure AD テナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。 アプリケーションのメインの登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
4. アプリケーションの登録ページで **[マニフェスト]** をクリックして、**[マニフェストの編集]** ページに切り替えます。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを**編集**できます。 "oauth2AllowImplicitFlow" を見つけて、値を "true" に設定します。 既定値は "false" です。
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. 更新したマニフェストを保存します。 保存が完了すると、ユーザー認証に OAuth 2.0 Implicit Grant を使用するように Web API が構成されています。

## <a name="removing-an-application"></a>アプリケーションの削除
このセクションでは、Azure AD テナントからアプリケーションの登録を削除する方法について説明します。

### <a name="removing-an-application-authored-by-your-organization"></a>組織によって作成されたアプリケーションの削除
組織によって登録されたアプリケーションは、テナントの "アプリの登録" メイン ページで、"マイアプリ" フィルターの下に表示されます。 こうしたアプリケーションは、Azure Portal を使用して手動で登録されているか、PowerShell または Graph API を使用してプログラムで登録されています。 具体的には、テナント内ではアプリケーション オブジェクトとサービス プリンシパル オブジェクトの両方によって表されます。 詳細については、「 [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](active-directory-application-objects.md)」をご覧ください。

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>ディレクトリからシングルテナント アプリケーションを削除するには
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数にアクセスできる場合は、右上隅でアカウントをクリックし、ポータル セッションを目的の Azure AD テナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。 アプリケーションのメインの登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
4. アプリケーションのメインの登録ページで **[削除]** をクリックします。
5. 確認メッセージが表示されたら、 **[はい]** をクリックします。

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>ホーム ディレクトリからマルチテナント アプリケーションを削除するには
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数にアクセスできる場合は、右上隅でアカウントをクリックし、ポータル セッションを目的の Azure AD テナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。 アプリケーションのメインの登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
4. **[設定]** ページで **[プロパティ]** を選択して、**[マルチテナント]** を **[いいえ]** に切り替えてまずアプリケーションをシングルテナントに変更してから、**[保存]** をクリックします。 アプリケーションのサービス プリンシパル オブジェクトは、既に同意しているすべての組織のテナントで保持されます。
5. アプリケーションのメインの登録ページで **[削除]** をクリックします。
6. 確認メッセージが表示されたら、 **[はい]** をクリックします。

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>別の組織によって承認されたマルチテナント アプリケーションの削除
テナントの "アプリの登録" メイン ページで、"すべてのアプリ" フィルター ("マイアプリ" 登録を除く) の下に表示されているアプリケーション サブセットが、マルチテナント アプリケーションです。 技術的には、このマルチテナント アプリケーションは他のテナントにあり、同意プロセス中にテナントに登録されました。 具体的には、テナント内ではサービス プリンシパル オブジェクトのみによって表されます。対応するアプリケーション オブジェクトはありません。 アプリケーション オブジェクトとサービス プリンシパル オブジェクトの違いの詳細については、[Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](active-directory-application-objects.md)に関するページをご覧ください。

(同意後に) ディレクトリへのマルチテナント アプリケーションのアクセス権を削除するには、会社の管理者がそのサービス プリンシパルを削除する必要があります。 管理者には全体管理者アクセス権が必要で、Azure Portal または [Azure AD PowerShell コマンドレット](http://go.microsoft.com/fwlink/?LinkId=294151)を使用して、アクセス権を削除できます。

## <a name="next-steps"></a>次の手順
- Azure AD での認証のしくみの詳細については、「[Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」を参照してください。
- アプリの視覚的なガイダンスに関するヒントについては、[統合アプリケーションのブランド化に関するガイドライン](active-directory-branding-guidelines.md)を参照してください。
- アプリケーションのアプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](active-directory-application-objects.md)に関するページをご覧ください。
- アプリ マニフェストの役割の詳細については、「[Azure Active Directory のアプリケーション マニフェストについて](active-directory-application-manifest.md)」を参照してください。
- Azure Active Directory (AD) の開発者向けの重要な概念の定義については、「[Azure Active Directory 開発者向け用語集](active-directory-dev-glossary.md)」を参照してください。
- 開発者向けのすべての関連コンテンツの概要については、[Active Directory 開発者ガイド](active-directory-developers-guide.md)を参照してください。

