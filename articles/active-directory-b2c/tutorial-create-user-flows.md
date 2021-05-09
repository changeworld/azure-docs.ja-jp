---
title: チュートリアル - ユーザー フローとカスタム ポリシーを作成する - Azure Active Directory B2C
description: このチュートリアルでは、Azure Active Directory B2C 内のアプリケーションに対するサインアップ、サインイン、およびユーザー プロファイルの編集を実行できるようにするユーザー フローとカスタム ポリシーの Azure portal での作成方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e41c1e74dbe428ee38d4480a1587050b7f96a55f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226228"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C 内にユーザー フローを作成する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

お使いのアプリケーションに、ユーザーによるサインアップ、サインイン、プロファイル管理を可能にするユーザー フローを設定できます。 Azure Active Directory B2C (Azure AD B2C) テナント内にさまざまな種類のユーザー フローを複数作成し、それらを必要に応じてアプリケーションで使用できます。 ユーザー フローは、アプリケーション間で再利用することができます。

::: zone pivot="b2c-user-flow"
ユーザー フローを使用することで、ユーザーがサインイン、サインアップ、プロファイルの編集、パスワードのリセットなどを行うときに、アプリケーションとどのように対話するかを決定できます。 この記事では、次のことについて説明します。
::: zone-end

::: zone pivot="b2c-custom-policy"
[カスタム ポリシー](custom-policy-overview.md)は、Azure Active Directory B2C (Azure AD B2C) テナントの動作を定義する構成ファイルです。 この記事では、次の方法について説明します。
::: zone-end

> [!div class="checklist"]
> * サインアップとサインイン ユーザー フローを作成する
> * セルフサービス パスワード リセットを有効にする
> * プロファイル編集ユーザー フローを作成する

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> ユーザー フローのバージョンを参照する方法が変更されました。 これまでは、V1 (運用対応) バージョンと V1.1 および V2 (プレビュー) バージョンが提供されていました。 現在は、ユーザー フローは **推奨** (次世代プレビュー) バージョンと **標準** (一般提供) バージョンに統合されています。 V1.1 と V2 の従来のプレビュー ユーザー フローはすべて、**2021 年 8 月 1 日** までに非推奨になる予定です。 詳細については、[Azure AD B2C のユーザー フローのバージョン](user-flow-versions.md)に関するページを参照してください。
::: zone-end

## <a name="prerequisites"></a>前提条件

::: zone pivot="b2c-user-flow"
- まだ持っていない場合は、[お使いの Azure サブスクリプションにリンクされている Azure AD B2C テナント](tutorial-create-tenant.md)を作成します。
- Azure AD B2C と通信できるように、自分が作成した[テナントにアプリケーションを登録](tutorial-register-applications.md)します。
::: zone-end

::: zone pivot="b2c-custom-policy"
- まだ持っていない場合は、[お使いの Azure サブスクリプションにリンクされている Azure AD B2C テナント](tutorial-create-tenant.md)を作成します。
- Azure AD B2C と通信できるように、自分が作成した[テナントにアプリケーションを登録](tutorial-register-applications.md)します。
- [Facebook アカウントでのサインアップとサインインの設定](identity-provider-facebook.md)に関する記事の手順を完了して、Facebook アプリケーションを構成します。 Facebook アプリケーションがなくてもカスタム ポリシーを使用できますが、カスタム ポリシーでソーシャル ログインを有効にする方法を見せる目的でこのチュートリアルで使用されています。
::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>サインアップとサインイン ユーザー フローを作成する

