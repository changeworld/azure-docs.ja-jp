---
title: Azure Active Directory SSO と F5 BIG-IP の統合によるヘッダーベースの認証
description: F5 BIG-IP Access Policy Manager (APM) と Azure Active Directory SSO を統合してヘッダーベースの認証を行う方法について説明します。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/10/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15d55f82483f496da314fe7b81c3369cc03d1a2f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717489"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-with-f5-big-ip-for-header-based-authentication"></a>チュートリアル: Azure Active Directory シングル サインオンと F5 BIG-IP の統合によるヘッダーベースの認証

このチュートリアルでは、F5 BIG-IP Access Policy Manager (APM) と Azure Active Directory (Azure AD) を統合して、ヘッダーベースのアプリケーションへの安全なハイブリッド アクセスを可能にする方法について説明します。

BIG-IP 公開アプリケーションと Azure AD の統合には、次のような多くの利点があります。

- Azure AD の事前認証および認可によるゼロ トラスト ガバナンスの強化

- Azure AD と BIG-IP 公開サービスの間のフル シングル サインオン (SSO)

- [Azure portal](https://azure.microsoft.com/features/azure-portal) という 1 つのコントロール プレーンからの ID とアクセスの管理

すべての利点については、[F5 BIG-IP と Azure AD の統合](./f5-aad-integration.md)に関する記事と [Azure AD を使用したアプリケーション アクセスとシングル サインオン](/azure/active-directory/active-directory-appssoaccess-whatis)に関する記事を参照してください。

## <a name="scenario-description"></a>シナリオの説明

このシナリオでは、レガシ ブローカー システムから HTTP Authorization ヘッダーを受信することによってアクセスを実現する内部アプリケーションを想定します。 これによって、ユーザーを各自のコンテンツ領域に誘導することができます。

理想的なシナリオは、Azure AD で直接アプリケーションを管理することです。 しかし、いかなる形態であれ最新のプロトコルとの相互運用機能が不足していて、最新化にかなりの労力と時間がかかることから、ダウンタイムのリスクとコストが避けられません。

その代わりに、ここではアプリケーションの接続先である内部 Azure VNet とパブリック インターネットとの間にデプロイされた BIG-IP Virtual Edition (VE) を使用します。 そうすれば、認証と承認に関する機能の選択肢が充実している Azure AD を使用して、インバウンド アクセスをゲート管理することができます。

アプリケーションの手前に BIG-IP を置くことにより、Azure AD の事前認証およびヘッダーベース SSO をサービスにオーバーレイすることが可能です。 アプリケーションのセキュリティの状態全体が飛躍的に向上し、企業は中断なく、安定的に活動を続けることができます。

このシナリオのためのセキュリティ保護されたハイブリッド アクセス ソリューションは、次のコンポーネントで構成されています。

- **アプリケーション**: Azure AD と BIG-IP のセキュリティ保護されたハイブリッド アクセスによって保護されているバックエンド サービス

- **Azure AD**: SAML ID プロバイダー (IdP) は、BIG-IP APM に対するユーザーの資格情報、条件付きアクセス (CA)、および SSO に責任を持ちます。

- **BIG-IP**: バックエンドアプリケーションに対してヘッダーベースの SSO を実行する前に、アプリケーションに対するリバース プロキシと SAML サービスプロバイダー (SP) の SAML IdP への認証を委任します。

![アーキテクチャのフロー図を示すスクリーンショット](./media/f5-big-ip-header-advanced/flow-diagram.png)

| 手順 | 説明 |
|:-------|:-----------|
| 1. | ユーザーは、アプリケーションの SAML SP エンドポイント (BIG IP APM) に接続します。 |
| 2. | APM アクセスポリシーは、事前認証のためにユーザーを SAML IdP (Azure AD) にリダイレクトします。|
| 3. | SAML IdP はユーザーを認証し、強制された CA ポリシーを適用します。 |
| 4. | Azure AD は、発行されたトークンと要求を使用してユーザーを SAML SP にリダイレクトします。 |
| 5. | BIG-IP APM がユーザーにアクセスを許可し、アプリケーションへの要求にヘッダーを挿入します。 |

このパターンを使用する組織では、セキュリティを強化するため、アプリケーションへのすべての直接アクセスをブロックすることで、BIG-IP 経由の厳密なパスを強制することもできます。

## <a name="prerequisites"></a>必須コンポーネント

以前の BIG-IP エクスペリエンスは必要ありませんが、次のものが必要です。

- Azure AD 無料サブスクリプション (またはそれ以上)

- 既存の BIG-IP。または [Azure に BIG-IP Virtual Edition (VE)](./f5-bigip-deployment-guide.md) をデプロイします。

- 次のいずれかの F5 BIG-IP ライセンス SKU

  - F5 BIG-IP® Best バンドル

  - F5 BIG-IP Access Policy Manager™ (APM) スタンドアロン ライセンス

  - 既存の BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) に対する F5 BIG-IP Access Policy Manager™ (APM) アドオン ライセンス

  - 90 日間の BIG-IP 全機能[試用版ライセンス](https://www.f5.com/trial/big-ip-trial.php)。

- オンプレミスのディレクトリから Azure AD に[同期済み](../hybrid/how-to-connect-sync-whatis.md)のユーザー ID

- Azure AD アプリケーション管理者の[アクセス許可](/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator)を持つアカウント

- HTTPS でサービスを公開するための [SSL 証明書](./f5-bigip-deployment-guide.md#ssl-profile)。またはテスト時に既定の証明書を使用します

- ヘッダーベースの既存のアプリケーション。またはテスト用に[簡単な IIS ヘッダー アプリをセットアップ](/previous-versions/iis/6.0-sdk/ms525396(v=vs.90))します

## <a name="deployment-modes"></a>デプロイ モード

このシナリオでは、ウィザードベースの 2 つのオプションのほか、詳細な構成など、BIG-IP を構成するための方法がいくつかあります。

このチュートリアルでは高度なアプローチについて説明します。これにより、すべての BIG-IP 構成オブジェクトを手動で作成することで、セキュリティで保護されたハイブリッド アクセスを実装する方法がより柔軟になります。 また、この方法は、ガイド付きの構成でカバーされていないシナリオにも使用します。

>[!NOTE]
>この記事全体で参照されている文字列または値の例はすべて、実際の環境に合わせて置き換える必要があります。

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Azure AD ギャラリーから F5 BIG-IP の追加

ビッグ IP APM と Azure AD 間に SAML フェデレーション信頼を設定することは、セキュリティで保護されたハイブリッド アクセスを実装するための最初の手順の1つです。 これにより、公開サービスへのアクセスを許可する BIG-IP が、あらかじめ事前認証と[条件付きアクセス](../conditional-access/overview.md)を Azure AD に引き継ぐうえで必要な統合が確立されます。

1. アプリケーションの管理者権限を持つアカウントを使用して、Azure AD ポータルにサインインします

2. 左のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスを選択します

3. **[エンタープライズ アプリケーション]** に移動して、上部のリボンから **[新しいアプリケーション]** を選択します

4. ギャラリーで **F5** を検索し、 **[F5 BIG-IP APM Azure AD 統合]** を選択します

5. アプリケーションの名前を指定してから、 **[追加/作成]** を選択してテナントに追加します。 その特定のサービスを反映した名前を指定してください。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成 

1. 新しい **F5** アプリケーションのプロパティが表示されている状態で、 **[管理]** > **[シングル サインオン]** の順に移動します

2. **[シングルサインオン方法の選択]** ページで、 **[SAML]** を選択してから、[**いいえ、後で保存します]** を選択し、シングル サインオンの設定を保存するプロンプトをスキップします

3. **[SAML でシングル サインオンの設定]** ブレードで **[基本的な SAML 構成]** のペン アイコンを選択して、以下の情報を指定します。

   a. 事前に定義された **[識別子]** URLを、BIG-IP で公開されたサービスの URL に置き換えます。 たとえば、`https://mytravel.contoso.com` のように指定します。

   b. **[応答 URL]** でも同じ操作を行いますが、APM の SAML エンドポイントのパスを含めます。 たとえば、`https://mytravel.contoso.com/saml/sp/profile/post/acs` のように指定します。

   >[!NOTE]
   >この構成では、SAML フローは IdP 開始モードで動作します。その場合、アプリケーションの BIG-IP サービスエンドポイントにリダイレクトされる前に、Azure AD が SAML アサーションを使用してユーザーに発行します。 BIG IP APM は、IdP モードと SP 開始モードの両方をサポートしています。

   c. では、`Logout URI` 公開するサービスのホストヘッダーによって前処理された BIG-IP APM シングルログアウト (SLO) エンドポイントを入力します。 SLO URI を入力しておくことで、Azure AD からのサインアウト後にユーザーの BIG-IP APM セッションが確実に終了します。 たとえば、`https://mytravel.contoso.com/saml/sp/profile/redirect/slr` のように指定します。

    ![基本的な SAML 構成を示すスクリーンショット](./media/f5-big-ip-header-advanced/basic-saml-configuration.png)

    >[!Note]
    >TMOS v16 以降では、SAML SLO エンドポイントが `/saml/sp/profile/redirect/slo` に変更されています。

4. **[保存]** を選択してから、[SAML 構成] ブレードを終了し、SSO テストのプロンプトをスキップします。

5. ペン アイコンを選択して **[ユーザー属性とクレーム] > [+ 新しいクレームの追加]** を編集します。

6. クレームのプロパティを次のように設定し、 **[保存]** を選択します。

   | プロパティ |説明|
   |:------|:---------|
   |名前 | Employeeid |
   | ソース属性 | user.employeeid |

   ![クレームの管理の構成画面のスクリーンショット](./media/f5-big-ip-header-advanced/manage-claims.png)

7. **[+ グループ要求を追加する]** を選択し、 **[アプリケーションに割り当てられているグループ]**  >  **[ソース属性]**  >  **[sAMAccountName]** を選択します。

   ![グループ クレームの構成画面のスクリーンショット](./media/f5-big-ip-header-advanced/group-claims.png)

8. 構成を **保存** してブレードを閉じます。

   **[ユーザー属性とクレーム]** セクションのプロパティを観察してください。 バックエンド アプリケーションに対する BIG-IP APM 認証と SSO に使用される次のプロパティが、Azure AD からユーザーに発行されます。

   ![[ユーザー属性とクレーム] の構成画面のスクリーンショット](./media/f5-big-ip-header-advanced/user-attributes-claims.png)

   BIG-IP 公開アプリケーションがヘッダーとして想定する可能性のあるクレームが他にもあれば、自由に追加してください。 既定のクレームに加えて定義されたクレームは、Azure AD に存在する場合にのみ発行されます。 同様に、ディレクトリの[ロールまたはグループ](../hybrid/how-to-connect-fed-group-claims.md)のメンバーシップも、クレームとして発行する前に Azure AD 内のユーザー オブジェクトに対して定義する必要があります。

9. **[SAML 署名証明書]** セクションで、 **[ダウンロード]** ボタンを選択して、 **[フェデレーション メタデータ XML]** ファイルをコンピューターに保存します。

   ![SAML 署名証明書を示すスクリーンショット](./media/f5-big-ip-header-advanced/saml-signing-certificate.png)

Azure AD によって作成された SAML 署名証明書には 3 年間の有効期間があり、発行された[ガイダンス](./manage-certificates-for-federated-single-sign-on.md)を使用して管理する必要があります。

### <a name="azure-ad-authorization"></a>Azure AD 認可

既定では、Azure AD はアプリケーションへのアクセスが許可されているユーザーにのみトークンが発行されます。

1. アプリケーションの構成ビューで、 **[ユーザーとグループ]** を選択します

2. **+** **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ブレードで **[ユーザーとグループ]** を選択します。

3. **[ユーザーとグループ]** ダイアログで、ヘッダーベースの内部アプリケーションへのアクセスが認可されているユーザーのグループを追加してから、 **[選択]** > **[割り当て]** の順に選択します。

これで、SAML フェデレーション信頼の Azure AD の部分が完了しました。 これで、BIG-IP APM を設定して内部 Web アプリケーションを発行し、対応する一連のプロパティで構成して、SAML 事前認証の信頼を完成させることができます。

## <a name="advanced-configuration"></a>詳細な構成

### <a name="saml-configuration"></a>[SAML 構成]

以下の手順では、公開アプリケーションの Azure AD とのフェデレーションを完了するために必要な BIG-IP SAML サービス プロバイダーと、対応する SAML IdP オブジェクトを作成します。

1. **[アクセス]**  >  **[フェデレーション]**  >  **[SAML サービス プロバイダー]**  >  **[ローカル SP サービス]**  >  **[作成]** の順に選択します

   ![SAML サービス プロバイダーの作成画面のスクリーンショット](./media/f5-big-ip-header-advanced/create-saml-sp.png)

2. **名前の指定** と、先ほど Azure AD で定義した **エンティティ ID** はまったく同じものです。

   ![新しい SAML サービス プロバイダー サービスを示すスクリーンショット ](./media/f5-big-ip-header-advanced/new-saml-sp-information.png)

   **[SP の名前設定]** が必要になるのは、エンティティ ID が発行された URL のホスト名部分と完全に一致しないか、通常のホスト名ベースの URL 形式でない場合のみです。 エンティティ ID が `urn:mytravel:contosoonline` の場合は、公開しているアプリケーションの外部スキームとホスト名を指定します。

3. 下へスクロールして新しい SAML SP オブジェクトを選択し、 **[IdP コネクタのバインドまたはバインド解除]** を選択します。

   ![新しい SAML サービス プロバイダー オブジェクト コネクタを示すスクリーンショット](./media/f5-big-ip-header-advanced/idp-connectors.png)

4. **[新しい IdP コネクタの作成]** を選択し、ドロップダウン メニューから **[メタデータから]** を選択します

   ![新しい SAML サービス IdP の編集画面のスクリーンショット](./media/f5-big-ip-header-advanced/edit-saml-idp.png)

5. 先ほどダウンロードしたフェデレーション メタデータ XML ファイルを参照し、外部 SAML IdP を表す APM オブジェクトの **[ID プロバイダー名]** を指定します たとえば、`MyTravel_AzureAD` のように指定します。

   ![新しい IdP コネクタを示すスクリーンショット](./media/f5-big-ip-header-advanced/idp-name.png)

6. 新しい **[SAML IdP コネクタ]** を選択し、その後に **[更新]** を選択するには、 **[新しい行の追加]** を選択します。

   ![IdP コネクタの更新方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/update-idp-connector.png)

7. **[OK]** を選択して設定を保存します

   ![設定の保存を示すスクリーンショット](./media/f5-big-ip-header-advanced/save-settings.png)

### <a name="header-sso-configuration"></a>ヘッダー SSO の構成

バックエンド アプリケーションへのヘッダー SSO を実行するための APM SSO オブジェクトを作成します。

1. **[Access]\(アクセス\)**  >  **[Profiles/Policies]\(プロファイルまたはポリシー\)**  >  **[Per-Request Policies]\(要求ごとのポリシー\)**  >  **[Create]\(作成\)** の順に選択します。

2. 一意のプロファイル名を指定し、 **[Accepted Language]\(使用可能な言語\)** を少なくとも 1 つ追加して **[Finished]\(完了\)** を選択します。 たとえば、「SSO_Headers」と入力します。

   ![ヘッダー構成を示すスクリーンショット](./media/f5-big-ip-header-advanced/header-configuration.png)

3. 新しく作成した要求ごとのポリシーの **[Edit]\(編集\)** リンクを選択します。

    ![要求ごとのポリシーの編集画面のスクリーンショット](./media/f5-big-ip-header-advanced/header-configuration-edit.png)

4. ビジュアル ポリシー エディターが起動したら、"fallback (フォールバック)" の横にある **[+]** 記号を選択します。

    ![ビジュアル ポリシー エディターを示すスクリーンショット](./media/f5-big-ip-header-advanced/visual-policy-editor.png)

5. ポップアップで **[General Purpose]\(汎用\)** タブに切り替え、 **[HTTP Headers]\(HTTP ヘッダー\)**  >  **[Add Item]\(項目の追加\)** を選択します。

    ![[HTTP Headers]\(HTTP ヘッダー\) の [Add Item]\(項目の追加\) を示すスクリーンショット](./media/f5-big-ip-header-advanced/add-item.png)

6. **[Add new entry]\(新しいエントリの追加\)** を選択し、**HTTP** **ヘッダーの変更** エントリを次のように 3 つ作成します。

   | プロパティ | 説明 |
   |:------|:-------|
   | ヘッダー名 | upn |
   | ヘッダー値 | %{session.saml.last.identity}|
   | ヘッダー名 | employeeid |
   | ヘッダー値 | %{session.saml.last.attr.name.employeeid} |
   | ヘッダー名 | group\_authz |
   | ヘッダー値 | %{session.saml.last.attr.name.`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`} |

   >[!Note]
   >中かっこ内で定義されている APM セッション変数は、大文字と小文字が区別されます。 そのため、Azure AD の属性名が employeeid として送信されているときに「EmployeeID」と入力すると、属性マッピング エラーが発生します。 必要な場合を除き、すべての属性を小文字で定義することをお勧めします。

   ![HTTP ヘッダーの変更画面を示すスクリーンショット](./media/f5-big-ip-header-advanced/http-header-modify.png)

7. 完了したら、 **[Save]\(保存\)** を選択してビジュアル ポリシー エディターを閉じます。

   ![要求ごとのポリシーの完成と保存を示すスクリーンショット](./media/f5-big-ip-header-advanced/per-request-policy-done.png)

### <a name="access-profile-configuration"></a>アクセス プロファイル構成

アクセス プロファイルは、アクセス ポリシー、SSO 構成、UI 設定など、BIG-IP 仮想サーバーへのアクセスを管理する多くの APM 要素をバインドします。

1. **[Access]\(アクセス\)**  >  **[Profiles / Policies]\(プロファイルまたはポリシー\)**  >  **[Access Profiles (Per-Session Policies)]\(アクセス プロファイル (セッションごとのポリシー)\)**  >  **[Create]\(作成\)** を選択して以下を指定し、 **[Finished]\(完了\)** を選択します。

   | プロパティ | 説明 |
   |:--------|:----------|
   | 名前 | MyTravel |
   | プロファイルの種類 | すべて |
   | 受け入れ可能な言語 | 少なくとも 1 つの言語を追加する|

   ![アクセス プロファイルの構成を示すスクリーンショット](./media/f5-big-ip-header-advanced/access-profile-configuration.png)

2. 先ほど作成したセッションごとのプロファイルの **[編集]** リンクを選択します。

    ![セッションごとのプロファイルの編集画面のスクリーンショット](./media/f5-big-ip-header-advanced/edit-per-session-profile.png)

3. ビジュアル ポリシー エディターが起動したら、"fallback (フォールバック)" の横にある **[+]** 記号を選択します。

   ![ビジュアル ポリシー エディターの起動方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/visual-policy-editor-launch.png)

4. ポップアップで **[認証]**  >  **[SAML 認証]**  >  **[項目の追加]** の順に選択します。

   ![SAML 認証の追加を示すスクリーンショット](./media/f5-big-ip-header-advanced/add-saml-auth.png)

5. **[SAML authentication SP]\(SAML 認証 SP\)** の構成で、先ほど作成した SAML SP オブジェクトを使用するように **[AAA Server]\(AAA サーバー\)** オプションを設定し、 **[Save]\(保存\)** を選択します。

   ![SAML 認証の SP で aaa サーバーを使用する画面のスクリーンショット](./media/f5-big-ip-header-advanced/aaa-server.png)

### <a name="attribute-mapping"></a>属性マッピング

省略可能ですが、LogonID_マッピング構成を追加すると、BIG-IP アクティブ セッションの一覧にセッション番号ではなく、ログインしているユーザーの UPN が表示されます。 これは、ログを分析したり、トラブルシューティングを行う場合に便利です。

1. [SAML Auth]\(SAML 認証\) の **[Successful]\(成功\)** ブランチの **[+]** 記号を選択します。

    ![SAML 認証ブランチの作成方法を示す画面のスクリーンショット](./media/f5-big-ip-header-advanced/create-saml-auth-branch.png)

2. ポップアップで **[割り当て]**  >  **[変数の割り当て]**  >  **[項目の追加]** の順に選択します。

   ![変数の割り当て方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/assign-variable.png)

3. わかりやすい名前を入力し、 **[Variable Assign]\(変数の割り当て\)** セクションで、 **[Add new entry]\(新しいエントリの追加\)**  >  **[change]\(変更\)** の順に選択します。 たとえば、「LogonID_Mapping」と入力します。

   ![新しいエントリの追加方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/assign-variable-change.png)

4. 次のように両方の変数を設定し、 **[Finished]\(完了\)** >
    **[Save]\(保存\)** の順に選択します。

   | プロパティ | 説明 |
   |:--------|:----------|
   | カスタム変数 | session.saml.last.identity |
   | セッション変数 | session.logon.last.username |

5. アクセス ポリシーの **[Successful]\(成功\)** ブランチの **[Deny]\(拒否\)** ターミナルを選択し、 **[Allow]\(許可\)** に変更して **[Save]\(保存\)** を選択します。

6. **[Apply Access Policy]\(アクセス ポリシーの適用\)** を選択してポリシーをコミットし、ビジュアル ポリシー エディターのタブを閉じます。

### <a name="backend-pool-configuration"></a>バックエンド プール構成

クライアント トラフィックの転送先を BIG-IP が把握できるようにするには、アプリケーションのホストとなるバックエンド サーバーを表す APM ノード オブジェクトを作成し、そのノードを APM プールに配置する必要があります。

1. **[Local Traffic]\(ローカル トラフィック\) > [Pools]\(プール\) > [Pool List]\(プール リスト\) > [Create]\(作成\)** を選択して、そのサーバー プール オブジェクトの名前を入力します。 たとえば、「MyApps_VMs」と入力します。

      ![アクセス ポリシーの適用方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/apply-access-policy.png)

2. 以下を使用してプール メンバー オブジェクトを追加します。

    | プロパティ | 説明 |
    |:--------|:----------|
    | Node Name (ノード名) | バックエンド Web アプリケーションをホストするサーバーの省略可能な表示名 |
    | Address | アプリケーションをホストするサーバーの IP アドレス|
    | Service Port (サービス ポート) | アプリケーションがリッスンしている HTTP/S ポート |

    ![プールのメンバー オブジェクトの追加方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/add-object.png)

>[!NOTE]
>正常性モニターには、このチュートリアルで説明されていない追加の[構成](https://support.f5.com/csp/article/K13397)が必要です。

## <a name="virtual-server-configuration"></a>仮想サーバーの構成

仮想サーバーは、アプリケーションに対するクライアント要求をリッスンする仮想 IP アドレスで表される BIG-IP データ プレーン オブジェクトです。 受信したトラフィックは、ポリシーの結果と設定に従って送信される前に、仮想サーバーに関連付けられている APM アクセス プロファイルに対して処理および評価されます。

1. **[Local Traffic]\(ローカル トラフィック\)**  >  **[Virtual Servers]\(仮想サーバー\)**  >  **[Virtual Server List]\(仮想サーバーの一覧\)**  >  **[Create]\(作成\)** の順に選択します。

2. クライアント トラフィックを受信するために、BIG-IP に割り当て可能な未使用の IP IPv4/IPv6 である **名前** を仮想サーバーに指定し、**サービス ポート** を 443 に設定します。

   ![新しい仮想サーバーの追加方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/new-virtual-server.png)

3. **HTTP プロファイル**: http に設定します。

4. **SSL プロファイル (クライアント)** : トランスポート層セキュリティ (TLS) を有効にし、HTTPS でサービスを発行できます。 前提条件の一部として作成したクライアント SSL プロファイルを選択するか、テストする場合は既定値のままにします。

   ![SSL プロファイル クライアントを示すスクリーンショット](./media/f5-big-ip-header-advanced/ssl-profile.png)

5. **[Source Address Translation]\(ソース アドレス変換\)** オプションを **[Auto Map]\(自動マップ\)** に変更します。

   ![自動マップ オプションを示すスクリーンショット](./media/f5-big-ip-header-advanced/change-source-address.png)

6. **[Access Policy]\(アクセス ポリシー\)** で、以前作成した **アクセス プロファイル** を設定します。 これにより、AZURE AD SAML 事前認証プロファイルとヘッダー SSO ポリシーが仮想サーバーにバインドされます。

   ![アクセス プロファイルの設定方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/set-access-profile.png)

7. 最後に、前のセクションで作成したバックエンド プール オブジェクトを設定するように **[Default Pool]\(既定のプール\)** を設定し、 **[Finished]\(完了\)** を選択します。

   ![既定のプールの設定方法を示すスクリーンショット](./media/f5-big-ip-header-advanced/default-pool.png)

## <a name="session-management"></a>セッションの管理

BIG-IP のセッション管理の設定は、ユーザー セッションが続行を中止または許可される条件、ユーザーと IP アドレスの制限、およびエラー ページを定義するために使用されます。 **[アクセス ポリシー]**  >  **[アクセス プロファイル]** の順に進み、一覧からアプリケーションを選択することで、独自のポリシーを作成できます。

SLO 機能に関して、Azure AD で SLO URI を定義すると、MyApps ポータルからの IdP によって開始されたサインアウトによって、クライアントと BIG-IP APM の間のセッションも終了します。 アプリケーションのフェデレーション エンドポイントをインポート metadata.xml、SP によって開始されるサインアウト用の Azure AD SAML ログアウト エンドポイントを APM に提供します。 しかし、本当にこれを効果的に行うには、APM はユーザーがいつサインアウトしたのかを正確に知る必要があります。

BIG-IP Web ポータルが使用されていない場合、ユーザーはサインアウトするように APM に指示する方法はありません。ユーザーがアプリケーション自体からサインアウトした場合でも、BIG-IP は技術的にはこの問題に気が付かないので、SSO を使用してアプリケーション セッションを簡単に再開できます。 このため、SP によって開始されるサインアウトでは、不要になったときにセッションが安全に終了されるのを確認するために慎重に検討する必要があります。

これを実現する 1 つの方法は、SLO 関数をアプリケーションのサインアウト ボタンに追加して、クライアントを Azure AD SAML サインアウト エンドポイントにリダイレクトする方法です。 テナントの SAML サインアウト エンドポイントに関しては、 **[アプリの登録** > **エンドポイント]** で確認できます。

アプリを変更する必要がない場合は、BIG-IP でアプリのサインアウト呼び出しをリッスンし、要求が検出されると SLO がトリガーされるのを検討してください。 BIG-IP iRules を使用してこれを実現する方法の詳細については、[記事 K42052145](https://support.f5.com/csp/article/K42052145) と[記事 K12056](https://support.f5.com/csp/article/K12056) を参照してください。

## <a name="summary"></a>まとめ

この最後の手順によって、適用されたすべての設定の概要が、そのコミット前に得られます。 **[Deploy]\(デプロイ\)** を選択してすべての設定をコミットし、アプリケーションがテナントに表示されていることを確認します。

これでアプリケーションが発行され、安全なハイブリッド アクセスで利用できるようになりました。アプリケーションには、その URL を介して直接アクセスすることも、Microsoft のアプリケーション ポータルを介してアクセスすることもできます。

このパターンを使用する組織では、セキュリティを強化するため、アプリケーションへのすべての直接アクセスをブロックすることで、BIG-IP 経由の厳密なパスを強制することもできます。

## <a name="next-steps"></a>次のステップ

ユーザーとしてブラウザーを起動し、アプリケーションの外部 URL に接続するか、Microsoft MyApps ポータルでアプリケーションのアイコンを選択します。 Azure AD への認証後、アプリケーションの BIG-IP の仮想サーバーにリダイレクトされ、SSO を通じて自動的にサインイン処理が行われます。
ヘッダーベースのアプリケーションには、挿入されたヘッダーの出力が次のように表示されます。

![出力を表示する画面のスクリーンショット](./media/f5-big-ip-header-advanced/mytravel-example.png)

## <a name="troubleshooting"></a>トラブルシューティング

ハイブリッド アクセスで保護されたアプリケーションへのアクセスができない場合は、構成の誤りなど、いくつもの要因が考えられます。

- BIG-IP のログは、認証と SSO のすべての問題を探し出すための優れた情報源です。 トラブルシューティングを行う場合は、 **[Access Policy]\(アクセス ポリシー\)**  >  **[Overview]\(概要\)**  >  **[Event Logs]\(イベント ログ\)**  >  **[Settings]\(設定\)** で、ログの詳細レベルを上げる必要があります。 発行されたアプリケーションの行を選択し、**[Edit]\(編集\)** > **[Access System Logs]\(システム ログへのアクセス\)** を選択します。 SSO の一覧から **[デバッグ]** を選択して、 **[OK]** を選択します。 これで、ログを調べる前に問題を再現できるようになりますが、完了したら忘れずに元に戻しておいてください。

- Azure AD の事前認証に続くリダイレクト後に BIG-IP ブランドのエラーが表示される場合、Azure AD から BIG-IP への SSO に関連する問題である可能性があります。 **[アクセスの** > **概要]**  >  **[アクセスレポート]** の順に移動し、過去 1 時間のレポートを実行してログに手がかりが表示されるのを確認します。 セッションに対する **[セッション変数の表示]** リンクも、APM が Azure AD から予想される要求を受信しているかどうかを把握するのに役立ちます。

- BIG-IP のエラー ページが表示されない場合、問題は、BIG-IP からバックエンド アプリケーションへの SSO に関係している可能性が高くなります。 その場合は、 **[アクセス ポリシーの** > **概要]**  >  **[アクティブ セッション]** の順に進み、アクティブ セッションのリンクを選択してください。 この場所にある **[View Variables]\(変数の表示\)** リンクも、SSO の問題、特に BIG-IP APM が適切なユーザー ID やドメイン ID を取得できない場合の根本的な原因を特定するのに役立つ場合があります。

詳細については、[BIG-IP APM 変数の割り当ての例](https://devcentral.f5.com/s/articles/apm-variable-assign-examples-1107)と [F5 BIG-IP セッション変数リファレンス](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html)を参照してください。

## <a name="additional-resources"></a>その他のリソース

詳細については、次の記事を参照してください。

- [パスワードは忘れて、これからはパスワードレス](https://www.microsoft.com/security/business/identity/passwordless)

- [条件付きアクセスとは](../conditional-access/overview.md)

- [リモート作業を可能にする Microsoft ゼロ トラスト フレームワーク](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)