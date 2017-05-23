---
title: "Azure Active Directory B2C: カスタム ポリシーのトラブルシューティング | Microsoft Docs"
description: "カスタム ポリシーのエラーを解決する各種方法のガイド"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0f783062cd674edb0b6b49660f5aee69af4b3adb
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-custom-policies-and-identity-experience-framework-troubleshooting"></a>Azure Active Directory B2C のカスタム ポリシーと Identity Experience Framework のトラブルシューティング
## <a name="the-basics"></a>基本

Azure AD B2C カスタム ポリシーを使用する ID 開発者は、XML 形式のポリシー言語で Identity Experience Framework を構成することを求められます。  このガイドには、問題を迅速に発見して解決するうえで推奨されるツールとヒントの一覧を示します。  カスタム ポリシーの記述の学習は、新しい言語の学習に似ています。  
"*この記事では、Azure AD B2C カスタム ポリシーの構成のトラブルシューティングについて主に説明します。証明書利用者アプリケーションまたはその ID ライブラリのトラブルシューティングについては説明しません。*"



## <a name="xml-editing-inproperly-formatted-xml-is-the-most-common-error"></a>最も一般的なエラーである XML の編集 (不適切な形式の XML)

優れた XML エディターでは、XML のネイティブな表示、コード内容の色付け、一般的な用語の事前入力、XML 要素の持続的なインデックス作成、スキーマの検証が可能であり、このようなエディターはほぼ必要不可欠です。  次の 2 つをお勧めします。