サインアップとサインイン ユーザー フローは、サインアップ エクスペリエンスとサインイン エクスペリエンスの両方を 1 つの構成で処理します。 アプリケーションのユーザーは、状況に応じて正しいパスに誘導されます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルツールバーの **[Directory + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントが含まれているディレクトリを選択します。

    ![B2C テナント、ディレクトリとサブスクリプションのペイン、Azure portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。

    ![[新しいユーザー フロー] ボタンが強調表示されているポータル内の [ユーザー フロー] ページ](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. **[ユーザー フローを作成する]** ページで、 **[サインアップとサインイン]** ユーザー フローを選択します。

    ![サインアップとサインイン フローが強調表示されている [ユーザー フローの選択] ページ](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。 (ユーザー フローのバージョンに関する[詳細情報](user-flow-versions.md))。

    ![プロパティが強調表示されている、Azure portal 内の [ユーザー フローの作成] ページ](./media/tutorial-create-user-flows/select-version.png)

1. ユーザー フローの **[名前]** を入力します。 たとえば、「*signupsignin1*」と入力します。
1. **[ID プロバイダー]** の **[電子メールのサインアップ]** を選択します。
1. **[ユーザー属性と要求]** で、サインアップ中にユーザーから収集して送信する要求と属性を選択します。 たとえば、 **[Show more]\(さらに表示\)** を選択し、 **[国/リージョン]** 、 **[表示名]** 、 **[郵便番号]** の属性と要求を選択します。 **[OK]** をクリックします。

    ![3 つの要求が選択されている [属性と要求の選択] ページ](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. **[作成]** をクリックして、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックしてから、 **[今すぐサインアップ]** を選択します。

    ![[ユーザー フローを実行します] ボタンが強調表示されているポータル内の [ユーザー フローを実行します] ページ](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 有効なメール アドレスを入力し、 **[確認コードの送信]** をクリックします。受信した確認コードを入力し、 **[コードの確認]** を選択します。
1. 新しいパスワードを入力し、そのパスワードを確認します。
1. 国とリージョンを選択し、表示する名前を入力し、郵便番号を入力したら、 **[作成]** をクリックします。 トークンが `https://jwt.ms` に返され、表示されます。
1. これでユーザー フローをもう一度実行できるようになりました。作成したアカウントでサインインできる必要があります。 返されたトークンには、選択した国/リージョン、名前、および郵便番号の要求が含まれています。

> [!NOTE]
> [ユーザー フローを実行します] エクスペリエンスは、現在、認可コード フローを使用する、種類が SPA の応答 URL と互換性がありません。 これらの種類のアプリで [ユーザー フローを実行します] エクスペリエンスを使用するには、種類が "Web" の応答 URL を登録し、[こちら](tutorial-register-spa.md)の説明に従って暗黙的なフローを有効にします。

## <a name="enable-self-service-password-reset"></a>セルフサービス パスワード リセットを有効にする

サインアップまたはサインインのユーザー フローで[セルフサービス パスワード リセット](add-password-reset-policy.md)を有効にするには:

1. 作成した、サインアップまたはサインインのユーザー フローを選択します。
1. 左側のメニューの **[設定]** で、 **[プロパティ]** を選択します。
1. **[パスワードの複雑さ]** で、 **[セルフサービス パスワード リセット]** を選択します。
1. **[保存]** を選択します。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択します。
1. サインアップまたはサインインのページで、 **[パスワードを忘れた場合]** を選択します。
1. 前に作成したアカウントの電子メール アドレスを確認してから、 **[続行]** を選択します。
1. これで、ユーザーのパスワードを変更できるようになりました。 パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、表示されます。

## <a name="create-a-profile-editing-user-flow"></a>プロファイル編集ユーザー フローを作成する

アプリケーションでユーザーによるプロファイル編集を有効にする場合は、プロファイル編集ユーザー フローを使用します。

1. Azure AD B2C テナントの [概要] ページのメニューで、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローを作成する]** ページで、 **[プロファイル編集]** ユーザー フローを選択します。 
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。
1. ユーザー フローの **[名前]** を入力します。 たとえば、「*profileediting1*」と入力します。
1. **[ID プロバイダー]** で、 **[ローカル アカウント サインイン]** を選択します。
2. **[ユーザー属性]** で、ユーザーが自分のプロファイルで編集できる属性を選択します。 たとえば、 **[Show more]\(さらに表示\)** を選択し、 **[表示名]** と **[役職]** の属性と要求を選択します。 **[OK]** をクリックします。
3. **[作成]** をクリックして、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックし、以前に作成したアカウントでサインインします。
1. これで、ユーザーの表示名と役職を変更できるようになりました。 **[Continue]** をクリックします。 トークンが `https://jwt.ms` に返され、表示されます。
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> この記事では、テナントを手動で設定する方法について説明します。 この記事からプロセス全体を自動化できます。 自動化すると、Azure AD B2C [SocialAndLocalAccountsWithMFA スターター パック ](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) がデプロイされます。これにより、サインアップとサインイン、パスワードのリセット、プロファイル編集の体験が提供されます。 以下のチュートリアルを自動化するには、[IEF セットアップ アプリ](https://aka.ms/iefsetup)にアクセスし、指示に従います。


## <a name="add-signing-and-encryption-keys"></a>署名および暗号化キーを追加します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[ポリシー]** を選択してから **[Identity Experience Framework]** を選択します。

### <a name="create-the-signing-key"></a>署名キーを作成します。

1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Generate`を選択します。
1. **名前** に`TokenSigningKeyContainer`を入力します。 プレフィックス `B2C_1A_` が自動的に追加される場合があります。
1. **キー タイプ** については、**RSA** を選択します。
1. **[キー使用法]** には **[署名]** を選択します。
1. **［作成］** を選択します

### <a name="create-the-encryption-key"></a>暗号化キーを作成します。

1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Generate`を選択します。
1. **名前** に`TokenEncryptionKeyContainer`を入力します。 プレフィックス `B2C_1A`_ が自動的に追加される場合があります。
1. **キー タイプ** については、**RSA** を選択します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **［作成］** を選択します

### <a name="create-the-facebook-key"></a>Facebook のキーを作成します。

ポリシー キーとして、Facebook アプリケーションの [[アプリ シークレット]](identity-provider-facebook.md)を追加します。 この記事の前提条件の一部として作成したアプリケーションのアプリ シークレットを使用できます。

1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. **名前** には、`FacebookSecret`を入力します。 プレフィックス `B2C_1A_` が自動的に追加される場合があります。
1. **シークレット** で、developers.facebook.com から Facebook アプリケーションの *アプリ シークレット* を入力します。 この値はシークレットであり、アプリケーション ID ではありません。
1. **[キー使用法]** には **[署名]** を選択します。
1. **［作成］** を選択します

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework アプリケーションを登録する

Azure AD B2C では、ローカル アカウントでのユーザーのサインアップとサインインのために使用する 2 つのアプリケーションを登録する必要があります。*IdentityExperienceFramework* (Web API) と、IdentityExperienceFramework アプリへのアクセス許可が委任された *ProxyIdentityExperienceFramework* (ネイティブ アプリ) です。 ユーザーはメール アドレスまたはユーザー名、およびパスワードを使用してサインアップし、テナントに登録されたアプリケーションにアクセスできます。これにより "ローカル アカウント" が作成されます。 ローカル アカウントは、Azure AD B2C テナント内にのみ存在します。

この 2 つのアプリケーションを Azure AD B2C テナントに 1 回だけ登録する必要があります。

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework アプリケーションを登録します

Azure AD B2C テナントにアプリケーションを登録するには、**アプリの登録** エクスペリエンスを使用できます。

1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **名前** には、`IdentityExperienceFramework`を入力します。
1. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[リダイレクト URI]** で **[Web]** を選択し、「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`」と入力します。`your-tenant-name` は、Azure AD B2C テナント ドメイン名です。
1. **[アクセス許可]** で、 *[openid と offline_access アクセス許可に対して管理者の同意を付与します]* チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。

次に、スコープを追加して API を公開します。

1. 左側のメニューの **[管理]** で、 **[API の公開]** を選択します。
1. **[スコープの追加]** 、 **[保存して続行]** の順に選択し、既定のアプリケーション ID URI をそのまま使用します。
1. 次の値を入力して、ご自身の Azure AD B2C テナントでカスタム ポリシーの実行を許可するスコープを作成します。
    * **スコープ名**: `user_impersonation`
    * **管理者の同意の表示名**: `Access IdentityExperienceFramework`
    * **管理者の同意の説明**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **[スコープの追加]** を選択します

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework アプリケーションを登録する

1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **名前** には、`ProxyIdentityExperienceFramework`を入力します。
1. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[リダイレクト URI]** で、ドロップダウンを使用して **[パブリック クライアント/ネイティブ (モバイルとデスクトップ)]** を選択します。
1. **[リダイレクト URI]** に「`myapp://auth`」と入力します。
1. **[アクセス許可]** で、 *[openid と offline_access アクセス許可に対して管理者の同意を付与します]* チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。

次に、アプリケーションをパブリック クライアントとして扱うよう指定します。

1. 左側のメニューの **[管理]** セクションで、 **[認証]** を選択します。
1. **[詳細設定]** の **[パブリック クライアント フローを許可する]** セクションで、 **[次のモバイルとデスクトップのフローを有効にする]** を **[はい]** に設定します。 **"allowPublicClient": true** がアプリケーション マニフェストで確実に設定されているようにします。 
1. **[保存]** を選択します。

次に、*IdentityExperienceFramework* 登録で前に公開した API スコープに、アクセス許可を付与します。

1. 左側のメニューの **[管理]** で、 **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の下で **[アクセス許可の追加]** を選択します。
1. **[自分の API]** タブ、**IdentityExperienceFramework** アプリケーションの順に選択します。
1. **[アクセス許可]** で、前に定義した **[user_impersonation]** スコープを選択します。
1. **[アクセス許可の追加]** を選択します. 指示に従って、数分待ってから次の手順に進みます。
1. **[<テナント名> に管理者の同意を与えます]** を選択します。
1. 現在サインインしているお使いの管理者アカウントを選択するか、少なくとも *クラウド アプリケーション管理者* ロールが割り当てられているお使いの Azure AD B2C テナントのアカウントでサインインします。
1. **[Accept]\(承認\)** を選択します。
1. **[最新の情報に更新]** を選択した後、スコープの **[状態]** に、"... に付与されました" が表示されることを確認します (offline_access、openid、および user_impersonation)。 アクセス許可が反映されるまでに数分かかる場合があります。

* * *

## <a name="custom-policy-starter-pack"></a>カスタム ポリシー スターター パック

カスタム ポリシーは、技術プロファイルとユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 すぐに作業を開始できるように、いくつかの事前に作成されたポリシーが含まれているスターターパックが用意されています。 これらの各スターター パックには、説明したシナリオを実現するために必要な最小数の技術プロファイルとユーザー体験が含まれています。

- **LocalAccounts** - ローカル アカウントのみの使用を可能にします。
- **SocialAccounts** - ソーシャル (フェデレーション) アカウントのみの使用を可能にします。
- **SocialAndLocalAccounts** - ローカル アカウントとソーシャル アカウントの両方の使用を可能にします。
- **SocialAndLocalAccountsWithMFA** - ソーシャル、ローカル、および多要素認証オプションの使用を可能にします。

各スターター パックには以下が含まれています。

- **ベース ファイル** - ベースにはいくつかの変更が必要です。 例:*TrustFrameworkBase.xml*
- **拡張ファイル** - このファイルは、構成変更の大半が実行される場所です。 例:*TrustFrameworkExtensions.xml*
- **証明書利用者ファイル** - アプリケーションによって呼び出される、タスク固有のファイルです。 例 :*SignUpOrSignin.xml*、*ProfileEdit.xml*、*PasswordReset.xml*

この記事では、**SocialAndLocalAccounts** スターター パック内の XML カスタム ポリシー ファイルを編集します。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。

### <a name="get-the-starter-pack"></a>スターター パックを取得する

GitHub からカスタム ポリシー スターター パックを取得し、SocialAndLocalAccounts スターター パック内の XML ファイルの名前を Azure AD B2C テナントの名前に更新します。

1. [.zip ファイルをダウンロードする](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)か、リポジトリを複製します。

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. **SocialAndLocalAccounts** ディレクトリ内のすべてのファイルで、`yourtenant` 文字列を Azure AD B2C テナントの名前に置き換えます。

    たとえば、B2C テナントの名前が *contosotenant* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosotenant.onmicrosoft.com` になります。

### <a name="add-application-ids-to-the-custom-policy"></a>カスタム ポリシーにアプリケーション ID を追加します。

拡張子ファイル *TrustFrameworkExtensions.xml*  にアプリケーション ID を追加します。

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** を開いて、`<TechnicalProfile Id="login-NonInteractive">` 要素を見つけます。
1. `IdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した IdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。
1. `ProxyIdentityExperienceFrameworkAppId` の両方のインスタンスを、前に作成した ProxyIdentityExperienceFramework アプリケーションのアプリケーション ID に置き換えます。
1. ファイルを保存します。

## <a name="upload-the-policies"></a>ポリシーをアップロードします。

1. Azure portal で B2C テナントに移動し、 **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択します。
1. 次の順序でポリシー ファイルをアップロードします。
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

ファイルをアップロードすると、Azure によって、それぞれに `B2C_1A_` が追加されます。

> [!TIP]
> XML エディターで検証がサポートされている場合は、スターター パックのルート ディレクトリにある `TrustFrameworkPolicy_0.3.0.0.xsd` という XML スキーマに対してファイルを検証します。 XML スキーマ検証では、アップロードする前にエラーを識別します。

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. カスタム ポリシーの概要ページの **[アプリケーションの選択]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。
1. **[返信 URL]** が `https://jwt.ms` であることを確認します。
1. **[今すぐ実行]** を選択します。
1. メール アドレスを使用してサインアップします。
1. もう一度 **[今すぐ実行]** を選択します。
1. 同じアカウントでサインインし、構成が正しく行われていることを確認します。

## <a name="add-facebook-as-an-identity-provider"></a>Facebook を ID プロバイダーとして追加する

[前提条件](#prerequisites)で説明したように、Facebook はカスタム ポリシーを使用するための必須条件では "*ありません*" が、カスタム ポリシーでソーシャル ログインを連携できることを示す目的でここでは使用されています。

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** ファイルで、`client_id` の値を Facebook アプリケーション ID に置き換えます。

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. *TrustFrameworkExtensions.xml* ファイルをテナントにアップロードします。
1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. **[今すぐ実行]** を選択し、Facebook でサインインする Facebook を選択し、カスタム ポリシーをテストします。

::: zone-end
## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * サインアップとサインイン ユーザー フローを作成する
> * プロファイル編集ユーザー フローを作成する
> * パスワードのリセット ユーザー フローを作成する

次に、Azure AD B2C を使用して、アプリケーションでユーザーをサインインおよびサインアップする方法を学習します。 下記のリンクを使用して ASP.NET Web アプリケーションに移動するか、目次の「**ユーザーを認証する**」の下にある別のアプリケーションに移動してください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure AD B2C を使用して Web アプリケーションで認証を有効にする >](tutorial-web-app-dotnet.md)
