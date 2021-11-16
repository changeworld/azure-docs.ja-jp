---
title: フォームベースの認証アプリケーションにとっての F5 BIG-IP APM と Azure AD SSO
description: F5 BIG-IP Access Policy Manager (APM) と Azure Active Directory を統合して、フォームベースのアプリケーションへの安全なハイブリッド アクセスを可能にする方法について説明します。
author: gargi-sinha
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8306c9f0035eababdcf5feb115786982d78f0d52
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137184"
---
# <a name="tutorial-integrate-azure-active-directory-with-f5-big-ip-for-forms-based-authentication-single-sign-on"></a>チュートリアル: フォームベース認証用の F5 BIG IP との Azure Active Directory 統合シングル サインオン

このチュートリアルでは、F5 BIG-IP Access Policy Manager (APM) と Azure Active Directory (Azure AD) を統合して、フォームベースのアプリケーションへの安全なハイブリッド アクセスを可能にする方法について説明します。

BIG-IP 公開アプリケーションと Azure AD の統合には、次のような多くの利点があります。

- Azure AD の事前認証および認可によるゼロ トラスト ガバナンスの強化

- Azure AD と BIG-IP 公開サービスの間のフル シングル サインオン (SSO)

- 1 つのコントロール プレーンからの ID とアクセスの管理 - [Azure portal](https://portal.azure.com)

すべての利点の詳細については、[F5 BIG-IP と Azure AD の統合](f5-aad-integration.md)に関する記事を参照してください。 および [Azure AD のアプリケーション アクセスとシングル サインオンとは](../active-directory-appssoaccess-whatis.md)

## <a name="scenario-description"></a>シナリオの説明

このシナリオでは、フォームベース認証 (FBA) 用に構成された内部レガシ アプリケーションを使用します。

理想的なシナリオは、アプリケーションを Azure AD を通じて直接管理することですが、最新のプロトコル相互運用機能を使用していない場合は、大幅なコストが発生し、潜在的なダウンタイムのリスクが発生します。

代わりに、インターネットと、アプリケーションが接続されている内部 Azure VNet の間にデプロイされた BIG-IP Virtual Edition (VE) を使用して受信アクセスをゲートし、さまざまな認証と承認機能の広範な選択のために、Azure AD とのアクセスを制御します。

アプリケーションの前面に BIG-IP を配置すると、Azure AD の事前認証とフォームベースの SSO でサービスをオーバーレイできるため、アプリケーションの全体的なセキュリティ体制が大幅に向上し、ビジネスを中断することなく継続的に成長させることができます。

BIG-IP APM によってキャッシュされたユーザー資格情報は、その後、他のフォーム ベースの認証アプリケーションに対する SSO で使用できます。

## <a name="scenario-architecture"></a>シナリオのアーキテクチャ

このシナリオのためのセキュリティ保護されたハイブリッド アクセス ソリューションは、次のように構成されています。

**アプリケーション**: Azure AD と BIG-IP のセキュリティ保護されたハイブリッド アクセスによって保護されているバックエンド サービス。 この特定のアプリケーションでは、Active Directory (AD) に対してユーザー資格情報の検証が行われますが、LDS (AD Lightweight Directory Services) やオープン ソースのものなど、任意のディレクトリを使用できます。

**Azure AD**: SAML ID プロバイダー (IdP) は、BIG-IP APM に対するユーザーの資格情報、条件付きアクセス (CA)、および SSO に責任を持ちます。

**BIG-IP**: バックエンドアプリケーションに対してフォームベースの SSO を実行する前に、アプリケーションに対するリバース プロキシと SAML サービスプロバイダー (SP) の SAML IdP への認証を委任します。

![スクリーンショットはフロー図を示しています](./media/f5-big-ip-forms-advanced/flow-diagram.png)

| 手順 | 説明|
|:-------|:----------|
| 1. | ユーザーは、アプリケーションの SAML SP エンドポイント (BIG IP APM) に接続します。|
| 2. | APM アクセスポリシーは、事前認証のためにユーザーを SAML IdP (Azure AD) にリダイレクトします。|
| 3. | ユーザーは Azure AD によって認証され、条件付きアクセス ポリシーが適用されます。|
| 4. | ユーザーは、発行されたトークンと要求を使用して SAML SP にリダイレクトされます。 |
| 5. | BIG-IP によってユーザーにアプリケーション パスワードが要求され、パスワードはキャッシュに格納されます。 |
| 6. | BIG-IP により、アプリケーションへの要求の送信と、ログイン フォームの受信が行われます。|
| 7. | APM スクリプトの自動応答により、フォームの送信前にユーザー名とパスワードが入力されます。|
| 8. | アプリケーションペイロードは、web サーバーによって提供され、クライアントに送信されます。 必要に応じて、APM は、応答ヘッダーを調べて Cookie またはリダイレクト URI を検索することで、成功したログオンを検出します。|

## <a name="prerequisites"></a>前提条件

以前の BIG-IP エクスペリエンスは必要ありませんが、次のものが必要です。

- Azure AD 無料サブスクリプション (またはそれ以上)

- Azure で既存の BIG-IP または [BIG-IP Virtual Edition (VE) をデプロイする](f5-bigip-deployment-guide.md)

- 次のいずれかの F5 BIG-IP ライセンス SKU

  - F5 BIG-IP&reg; Best バンドル

  - F5 BIG-IP Access Policy Manager&trade; (APM) スタンドアロン ライセンス

  - 既存の BIG-IP F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM) に対する F5 BIG-IP Access Policy Manager&trade; (APM) アドオン ライセンス

  - 90 日間の BIG-IP 全機能[試用版ライセンス](https://www.f5.com/trial/big-ip-trial.php)

- ユーザー ID   オンプレミスのディレクトリから Azure AD に[同期済み](../hybrid/how-to-connect-sync-whatis.md)

- Azure AD アプリケーション管理者の[アクセス許可](../roles/permissions-reference.md#application-administrator)を持つアカウント

- HTTPS 経由でサービスを発行するための [SSL 証明書](f5-bigip-deployment-guide.md#ssl-profile)、またはテスト時に既定値を使用します

- 既存のフォームベースの認証アプリケーション、またはテスト用 [IIS FBA アプリのセットアップ](/troubleshoot/aspnet/forms-based-authentication)

## <a name="deployment-modes"></a>デプロイ モード

このシナリオで BIG-IP を構成するための方法はいくつかあります。 このチュートリアルでは高度なアプローチについて説明します。これにより、すべての BIG-IP 構成オブジェクトを手動で作成することで、セキュリティで保護されたハイブリッド アクセスを実装する方法がより柔軟になります。 このアプローチは、テンプレート ベースのガイド付き構成の対象ではないシナリオで使用します。

>[!NOTE]
>この記事全体で参照されている文字列または値の例はすべて、実際の環境に合わせて置き換える必要があります。

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Azure AD ギャラリーから F5 BIG-IP の追加

ビッグ IP APM と Azure AD 間に SAML フェデレーション信頼を設定することは、セキュリティで保護されたハイブリッド アクセスを実装するための最初の手順の1つです。 これにより、公開されたサービスへのアクセスを許可する前に、BIG-IP から事前認証および [CA](../conditional-access/overview.md) Azure AD に渡す必要がある統合が確立されます。

1. アプリケーションの管理者権限を持つアカウントを使用して、**Azure portal** にサインインします

2. 左のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスを選択します

3. **[エンタープライズ アプリケーション]** に移動して、上部のリボンから **[新しいアプリケーション]** を選択します

4. ギャラリーで **F5** を検索し、 **[F5 BIG-IP APM Azure AD 統合]** を選択します

5. アプリケーションの名前を指定してから、 **[追加/作成]** を選択してテナントに追加します。 その特定のサービスを反映した名前を指定してください。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

1. 新しい **F5** アプリケーションのプロパティが表示されている状態で、 **[管理]**  >  **[シングル サインオン]** の順に移動します

2. **[シングルサインオン方法の選択]** ページで、 **[SAML]** を選択してから、[**いいえ、後で保存します]** を選択し、シングル サインオンの設定を保存するプロンプトをスキップします

3. **[SAML でシングル サインオンの設定]** ブレードで **[基本的な SAML 構成]** のペン アイコンを選択して、以下の情報を指定します。

   a. 事前に定義された **[識別子]** URLを、BIG-IP で公開されたサービスの URL に置き換えます。 たとえば、`https://myvacation.contoso.com` のように指定します。

   b. **[応答 URL]** でも同じ操作を行いますが、APM の SAML エンドポイントのパスを含めます。 たとえば、`https://myvacation.contoso.com/saml/sp/profile/post/acs` のように指定します。

      >[!NOTE]
      >この構成では、SAML フローは IdP 開始モードで動作します。その場合、アプリケーションの BIG-IP サービスエンドポイントにリダイレクトされる前に、Azure AD が SAML アサーションを使用してユーザーに発行します。 BIG IP APM は、IdP モードと SP 開始モードの両方をサポートしています。

   c. では、`Logout URI` 公開するサービスのホストヘッダーによって前処理された BIG-IP APM シングルログアウト (SLO) エンドポイントを入力します。 `Providing an SLO URI` Azure AD からサインアウトした後に、ユーザーの BIG-IP APM セッションも終了するようにします。 たとえば、`https://myvacation.contoso.com/saml/sp/profile/redirect/slr` のように指定します。

     ![基本的な SAML 構成を示すスクリーンショット](./media/f5-big-ip-forms-advanced/basic-saml-configuration.png)

     >[!Note]
     > TMOS v16 から、SAML SLO エンドポイントが /saml/sp/profile/redirect/slo に変更されました

4. **[保存]** を選択してから、[SAML 構成] メニューを終了し、SSO テストのプロンプトをスキップします。

   **[ユーザー属性とクレーム]** セクションのプロパティを確認します。Azure AD では、BIG-IP APM 認証と SSO のためにこれらがユーザーに発行されるためです。

5. **[SAML 署名証明書]** セクションで、 **[ダウンロード]** ボタンを選択して、 **[フェデレーション メタデータ XML]** ファイルをコンピューターに保存します。

   ![フェデレーション メタデータのダウンロード リンクを示す画像](./media/f5-big-ip-forms-advanced/saml-certificate.png)

   Azure AD によって作成された SAML 署名証明書には 3 年間の有効期間があり、発行された[ガイダンス](manage-certificates-for-federated-single-sign-on.md)を使用して管理する必要があります。

### <a name="azure-ad-authorization"></a>Azure AD 認可

既定では、Azure AD はアプリケーションへのアクセスが許可されているユーザーにのみトークンが発行されます。

1. アプリケーションの構成ビューで、 **[ユーザーとグループ]** を選択します

2. **+** **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ブレードで **[ユーザーとグループ]** を選択します

3. **[ユーザーとグループ]** ダイアログで、内部アプリケーションへのアクセスが認可されているユーザーのグループを追加してから、 **[選択]** \> **[割り当て]** の順に選択します

これで、SAML フェデレーション信頼の Azure AD の部分が完了しました。 これで、BIG-IP APM を設定して内部 web アプリケーションを発行し、対応する一連のプロパティで構成して、SAML 事前認証および SSO の信頼を完成させることができます。

## <a name="advanced-configuration"></a>詳細な構成

### <a name="service-provider"></a>サービス プロバイダー

これらの設定は、APM がレガシ アプリケーションを SAML 事前認証にオーバーレイするために使用する SAML SP プロパティを定義します。

1. **[アクセス]**  >  **[フェデレーション]**  >  **[SAML サービス プロバイダー]**  >  **[ローカル SP サービス]**  >  **[作成]** の順に選択します

   ![F5 フォームの構成を示すスクリーンショット](./media/f5-big-ip-forms-advanced/f5-forms-configuration.png)

2. **名前の指定** と、先ほど Azure AD で定義した **エンティティ ID** は全く同じものです。

   ![Sceenshot に新しい saml sp サービスが表示される](./media/f5-big-ip-forms-advanced/saml-sp-service.png)

    **[SP の名前設定]** が必要になるのは、エンティティ ID が発行された URL のホスト名部分と完全に一致しないか、通常のホスト名ベースの URL 形式でない場合のみです。 エンティティ ID が `urn:myvacation:contosoonline` の場合は、公開しているアプリケーションの外部スキームとホスト名を指定します。

### <a name="external-idp-connector"></a>外部 IdP コネクタ

SAML IdP コネクタは、BIG-IP APM が SAML IDP として Azure AD を信頼するために必要な設定を定義します。 これらの設定により、SAML SP が SAML IdP にマップされ、APM と Azure AD の間にフェデレーションの信頼が確立されます。

1. 下にスクロールして新しい SAML SP オブジェクトを選択し、 **[IdP コネクタのバインドまたはバインド解除]** を選択します

   ![Sceenshot にローカル sp サービスが表示される](./media/f5-big-ip-forms-advanced/local-services.png)

2. **[新しい IdP コネクタの作成]** を選択し、ドロップダウン メニューから **[メタデータから]** を選択します

   ![[メタデータ] ドロップダウンから [Sceenshot] が表示される](./media/f5-big-ip-forms-advanced/from-metadata.png)
  
3. 先ほどダウンロードしたフェデレーション メタデータ XML ファイルを参照し、外部 SAML IdP を表す APM オブジェクトの **[ID プロバイダー名]** を指定します。 たとえば、`MyVacation\_AzureAD` のように指定します。

   ![Sceenshot に新しい idp saml コネクタが表示される](./media/f5-big-ip-forms-advanced/new-idp-saml-connector.png)

4. 新しい **[SAML IdP コネクタ]** を選択し、その後に **[更新]** を選択するには、 **[新しい行の追加]** を選択します。
  
   ![Sceenshot に新しい行の追加が表示される](./media/f5-big-ip-forms-advanced/add-new-row.png)

5. **[OK] を選択して、これらの設定を保存する**

   ![Sceenshot に、この sp を使用する saml idp の編集が表示される](./media/f5-big-ip-forms-advanced/edit-saml-idp-using-sp.png)

### <a name="forms-based-sso"></a>フォームベースの SSO

FBA SSO は、クライアント開始モードまたは BIG-IP で実行できます。 どちらの方法でも、フォームを自動送信する前にユーザー名とパスワードのタグに資格情報を挿入することで、ユーザー ログインがエミュレートされます。 ユーザーが FBA アプリケーションにアクセスする場合にパスワードを 1 回指定する必要がある場合を除き、フローはほぼ透過的です。 その後、他の FBA アプリケーション間で再利用するためにパスワードがキャッシュされます。

ここでは、バックエンド アプリケーションへの SSO を直接処理する APM アプローチについて説明します。

**[アクセス** > **のシングルサインオン** > **フォームベース** > **の作成]** を選択し、次の情報を指定します。

|プロパティ | 説明 |
|:------|:---------|
| 名前 | SSO APM オブジェクトは、公開されている他のアプリケーションで再利用できるため、構成にわかりやすい名前を使用します。例えば `Contoso\FBA\sso`|
| SSO テンプレートを使用する | なし |
| ユーザー名のソース | パスワードコレクションフォームに事前入力する場合に推奨されるユーザー名ソース。 任意の APM セッション変数は使用できますが、既定では、`session.sso.token.last.username`ログインしているユーザーの Azure AD UPN を保持しているので、既定の動作は非常に効果的です |
| パスワード ソース | 既定値は、`session.sso.token.last.password` BIG-IP がユーザーによって指定されたパスワードをキャッシュするために使用する APM 変数のままにします |

![Sceenshot に新しい SSO 構成が表示される](./media/f5-big-ip-forms-advanced/new-sso-configuration.png)

|プロパティ | 説明 |
|:------|:---------|
| 開始 URI | FBA アプリケーションのログイン URI。 要求 URI がこの URI 値と一致すると、APM フォームベースの認証で SSO が実行されます。|
| フォーム アクション | 元の要求 URL が SSO に使用される場合は空白のままにします |
| ユーザー名のフォーム パラメーター | ログイン フォームのユーザー名フィールドの要素名。 これを判断するには、ブラウザーの開発ツールを使用します。| 
| パスワードのフォーム パラメーター | ログイン フォームのユーザー名フィールドの要素名。 同じように、開発ツールを使用します。|

![Sceenshot に sso メソッドの構成が表示される](./media/f5-big-ip-forms-advanced/sso-method-configuration.png)

![Sceenshot に contoso の休暇サインイン ページが表示される](./media/f5-big-ip-forms-advanced/contoso-example.png)

FBA SSO の APM の構成の詳細については、[こちら](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-single-sign-on-concepts-configuration-14-1-0/single-sign-on-methods.html#GUID-F8588DF4-F395-4E44-881B-8D16EED91449)を参照してください。

### <a name="access-profile-configuration"></a>アクセス プロファイル構成

アクセス プロファイルは、アクセス ポリシー、SSO 構成、UI 設定など、BIG-IP 仮想サーバーへのアクセスを管理する多くの APM 要素をバインドします。

1. **[アクセス]**  >  **[プロファイル / ポリシー]**  >  **[アクセス プロファイル (セッション単位のポリシー)]**  >  **[作成]** の順に選択して、以下を指定します。

   | プロパティ | 説明 |
   |:-----|:-------|
   | 名前 | たとえば、`MyVacation` のように指定します。 |
   |プロファイルの種類 | すべて |
   | [SSO Configuration] | 作成した FBA SSO 構成オブジェクト|
   |受け入れ可能な言語 | 少なくとも 1 つの言語を追加する|

   ![新しいアクセス プロファイルの作成を示す Sceenshot](./media/f5-big-ip-forms-advanced/create-new-access-profile.png)

2. セッション ポリシーを変更して、ユーザー名が事前入力されたログオン ページを表示します。 先ほど作成したセッションごとのプロファイルの **[編集]** リンクを選択して、APM ビジュアルポリシーエディター (VPE) を起動します。

   ![セッションごとの編集ポリシーを示すSceenshot](./media/f5-big-ip-forms-advanced/edit-per-session-policy.png)

3. VPE が起動したら、フォールバックの横 **+** にある記号を選択します。

   ![VPE が起動すると、フォールバックの横に [+] と表示されます](./media/f5-big-ip-forms-advanced/vpe-launched.png)

4. ポップアップで **[認証]**  >  **[SAML 認証]**  >  **[項目の追加]** の順に選択します。

   ![Sceenshot に [SAML 認証] と [項目の追加] ボタンが表示される](./media/f5-big-ip-forms-advanced/saml-auth-add-item.png)

5. **[SAML 認証 SP]** 構成で、名前を **[Azure AD 認証]** に変更し、先ほど作成した SAML SP オブジェクトを使用するように **AAA サーバー** を設定します。

   ![Sceenshot に Azure AD 認証サーバーの設定が表示される](./media/f5-big-ip-forms-advanced/azure-ad-auth-server.png)

6. **成功** したブランチの **+** [サインオン] を選択します。

7. ポップアップで **[認証]**  >  **[ログオン ページ]**  >  **[項目の追加]** の順に選択します。

   ![Sceenshot にログオン ページの設定が表示される](./media/f5-big-ip-forms-advanced/logon-page.png)

8. [ユーザー名] フィールドの **[読み取り専用]** オプションを **[はい]** に変更します。

   ![Sceenshot の [読み取り専用] が [はい] に設定されている](./media/f5-big-ip-forms-advanced/set-read-only-as-yes.png)

9. ログオン ページのフォールバックの **+** 記号を選択して、SSO 資格情報のマッピング オブジェクトを追加します。

10. ポップアップ ウィンドウで、 **[割り当て]**  >  **[SSO 資格情報マッピング]** **> [項目の追加]** の順に選択します。

    ![Sceenshot に SSO 資格情報マッピング情報が表示される](./media/f5-big-ip-forms-advanced/sso-credential-mapping.png)

11. ポップアップに既定の設定を表示したままにして続行します。

    ![Sceenshot に SSO 資格情報マッピング情報の保存が表示される](./media/f5-big-ip-forms-advanced/save-sso-credential-mapping.png)

12. 上部の **[拒否]** ボックス内のリンクを選択し、**成功の** ブランチを **[許可]**  >  **[保存]** の順に変更します。

**属性マッピング**

省略可能ですが、LogonID_マッピング構成を追加すると、BIG-IP アクティブ セッションの一覧にセッション番号ではなく、ログインしているユーザーの UPN が表示されます。 これは、ログを分析したり、トラブルシューティングを行う場合に便利です。

1. **+** SAML 認証 **の成功** ブランチのシンボルを選択します。

2. ポップアップで **[割り当て]**  >  **[変数の割り当て]**  >  **[項目の追加]** の順に選択します。

   ![Sceenshot に変数の割り当て情報が表示される](./media/f5-big-ip-forms-advanced/variable-assign.png)

3. わかりやすい名前を入力し、 **[変数の割り当て]** セクションで **[新しいエントリ** > **変更** の追加] を選択します。 たとえば、「 `LogonID_Mapping` 」のように入力します。

   ![Sceenshot に新しいエントリ フィールドの追加が表示される](./media/f5-big-ip-forms-advanced/add-new-entry.png)

4. 両方の変数を設定して、以下を使用します。

   | プロパティ | 説明 |
   |:-----|:-------|
   | カスタム変数 | `session.logon.last.username` |
   | セッション変数 | `session.saml.last.identity`|

   次に、 **[完了]**  >  **[保存] の順に選択します**。

5. 左上隅にある **[アクセス ポリシーの適用]** を選択してこれらの設定をコミットし、ビジュアル ポリシー エディターを閉じます。

   ![Sceenshot にアクセス ポリシーの適用が表示される](./media/f5-big-ip-forms-advanced/apply-access-policy.png)

### <a name="backend-pool-configuration"></a>バックエンド プール構成

BIG-IP でクライアント トラフィックを転送する場所を確認するには、アプリケーションをホストするバックエンド サーバーを表す BIG-IP ノード オブジェクトを作成する必要があります。 その後、そのノードを BIG-IP サーバー プールに配置します。

1. **[ローカル トラフィック**  > **プール]**  >  **[プールの一覧** > **作成]** の順に選択し、サーバー プール オブジェクトの名前を指定します。 たとえば、「 `MyApps_VMs` 」のように入力します。

   ![Sceenshot にプールの一覧が表示される](./media/f5-big-ip-forms-advanced/pool-list.png)

2. 以下を使用してプール メンバー オブジェクトを追加します。

   | プロパティ | 説明 |
   |:-----|:-------|
   | Node Name (ノード名) | バックエンド Web アプリケーションをホストするサーバーの省略可能な表示名 |
   | Address | アプリケーションをホストするサーバーの IP アドレス |
   | Service Port (サービス ポート) | アプリケーションがリッスンしている HTTP/S ポート |

   ![Sceenshot にプール メンバーが表示される](./media/f5-big-ip-forms-advanced/pool-member.png)

>[!NOTE]
>正常性モニターには、このチュートリアルで説明されていない追加の[構成](https://support.f5.com/csp/article/K13397)が必要です。

## <a name="virtual-server-configuration"></a>仮想サーバーの構成

仮想サーバーは、アプリケーションに対するクライアント要求をリッスンする仮想 IP アドレスで表される BIG-IP データ プレーン オブジェクトです。 受信したトラフィックは、ポリシーの結果と設定に従って送信される前に、仮想サーバーに関連付けられている APM アクセス プロファイルに対して処理および評価されます。

1. **[ローカル トラフィック]**  >  **[仮想サーバー]**  >  **[仮想サーバーの一覧]**  >  **[作成]** の順に選択します。

2. クライアント トラフィックを受信するために、BIG-IP に割り当て可能な未使用の IP IPv4/IPv6 である **名前** を仮想サーバーに指定し、**サービス ポート** を 443 に設定します。

   ![Sceenshot に仮想サーバーが表示される](./media/f5-big-ip-forms-advanced/virtual-server.png)

3. **HTTP プロファイル**: http に設定します。

4. **SSL プロファイル (クライアント)** : トランスポート層セキュリティ (TLS) を有効にし、HTTPS 経由でサービスを発行できます。 事前要求の一部として作成したクライアント SSL プロファイルを選択するか、テストする場合は既定値のままにします。

   ![Sceenshot に SSL プロファイルが表示される](./media/f5-big-ip-forms-advanced/ssl-profile.png)

5. **[ソース アドレス変換]** を **[自動マップ]** に変更します。

   ![Sceenshot に自動マップが表示される](./media/f5-big-ip-forms-advanced/auto-map.png)

6. **[アクセス ポリシー]** で、以前作成した **[アクセス プロファイル]** を設定します。 これにより、AZURE AD SAML 事前認証プロファイルと FBA SSO ポリシーが仮想サーバーにバインドされます。

   ![Sceenshot にアクセス ポリシーが表示される](./media/f5-big-ip-forms-advanced/access-policy.png)

7. 最後に、**既定のプール** を前のセクションで作成したバックエンド プール オブジェクトに設定します。

   ![Sceenshot に既定のプールが表示される](./media/f5-big-ip-forms-advanced/default-pool.png)

## <a name="session-management"></a>セッションの管理

BIG-IP のセッション管理の設定は、ユーザー セッションが終了されるか続行が許可される条件、ユーザーと IP アドレスの制限、およびエラー ページを定義するために使用されます。 **[アクセス ポリシー]**  >  **[アクセス プロファイル]** の順に進み、一覧からアプリケーションを選択することで、独自のポリシーを作成できます。

SLO 機能に関して、Azure AD で 1 つのログアウト URI を定義すると、MyApps ポータルからの IdP によって開始されたサインアウトによって、クライアントと BIG-IP APM の間のセッションも終了されます。

アプリケーションのフェデレーション用 metadata.xml をインポートしてから、SP によって開始されるサインアウトのために、Azure AD SAML SLO エンドポイントを APM に提供します。 しかし、本当にこれを効果的に行うには、APM はユーザーがいつサインアウトしたのかを正確に知る必要があります。

BIG-IP Web ポータルが使用されていない場合、ユーザーはサインアウトするように APM に指示する方法はありません。ユーザーがアプリケーション自体からサインアウトした場合でも、BIG-IP は技術的にはこの問題に気が付かないので、SSO を使用してアプリケーション セッションを簡単に再開できます。 このため、SP によって開始されるサインアウトでは、不要になったときにセッションが安全に終了されるのを確認するために慎重に検討する必要があります。

これを実現する 1 つの方法は、SLO 関数をアプリケーションのサインアウト ボタンに追加して、クライアントを Azure AD SAML サインアウト エンドポイントにリダイレクトする方法です。 テナントの SAML サインアウト エンドポイントに関しては、 **[アプリの登録** > **エンドポイント]** で確認できます。

アプリを変更する必要がない場合は、BIG-IP でアプリのサインアウト呼び出しをリッスンし、要求が検出されると SLO がトリガーされるのを検討してください。 BIG-IP iRules を使用してこれを実現する方法の詳細については、[記事 K42052145](https://support.f5.com/csp/article/K42052145) と F5 の [記事 K12056](https://support.f5.com/csp/article/K12056) を参照してください。

## <a name="summary"></a>まとめ

これで、アプリケーションが発行され、セキュリティで保護されたハイブリッド アクセスで、その URL を介して直接または Microsoft のアプリケーション ポータルを介してアクセスでする必要があります。

また、アプリケーションはターゲット リソースとして Azure AD CA があります。 CA ポリシーを構築するための[ガイダンス](../conditional-access/concept-conditional-access-policies.md)を参照してください。

このパターンを使用する組織では、セキュリティを強化するため、アプリケーションへのすべての直接アクセスをブロックすることで、BIG-IP 経由の厳密なパスを強制することもできます。

## <a name="next-steps"></a>次のステップ

ブラウザーから、アプリケーションの外部 URL に接続するか、MyApps ポータルでアプリケーションのアイコンを選択します。 Azure AD に対する認証後、そのアプリケーション用の BIG-IP エンドポイントにリダイレクトされ、パスワードの入力を求められます。 APM により、Azure AD からの UPN が、どのように事前にユーザー名に格納されるかに注意してください。 APM によって事前に格納されたユーザー名は、Azure AD とバックエンド アプリケーションとの間でセッションの一貫性を確保するため、読み取り専用になります。 必要な場合は、追加の構成によって、ビューではこのフィールドを非表示にできます。

![Sceenshot にセキュリティで保護された SSO が表示される](./media/f5-big-ip-forms-advanced/secured-sso.png)

送信されると、ユーザーはアプリケーションに自動的にサインインされるはずです。

![Sceenshot にウェルカム メッセージが表示される](./media/f5-big-ip-forms-advanced/welcome-message.png)

## <a name="troubleshoot"></a>トラブルシューティング

ハイブリッド アクセスで保護されたアプリケーションへのアクセスができない場合は、構成の誤りなど、いくつもの要因の可能性が考えられます。 トラブルシューティングを行う場合は、次の点に注意してください。

- FBA SSO は、BIG-IP が指定された URI でログイン フォームを解析し、構成で定義されているユーザー名とパスワード要素タグを探して実行されます。

- 要素タグは一貫性が必要で、一貫性が確保されていないと SSO は失敗します。 動的に生成されるより複雑なフォームでは、ログイン フォームの構造を理解するために、開発ツールを使用したより詳細な分析が必要な場合があります。

- クライアントが開始するアプローチは、フォーム名を指定し、JavaScript フォーム ハンドラー ロジックをカスタマイズすることができるので、複数のフォームを含むログイン ページに適している場合があります。

- FBA SSO のどちらの方法でも、すべてのフォームの相互作用を非表示にすることでユーザー エクスペリエンスとセキュリティが最適化されますが、場合によっては、資格情報が実際に挿入されているかどうかを検証すると便利な場合があります。 これは、SSO プロファイルでフォームの自動送信を無効にしてから、開発ツールを使用してログイン ページが表示されない 2 つのスタイル プロパティを無効にすることで、クライアント開始モードでは可能です。

  ![Sceenshot にプロパティが表示される](./media/f5-big-ip-forms-advanced/properties.png)

BIG-IP のログは、認証と SSO のすべての問題を探し出すための優れた情報源です。 トラブルシューティングを行う場合は、ログの詳細レベルを上げる必要があります。

1. **[Access Policy]\(アクセス ポリシー\)** > **[Overview]\(概要\)** > **[Event Logs]\(イベント ログ\)** > **[Settings]\(設定\)** に移動します。

2. 発行されたアプリケーションの行を選択し、**[Edit]\(編集\)** > **[Access System Logs]\(システム ログへのアクセス\)** を選択します。

3. SSO の一覧から **[デバッグ]** を選択して、 **[OK]** を選択します。 次に、ログを調べる前に問題を再現できるようになりますが、完了したら忘れずに元に戻しておいてください。

Azure AD 事前認証が成功した直後に、BIG-IP ブランドのエラーが表示される場合は、Azure AD から BIG-IP への SSO に関連する問題である可能性があります。

**[アクセスの** > **概要]**  >  **[アクセスレポート]** の順に移動し、過去 1 時間のレポートを実行してログに手がかりが表示されるのを確認します。 セッションに対する **[セッション変数の表示]** リンクも、APM が Azure AD から予想される要求を受信しているかどうかを把握するのに役立ちます。

BIG-IP のエラー ページが表示されない場合、問題はバックエンド要求や、BIG-IP からアプリケーションへの SSO に関係している可能性が高くなります。 その場合は、 **[アクセス ポリシーの** > **概要]**  >  **[アクティブ セッション]** の順に進み、アクティブ セッションのリンクを選択します。

この場所にある **[変数の表示]** リンクは、特に APM が適切なユーザー識別子とパスワードを取得できない場合、根本的な原因を特定するのに役立つ場合があります。

詳細については、F5 の BIG-IP [[セッション変数のリファレンス]](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html) を参照してください。