* [VS Code](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

スターター パックのルート フォルダーから XML スキーマ定義 `TrustFrameworkPolicy_0.3.0.0.xsd` を取得します。 XML スキーマ検証では、アップロードする前にエラーを識別します。  `XML tools` と `XML Validation` について XML エディターのドキュメントを参照してください。

Azure AD B2C では検出されたすべての形式エラーが拒否されるため、XML の規則を簡単に確認できることが非常に役立つ場合があります。  ときどき不適切な形式の XML が原因で、誤解を招くエラー メッセージが表示されることがあります。

## <a name="uploading-policies-and-policy-validation"></a>ポリシーのアップロードとポリシー検証

 **アップロード**の検証は自動であり、ほとんどのエラーでアップロードが中止されます。  **アップロードしようとしているポリシー ファイルのほか、それが参照する一連のファイルが検証されることに留意してください。  `RP policy file > Extensions file > Base File` 一般的な検証エラーには、次のものがあります。

エラー スニペット: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* ClaimType のスペルが間違っているか、スキーマに存在しない可能性があります。
* ClaimType は、ポリシー内の少なくとも 1 つのファイルで定義されている必要があります。  例: ` <ClaimType Id="socialIdpUserId">`。
* ClaimType が拡張ファイルで定義されていてベース ファイルの TechnichalProfile で使用される場合、ベース ファイルのアップロードでエラーが発生します。

エラー スニペット: `...males a reference to a ClaimsTransformation with id...`
* 上記と同じです。

エラー スニペット: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* **<TrustFrameworkPolicy>** 要素と **<BasePolicy>** 要素の TenantId が対象の B2C テナントと一致していることを確認します。  



## <a name="runtime-troubleshooting"></a>ランタイムのトラブルシューティング

* `Run Now` と `https://jwt.io` を使用して、Web アプリケーションまたはモバイル アプリケーションとは別にポリシーをテストします。 この Web サイトは証明書利用者アプリケーションのように動作し、Azure AD B2C ポリシーによって生成された JWT トークンの内容を表示します。  Identity Experience Framework でテスト アプリケーションを作成します。
    * 名前: TestApp
    * Web アプリ/Web API: なし
    * ネイティブ クライアント: なし

* [Fiddler](http://www.telerik.com/fiddler) を使用して、クライアント ブラウザーと Azure AD B2C の間でのメッセージ交換をトレースします。  オーケストレーション手順のどこでユーザー体験が失敗するか、ヒントが得られます。

* **開発モード**で **Application Insights** を使用して、Identity Experience Framework (IEF) のユーザー体験の動作をトレースします。  **開発モード**では、TechnicalProfiles で定義された各種要求プロバイダーと IEF の間で行われる要求の交換を観察できます。この要求プロバイダーには、ID プロバイダー、API ベースのサービス、Azure AD B2C ユーザー ディレクトリ (AAD ディレクトリ) のほか、Multi-Factor Authentication などのサービスがあります。  

## <a name="recommended-practies"></a>推奨される方法

**複数のバージョンのシナリオを保持し、アプリケーションを使用してプロジェクトでそれらをグループ化する。** ベース ファイル、拡張ファイル、証明書利用者 (RP) ファイルは互いに直接依存しています。ポリシーで新しい機能を実現する場合、これらのファイルを 1 つのグループとして保存し、作業バージョンを別々に保持します。  これらのファイルがやり取りするアプリケーション コードを使用して、独自のファイル システムでそれらのファイルをステージングします。  アプリケーションはテナント内の異なる RP ポリシーを呼び出し、Azure AD B2C ポリシーからの想定される要求に依存するようになることがあります。

**既知のユーザー体験を使用して技術プロファイルの開発とテストを行う。**  テストが済んでいるスターター パックのポリシーを使用して技術プロファイルを構成し、独自のユーザー体験に組み込む前に個別にテストします。

**テストが済んでいる技術プロファイルを使用してユーザー体験を開発してテストする。**ユーザー体験のオーケストレーション手順を少しずつ変更し、目的のシナリオを段階的に構築します。







次の手順で作業を開始します。

1. GitHub から "active-directory-b2c-custom-policy-starterpack" をダウンロードします。  [zip をダウンロード](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)するか、次を実行します。
### <a name="built-in"></a>ビルトイン



Azure Active Directory (Azure AD) B2C の拡張可能なポリシー フレームワークは、このサービスの中核となる強みです。 ポリシーには、サインアップ、サインイン、プロファイルの編集など、コンシューマーの ID エクスペリエンスが完全に記述されています。 たとえば、サインアップのポリシーを使用して次の設定を構成し、動作を制御できます。

* コンシューマーがアプリケーションにサインアップするために使用できるアカウント タイプ (Facebook などのソーシャル アカウント、または電子メール アドレスなどのローカル アカウント)。
* サインアップ中にコンシューマーから収集される属性 (名、郵便番号、靴のサイズなど)。
* 多要素認証の使用。
* すべてのサインアップ ページの外観。
* ポリシーの実行が完了したときにアプリケーションが受け取る情報 (要求内で要求として明示される)。

テナント内でさまざまな種類のポリシーを複数作成し、必要に応じてそれらをアプリケーションで使用できます。 ポリシーは、アプリケーション間で再利用することができます。 これにより、開発者はコードの変更を最小限に抑えるか、まったく変更を行わずに、コンシューマーの ID エクスペリエンスを定義および変更できます。

ポリシーは、簡単な開発者インターフェイスを使用して入手できます。 アプリケーションは標準 HTTP 認証要求 (要求でポリシー パラメーターを渡す) を使用してポリシーをトリガーし、カスタマイズされたトークンを応答として受け取ります。 たとえば、サインアップ ポリシーを呼び出す要求と、サインイン ポリシーを呼び出す要求の唯一の相違点は、"p" クエリ文字列パラメーターで使用されるポリシー名です。

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

ポリシー フレームワークの詳細については、こちらの [ブログ記事](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)を参照してください。

## <a name="create-a-sign-up-policy"></a>サインアップ ポリシーを作成する
アプリケーションにサインアップできるようにするには、サインアップ ポリシーを作成する必要があります。 このポリシーは、サインアップ中のコンシューマーのエクスペリエンスと、サインアップが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure ポータルで B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[サインアップ ポリシー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるサインアップ ポリシー名が決定されます。 たとえば、「SiUp」を入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Email signup (電子メール サインアップ)] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[サインアップ属性]**をクリックします。 ここで、サインアップ中にコンシューマーから収集する属性を選択します。 たとえば、[市町村]、[表示名]、[郵便番号] などを選択します。 **[OK]**をクリックします。
7. **[アプリケーション クレーム]**をクリックします。 ここで、サインアップ エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名"、"ID プロバイダー"、"郵便番号"、"ユーザーが新規"、"ユーザーのオブジェクト ID" などを選択します。
8. **[作成]**をクリックします。 作成したポリシーが **[Sign-up policies (サインアップ ポリシー)]** ブレードに "**B2C_1_SiUp**" と表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
9. **[B2C_1_SiUp]** をクリックしてポリシーを開きます。
10. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
11. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションへのサインアップのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-sign-in-policy"></a>サインイン ポリシーを作成する
アプリケーションにサインインできるようにするには、サインイン ポリシーを作成する必要があります。 このポリシーは、サインイン中のコンシューマーのエクスペリエンスと、サインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[サインイン ポリシー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるサインイン ポリシー名が決定されます。 たとえば、「SiIn」を入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Local Account SignIn (ローカル アカウント サインイン)] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[アプリケーション クレーム]**をクリックします。 ここで、サインイン エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名"、"ID プロバイダー"、"郵便番号"、および "ユーザーのオブジェクト ID" などを選択します。 **[OK]**をクリックします。
7. **[作成]**をクリックします。 作成したポリシーが**サインイン ポリシー** ブレードに "**B2C_1_SiIn**" と表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
8. **[B2C_1_SiIn]** をクリックしてポリシーを開きます。
9. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
10. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションへのサインインのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを作成する
このポリシーは、1 つの構成でコンシューマーのサインアップ エクスペリエンスとサインイン エクスペリエンスの両方を処理します。 コンシューマーは、状況に応じて正しいパス (サインアップまたはサインイン) に誘導されます。 このポリシーはまた、サインアップまたはサインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。  サインアップまたはサインイン ポリシーのサンプル コードは、 [こちらで入手できます](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[Sign-up or sign-in policies (サインアップまたはサインイン ポリシー)]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるサインアップ ポリシー名が決定されます。 たとえば、「SiUpIn」と入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Email signup (電子メール サインアップ)] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[サインアップ属性]**をクリックします。 ここで、サインアップ中にコンシューマーから収集する属性を選択します。 たとえば、[市町村]、[表示名]、[郵便番号] などを選択します。 **[OK]**をクリックします。
7. **[アプリケーション クレーム]**をクリックします。 ここで、サインアップまたはサインイン エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名"、"ID プロバイダー"、"郵便番号"、"ユーザーが新規"、"ユーザーのオブジェクト ID" などを選択します。
8. **[作成]**をクリックします。 作成したポリシーが **[Sign-up or sign-in policies (サインアップまたはサインイン ポリシー)]** ブレードに "**B2C_1_SiUpIn**" として表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
9. **[B2C_1_SiUpIn]** をクリックしてポリシーを開きます。
10. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
11. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、構成したサインアップまたはサインインのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する
アプリケーションでポリシーを編集できるようにするには、プロファイル編集ポリシーを作成する必要があります。 このポリシーは、プロファイル編集中のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[プロファイル編集ポリシー]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるプロファイル編集ポリシー名が決定されます。 たとえば、「SiPe」を入力します。
5. **[ID プロバイダー]** をクリックし、[ローカル アカウント サインイン] を選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。
6. **[プロファイルの属性]**をクリックします。 ここで、コンシューマーが表示および編集できる属性を選択します。 たとえば、[市町村]、[表示名]、[郵便番号] などを選択します。 **[OK]**をクリックします。
7. **[アプリケーション クレーム]**をクリックします。 ここで、プロファイル編集エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"表示名" および "郵便番号" などを選択します。
8. **[作成]**をクリックします。 作成したポリシーが **[Profile editing policies (プロファイルの編集ポリシー)]** ブレードに "**B2C_1_SiPe**" として表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
9. **[B2C_1_SiPe]** をクリックしてポリシーを開きます。
10. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
11. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションでプロファイル編集のコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する
アプリケーションで詳細なパスワード リセットを有効にするには、パスワードのリセット ポリシーを作成する必要があります。 [ここ](active-directory-b2c-reference-sspr.md) に示されているテナント全体のパスワード リセット オプションは、サインイン ポリシーにも適用可能であることに注意してください。 このポリシーは、パスワード リセット中のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. **[Password reset policies (パスワードのリセット ポリシー)]**をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. **[名前]** によって、アプリケーションで使用されるパスワードのリセット ポリシー名が決まります。 たとえば、「SSPR」などと入力します。
5. **[Identity providers (ID プロバイダー)]** をクリックし、[Reset password using email address (電子メール アドレスを使用してパスワードをリセットする)] を選択します。 **[OK]**をクリックします。
6. **[アプリケーション クレーム]**をクリックします。 ここで、パスワード リセット エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"ユーザーのオブジェクト ID" を選択します。
7. **[作成]**をクリックします。 作成したポリシーが **[Password reset policies (パスワードのリセット ポリシー)]** ブレードに "**B2C_1_SSPR**" として表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。
8. **[B2C_1_SSPR]** をクリックしてポリシーを開きます。
9. **[アプリケーション]** ボックスの一覧で "Contoso B2C app" を選択し、**[応答 URL/リダイレクト URI]** ボックスの一覧で `https://localhost:44321/` を選択します。
10. **[今すぐ実行]**をクリックします。 新しいブラウザー タブが開き、アプリケーションでパスワード リセットのコンシューマー エクスペリエンスを確認できます。
    
    > [!NOTE]
    > ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
    > 
    > 

## <a name="how-to-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>サインアップまたはサインイン ポリシーをパスワード リセット ポリシーに関連付ける方法
(ローカル アカウントで) サインアップまたはサインイン ポリシーを作成すると、エクスペリエンスの最初のページに  "パスワードを忘れた場合" リンクが表示されます。 このリンクをクリックしても、パスワード リセット ポリシーは自動的にはトリガーされません。 代わりに、エラーコード `AADB2C90118` がアプリに返されます。 アプリはこれを処理し、特定のパスワード リセット ポリシーを呼び出す必要があります。 ポリシーを関連付けるこの方法を示すサンプルについては、[こちら](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [トークン、セッション、およびシングル サインオンの構成](active-directory-b2c-token-session-sso.md)。
* [コンシューマーのサインアップ時の電子メール検証の無効化](active-directory-b2c-reference-disable-ev.md)


