---
title: F5 BIG-IP での Azure Active Directory B2C の構成に関するチュートリアル
titleSuffix: Azure AD B2C
description: セキュリティ保護されたハイブリッド アクセスのために Azure AD B2C 認証を F5 BIG-IP と統合する方法について説明します
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.service: active-directory
ms.subservice: B2C
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: ca0d912c837a4c3fb218d1bb3fb8b07b43100119
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044629"
---
# <a name="tutorial-extend-azure-active-directory-b2c-to-protect-on-premises-applications-using-f5-big-ip"></a>チュートリアル: F5 BIG-IP を使用してオンプレミスのアプリケーションを保護するように Azure Active Directory B2C を拡張する

このサンプル チュートリアルでは、Azure Active Directory (Azure AD) B2C と [F5 BIG-IP Access Policy Manager (APM)](https://www.f5.com/services/resources/white-papers/easily-configure-secure-access-to-all-your-applications-via-azure-active-directory) を統合する方法について説明します。 このチュートリアルでは、Azure AD B2C の事前認証、条件付きアクセス (CA)、シングル サインオン (SSO) と組み合わせた BIG-IP セキュリティにより、レガシ アプリケーションをインターネットに安全に公開する方法を示します。

F5 Inc. では、コンピューティング、ストレージ、ネットワーク リソースの可用性など、接続されたサービスの配信、セキュリティ、パフォーマンス、可用性に焦点が当てられています。 ハードウェア、モジュール化されたソフトウェア、クラウド対応の仮想アプライアンス ソリューションが提供されます。

F5 の BIG-IP Application Delivery Controller (ADC) は、多くの場合、プライベート ネットワークとインターネットの間にセキュリティ保護されたゲートウェイとして展開されます。
それにより、アプリケーション レベルの検査や完全にカスタマイズ可能なアクセス制御など、豊富な機能が提供されます。 リバース プロキシとして展開された BIG-IP を使用すると、F5 の APM によって管理されるフェデレーション ID アクセス層をサービスのフロントエンドにすることで、重要なビジネス アプリケーションへのセキュリティ保護されたハイブリッド アクセスを実現することもできます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)

- 既存の BIG-IP、または試用版の [BIG-IP 仮想環境 (VE) を Azure](../active-directory/manage-apps/f5-bigip-deployment-guide.md) にデプロイします

- 次のいずれかの F5 BIG-IP ライセンス SKU

  - F5 BIG-IP® Best バンドル

  - F5 BIG-IP Access Policy Manager™ スタンドアロン ライセンス

  - 既存の BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) での F5 BIG-IP Access Policy Manager™ アドオン ライセンス

  - 90 日間の BIG-IP 全機能[試用版ライセンス](https://www.f5.com/trial/big-ip-trial.php)

- 既存のヘッダー ベースの Web アプリケーション、またはテスト用に [IIS アプリをセットアップ](/previous-versions/iis/6.0-sdk/ms525396(v=vs.90))します

- HTTPS 経由でサービスを発行するための [SSL 証明書](../active-directory/manage-apps/f5-bigip-deployment-guide.md#ssl-profile)、またはテスト時に既定値を使用します。

## <a name="scenario-description"></a>シナリオの説明

このシナリオでは、レガシ ブローカー システムから受け取った HTTP Authorization ヘッダーに依存してアクセスを行う内部アプリケーションがあります。これにより、販売エージェントはそれぞれのコンテンツ領域に移動されます。

サービスは、より広範なコンシューマー ベースに拡張する必要があるため、アプリケーションでは、コンシューマー認証オプションの選択を提供するようにアップグレードするか、より適切なソリューションで完全に置き換える必要があります。

理想的なのは、最新のコントロール プレーンを介した直接管理をサポートするようにアプリケーションをアップグレードすることです。 しかし、それには最新の形式の相互運用がないため、最新化にかなりの労力と時間がかかり、予想外のコストが発生し、ダウンタイムのリスクが発生します。

代わりに、パブリック インターネットと、アプリケーションが接続されている内部 Azure VNet の間にデプロイされた BIG-IP Virtual Edition (VE) を使用して、サインインとサインアップの機能の広範な選択のために、Azure AD B2C とのアクセスを制御します。

アプリケーションの前面に BIG-IP を配置すると、Azure AD B2C の事前認証とヘッダー ベースの SSO でサービスをオーバーレイできるため、アプリケーションの全体的なセキュリティ体制が大幅に向上し、ビジネスを中断することなく継続的に成長させることができます。

このシナリオのためのセキュリティ保護されたハイブリッド アクセス ソリューションは、次のコンポーネントで構成されています。

- **アプリケーション** - Azure AD B2C と BIG-IP のセキュリティ保護されたハイブリッド アクセスによって保護されているバックエンド サービス

- **Azure AD B2C** - ユーザーの資格情報、多要素認証 (MFA)、および BIG-IP APM への SSO の検証を担当する IdP および Open ID Connect (OIDC) 承認サーバー。

- **BIG-IP** - アプリケーション用のリバース プロキシとして、BIG-IP APM は OIDC クライアントにもなり、バックエンド サービスへのヘッダー ベースの SSO を実行する前に、OIDC 承認サーバーに認証を委任します。

次の図は、このシナリオでのサービス プロバイダー (SP) によって開始されるフローを示したものです。

![このシナリオで SP によって開始されるフローを示すスクリーンショット](./media/partner-f5/flow-diagram.png)

|手順| 説明|
|:----|:-------|
| 1. | ユーザーは、BIG-IP がサービス プロバイダーであるアプリケーション エンドポイントに接続します |
| 2. | ユーザーは、OIDC クライアントである BIG-IP APM によって、OIDC 承認サーバーである Azure AD B2C テナント エンドポイントにリダイレクトされます |
| 3. | ユーザーは Azure AD B2C テナントによって事前認証され、条件付きアクセス ポリシーが適用されます |
|4. | ユーザーは、Azure AD B2C によって承認コードと共に SP にリダイレクトされて戻されます |
| 5. | OIDC クライアントにより、承認サーバーに、認証コードと ID トークンの交換が要求されます |
| 6. | BIG-IP APM によってユーザー アクセスが許可され、アプリケーションに転送されるクライアント要求に HTTP ヘッダーが挿入されます |

このパターンを使用する組織では、セキュリティを強化するため、アプリケーションへのすべての直接アクセスをブロックすることで、BIG-IP 経由の厳密なパスを強制することもできます。

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C の構成

Azure AD B2C の認証で BIG-IP を有効にするには、適切なユーザー フローまたはカスタム ポリシーが設定された Azure AD B2C テナントが必要です。 [Azure AD B2C のユーザー フローを設定](tutorial-create-user-flows.md)します。

### <a name="create-custom-attributes"></a>カスタム属性を作成する

カスタム属性はさまざまなソースから取得できます。たとえば、既存の Azure AD B2C ユーザー オブジェクトから直接取得したり、フェデレーション IdP や API コネクタに要求したり、ユーザーのサインアップの過程で収集したりすることができます。 要求されたら、アプリケーションに送信されるトークンにそれを含めることができます。

レガシ アプリケーションには特定の属性が必要であるため、これらの属性をユーザー フローに含めます。 ただし、これらの属性は、アプリケーションで必要な任意の属性に置き換えることができます。 または、前提条件の指示に従ってテスト アプリを設定する場合は、すれらがすべて表示されるのでどのヘッダーでもかまいません。

1. Azure AD B2C テナントのポータルにサインインします

2. 左側のペインで **[ユーザー属性]** を選択してから、**[追加]** を選択して 2 つのカスタム属性を作成します

   - エージェント ID: 文字列 **データ型**

   - エージェント geo: 文字列 **データ型**

### <a name="add-attributes-to-user-flow"></a>属性をユーザー フローに追加する

1. 左側のペインで、**[ポリシー]** > **[ユーザー フロー]** に移動します。

2. ポリシー (**B2C_1_SignupSignin** など) を選択します

3. **[ユーザー属性]** を選択して両方のカスタム属性を追加し、さらに **[表示名]** 属性も追加します。 これらは、ユーザーのサインアップの間に収集される属性です。

4. **[アプリケーション要求]** を選択して両方のカスタム属性を追加し、**[表示名]** も追加します。 これらは、BIG-IP に送信される属性です。

左側のナビゲーション バーにあるユーザー フロー メニューの [[ユーザー フローを実行します]](tutorial-create-user-flows.md) 機能を使用して、定義されているすべての属性の入力が求められることを確認できます。

### <a name="azure-ad-b2c-federation"></a>Azure AD B2C のフェデレーション

BIG-IP と Azure AD B2C が相互に信頼するには、フェデレーションが必要であるため、BIG-IP を OIDC アプリケーションとして Azure AD B2C テナントに登録する必要があります。

1. まだ Azure AD B2C ポータルで、**[アプリの登録]** >  **[新規登録]** を選択します。

2. アプリケーションの名前を入力します。 たとえば、**HeaderApp1** などとします

3. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します

4. **[リダイレクト URI]** で **[Web]** を選択し、保護対象のサービスのパブリック FQDN とパスを入力します。

5. 残りはそのままにして、**[登録]** 選択します。

6. **[証明書とシークレット]** > **[+ 新しいクライアント シークレット]** に移動します。

7. BIG-IP によって使用されるシークレットのわかりやすい名前と TTL を指定します。

8. クライアント シークレットを記録しておきます。これは、後で BIG-IP を構成するために必要になります。

リダイレクト URI は、認証の後で承認サーバー (Azure AD B2C) によってユーザーが送り返される BIG-IP エンドポイントです。 Azure AD B2C に[アプリケーションを登録](tutorial-register-applications.md)します。

## <a name="big-ip-configuration"></a>BIG-IP の構成

BIG-IP には Azure AD のセキュリティ保護されたハイブリッド アクセスを構成するための方法がいくつか用意されており、その中のウィザード ベースの Guided Configuration を使用すると、最低限の時間と労力で複数の一般的なシナリオを実装できます。 そのワークフロー主導のフレームワークは、特定のアクセス トポロジに合わせて調整された直感的なエクスペリエンスを備えており、発行のために最小限必要な構成で Web サービスを迅速に発行するために使用されます。

### <a name="version-check"></a>バージョン チェック

このチュートリアルは Guided Configuration v.7/8 に基づいていますが、以前のバージョンにも適用できます。 バージョンを調べるには、管理者アカウントを使用して BIG-IP の Web 構成にログインし、**[Access]\(アクセス\)** > **[Guided Configuration]\(ガイド付き構成\)** に移動します。 右上隅にバージョンが表示されます。 BIG-IP の Guided Configuration をアップグレードするには、[こちらの手順](https://support.f5.com/csp/article/K85454683)のようにします。

### <a name="ssl-profiles"></a>SSL プロファイル

クライアントの SSL プロファイルを使用して BIG-IP を構成すると、TLS 経由のクライアント側トラフィックをセキュリティ保護できます。 これを行うには、アプリケーションの公開 URL によって使用されるドメイン名と一致する証明書をインポートする必要があります。 可能であれば、公開証明機関を使用することをお勧めしますが、テスト時には組み込みの BIG-IP 自己署名証明書を使用することもできます。
BIG-IP VE で[証明書を追加して管理](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-ssl-administration-13-0-0.html)します。

## <a name="guided-configuration"></a>ガイド付き構成

1. Web 構成で **[Access]\(アクセス\)** > **[Guided Configuration]\(ガイド付き構成\)** に移動して、デプロイ ウィザードを起動します。

2. **[Federation]\(フェデレーション\)** > **[F5 as OAuth Client and Resource Server]\(OAuth クライアントおよびリソース サーバーとして F5\)** を選択します。

3. このシナリオのフローの概要を確認し、**[Next]\(次へ\)** を選択してウィザードを開始します。

### <a name="oauth-properties"></a>OAuth のプロパティ

このセクションでは、BIG-IP APM と OAuth 承認サーバー (お使いの Azure AD B2C テナント) の間のフェデレーションを有効にするプロパティを定義します。 OAuth は BIG-IP の構成全体を通して参照されますが、ソリューションで実際に使用されるのは OAuth 2.0 プロトコル上のシンプルな ID レイヤーである OIDC であり、これにより、OIDC クライアントはユーザーの ID を確認したり、他のプロファイル情報を取得したりできます。

間違えると認証とアクセスに影響するため、細部まで十分に注意してください。

#### <a name="configuration-name"></a>構成名

構成の表示名を指定すると、ガイド付き構成に最終的に存在する可能性のある多数のデプロイ構成を区別するのに役立ちます。 いったん設定した名前は変更できず、Guided Configuration のビューでのみ見ることができます。

#### <a name="mode"></a>モード

BIG-IP APM は OIDC クライアントとして機能するので、[Client]\(クライアント\) オプションのみを選択します。

#### <a name="dns-resolver"></a>DNS リゾルバー

指定するターゲットによって、Azure AD B2C エンドポイントのパブリック IP アドレスを解決できる必要があります。 既存のパブリック DNS リゾルバーを選択するか、新しいものを作成します。

#### <a name="provider-settings"></a>プロバイダーの設定

ここでは、Azure AD B2C を OAuth2 IdP として構成します。 Guided Configuration v8 には Azure AD B2C テンプレートが用意されていますが、いくつかのスコープが不足しているため、ここではカスタムの種類を使用します。 F5 により、今後の Guided Configuration の更新に足りないスコープを組み込む作業が行われています。 新しいプロバイダーを追加して、次のように構成します。

- **OAuth の全般プロパティ**

  | Properties | 説明 |
  |:-------|:---------|
  |OAuth プロバイダーの種類 | Custom |
  | OAuth プロバイダーを選択します | 新しく作成します (または、存在する場合は既存の OAuth プロバイダーを使用します) |
  | 名前 | B2C IdP の一意の表示名。 この名前は、サインイン対象のプロバイダー オプションとしてユーザーに表示されます。|
  | トークンの種類 | JSON Web トークン |

- **OAuth ポリシーの設定**

  | Properties | 説明 |
  |:-----------|:----------------|
  | Scope | 空白のままにします。ユーザーをサインインさせるための OpenID スコープが自動的に追加されます |
  | [付与タイプ] | Authorization code (承認コード) |
  | Enable OpenID Connect (OpenID Connect を有効にする) | オンにして APM OAuth クライアントを OIDC モードにします |
  | フローの種類 | Authorization code (承認コード) |

- **OAuth プロバイダーの設定**

  以下の OpenID URI は、署名証明書のロールオーバーなどの重要な IdP 情報を自動検出するために OIDC クライアントによって使用されるメタデータ エンドポイントを参照します。 **[アプリの登録]** > **[エンドポイント]** に移動し、Azure AD B2C の OpenID Connect メタデータ ドキュメントの URI をコピーすることで、Azure AD B2C テナント用のメタデータ エンドポイントを見つけます。 たとえば、「 `https://wacketywackb2c .b2clogin.com/<tenantname>.onmicrosoft.com/<policyname>/v2.0/.well-known/openid-configuration` 」のように入力します。

  次に、独自のプロパティ `https://<tenantname>.b2clogin.com/WacketywackB2C.onmicrosoft.com/B2C_1_SignUpIn/v2.0/.well-known/openid-configuration` を使用して URI を更新します。

  この URI をブラウザーに貼り付けて、自分の Azure AD B2C テナントの OIDC メタデータを表示します。

  | Properties | 説明 |
  |:----------|:----------|
  | 対象ユーザー | Azure AD B2C テナント内で BIG-IP を表すアプリケーションのクライアント ID |
  | 認証 URI | B2C OIDC メタデータの承認エンドポイント |
  | トークン URI | Azure AD B2C メタデータ内のトークン エンドポイント |
  | Userinfo request URI (ユーザー情報要求 URI) | 空のままにします。 現在、Azure AD B2C ではこの機能はサポートされていません |
  |OpenID URI | 上で作成した OpenID URI メタデータ エンドポイント |
  | Ignore expired certificate validation (期限切れの証明書の検証を無視する) | オフのまま |
  | Allow self-signed JWK config certificate (自己署名された JWK 構成証明書を許可する) | ○ |
  | Trusted CA bundle (信頼された CA バンドル) | ca-bundle.crt を選択して、F5 の既定の信頼された機関を使用します |
  | Discovery interval (検出の間隔) | Azure AD B2C テナントの更新プログラムを BIG-IP で照会する適切な間隔を指定します。 AGC バージョン 16.1 0.0.19 final によって提供される最小間隔は 5 分です。|

- **OAuth サーバーの設定**

  このセクションでは、OIDC 承認サーバー (自分の Azure AD B2C テナント) を参照します。

  |Properties | 説明|
  |:---------|:---------|
  | クライアント ID | Azure AD B2C テナント内で BIG-IP を表すアプリケーションのクライアント ID。 |
  | クライアント シークレット | アプリケーションの対応するクライアント シークレット。 |
  |Client-server SSL profile (クライアント サーバー SSL プロファイル) | SSL プロファイルを設定すると、APM と Azure AD B2C IdP が TLS 経由で通信できるようになります。 既定のオプションを `serverssl` 選択します。 |

- **OAuth 要求の設定**

  興味深いことに、BIG-IP では、Azure AD B2C で必要なすべての要求が、事前に構成された要求セットに収められています。 ただし、実装に使用したビルドでは、これらの要求の形式が正しくなく、重要なパラメーターがないことが確認されました。 そのため、手動で作成することを選択します。

- **トークン要求 - 有効**

  | Properties | 説明 |
  |:-----------|:------------|
  | Choose OAuth request (OAuth 要求の選択) | 新規作成 |
  | HTTP メソッド | POST |
  | Enable headers (ヘッダーを有効にする)| オフ |
  | Enable parameters (パラメーターを有効にする) | オン |

  | パラメーターのタイプ | パラメーター名 | パラメーター値|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | nonce | nonce| |
  | redirect-uri | redirect-uri | |
  | scope | scope | |
  | response-type | response-type | |
  | client-secret | client-secret | |
  | custom | grant_type | authorization_code |

- **認証リダイレクト要求 - 有効**

  | Properties | 説明 |
  |:-----------|:------------|
  | Choose OAuth request (OAuth 要求の選択) | 新規作成 |
  | HTTP メソッド | GET |
  | プロンプトの種類 | なし |
  | Enable headers (ヘッダーを有効にする) | オフ |
  | Enable parameters (パラメーターを有効にする) | オン |

  | パラメーターのタイプ | パラメーター名 | パラメーター値|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | redirect-uri | redirect-uri | |
  | response-type |response-type | |
  | scope | scope | |
  | nonce | nonce | |

- **トークン更新要求** - **無効** - 必要な場合は、有効にして構成できます。

- **OpenID UserInfo 要求** - **無効** - グローバル Azure AD B2C テナントでは、現在はサポートされていません。

- **仮想サーバーのプロパティ**

  セキュリティ保護されたハイブリッド アクセスによって保護されているバックエンド サービスに対する外部クライアントの要求をインターセプトするには、BIG-IP 仮想サーバーを作成する必要があります。 仮想サーバーには、アプリケーションを表す BIG-IP サービス エンドポイントのパブリック DNS レコードにマップされる IP を割り当てる必要があります。 使用可能な場合は既存の仮想サーバーを使用し、それ以外の場合は次のように指定します。

  | Properties | 説明 |
  |:-----------|:------------|
  | 宛先アドレス | バックエンド アプリケーションに対する BIG-IP サービス エンドポイントになるプライベートまたはパブリック IP |
  | サービス ポート | HTTPS |
  | Enable redirect port (リダイレクト ポートを有効にする) | ユーザーを http から https に自動的にリダイレクトするにはオンにします |
  | Redirect port (リダイレクト ポート) | HTTP |
  | Client SSL profile (クライアント SSL プロファイル) | 定義済みのプロファイルを `clientssl` SSL 証明書を含むプロファイルに入れ替えします。 既定のプロファイルでテストしてもかまいませんが、ブラウザーでアラートが発生する可能性があります。 |

- **プールのプロパティ**

  BIG-IP では、バックエンド サービスは、仮想サーバーによってインバウンド トラフィックが転送される 1 つ以上のアプリケーション サーバーを含むプールとして表されます。 既存のプールを選択するか、新しく作成します。

  | Properties | 説明 |
  |:-----------|:------------|
  | 負荷分散方法 | ラウンド ロビンのままにします |
  |Pool server (プール サーバー) | バックエンド アプリケーションの内部 IP |
  | Port | バックエンド アプリケーションのサービス ポート |
  
>[!NOTE]
>BIG-IP には、指定したプール サーバー アドレスへの通信経路が存在する必要があります。

- **シングル サインオンの設定**

  BIG-IP では多くの SSO オプションがサポートされていますが、OAuth クライアント モードのガイド付き構成では、Kerberos または HTTP ヘッダーに制限されます。 SSO を有効にし、次の情報を使用して、前に定義したインバウンド属性が APM でアウトバウンド ヘッダーにマップされるようにします。

  | Properties | 説明 |
  |:-----------|:------------|
  | ヘッダー操作 |`Insert`|
  | ヘッダー名 | "名前" |
  | ヘッダー値 | `%{session.oauth.client.last.id_token.name}`|
  | ヘッダー操作 | `Insert`|
  |ヘッダー名| `agentid`|
  |ヘッダー値 | `%{session.oauth.client.last.id_token.extension_AgentGeo}`|
 
  >[!Note]
  > 中かっこ内で定義されている APM セッション変数は、大文字と小文字が区別されます。 そのため、Azure AD B2C の属性名が AgentID として送信されているときに agentid と入力すると、属性マッピング エラーが発生します。 必要な場合を除き、すべての属性を小文字で定義することをお勧めします。 Azure AD B2C の場合は、ユーザー フローでのユーザーに対する追加の属性の要求は、ポータルに表示される属性の名前を使用して行われるので、小文字ではなく通常の文を使用する方が望ましいかもしれません。

  ![ユーザーのシングル サインオンの設定を示すスクリーンショット](./media/partner-f5/single-sign-on.png)

- **カスタマイズのプロパティ**

  これらの設定を使用すると、ユーザーが APM アクセス ポリシー フローを操作するときに表示される画面の言語と外観をカスタマイズできます。 画面のメッセージとプロンプトを個人用に設定したり、画面のレイアウト、色、画像を変更したり、アクセス ポリシー項目で通常カスタマイズ可能なキャプション、説明、メッセージをローカライズしたりすることができます。

  フォーム ヘッダーのテキスト フィールドの "F5 Networks" という文字列を、自分の組織の名前に置き換えます。 たとえば、"Wacketywack Inc. Secure hybrid access" などとします。

- **セッション管理のプロパティ**

  BIG-IP のセッション管理の設定は、ユーザー セッションが続行を中止または許可される条件、ユーザーと IP アドレスの制限、およびエラー ページを定義するために使用されます。 これらは省略可能ですが、シングル ログアウト (SLO) 機能を実装することを強くお勧めします。これにより、不要になったセッションは安全に終了され、発行されているアプリケーションに第三者が誤って不正アクセスするリスクが軽減されます。

## <a name="related-information"></a>関連情報

最後のステップでは、構成の概要が提供されます。 [Deploy]\(デプロイ\) を選択すると、設定がコミットされて、アプリケーションへのセキュリティ保護されたハイブリッド アクセスを可能にするために必要な BIG-IP と APM のすべてのオブジェクトが作成されます。
アプリケーションは、CA のターゲット リソースとしても表示されるようになるはずです。 [Azure AD B2C 用の CA ポリシーを構築するためのガイダンス](conditional-access-identity-protection-overview.md)に関する記事を参照してください。
このパターンを使用する組織では、セキュリティを強化するため、アプリケーションへのすべての直接アクセスをブロックすることで、BIG-IP 経由の厳密なパスを強制することもできます。

## <a name="next-steps"></a>次のステップ

ユーザーとして、ブラウザーを起動し、アプリケーションの外部 URL に接続します。 BIG-IP の OAuth クライアント ログオン ページで、承認コードの付与を使用してログオンするように求められます。 このステップを省く方法については、追加構成のセクションを参照してください。

その後、サインアップにリダイレクトされるので、Azure AD B2C テナントに対して認証を行います。

![ユーザー サインインを示すスクリーンショット](./media/partner-f5/sign-in-message.png)

![サインイン後のウェルカム メッセージを示すスクリーンショット](./media/partner-f5/welcome-page.png)

### <a name="supplemental-configurations"></a>追加の構成

**シングル ログアウト (SLO)**

Azure AD B2C では、さまざまな[メカニズム](session-behavior.md?pivots=b2c-custom-policy#single-sign-out)を通じて IdP とアプリケーションのサインアウトが完全にサポートされています。
アプリケーションのサインアウト関数で Azure AD B2C のログアウト エンドポイントを呼び出すのは、SLO を実現する 1 つの方法です。 それにより、Azure AD B2C で BIG-IP への最後のリダイレクトを発行することができ、ユーザーとアプリケーションの間の APM セッションも確実に終了されます。
もう 1 つの方法は、アプリケーションのサインアウト ボタンが選択されたときの要求を BIG-IP でリッスンし、要求を検出したら、Azure AD B2C のログオフ エンドポイントを同時に呼び出すというものです。 この方法を使用すると、アプリケーション自体を変更する必要なしに、SLO を実現できます。 BIG-IP の iRules を使用してこれを実装する方法の詳細については、[こちらを参照](https://support.f5.com/csp/article/K42052145)してください。
どちらの場合も、Azure AD B2C テナントで APM のログアウト エンドポイントを認識している必要があります。 

1. Azure AD B2C ポータルで **[管理]** > **[マニフェスト]** に移動し、logoutUrl プロパティを見つけます。 null になっているはずです。

2. APM のログアウト後 URI `https://<mysite.com>/my.logout.php3` を追加します。`<mysite.com>` は、自分のヘッダー ベース アプリケーションの BIG-IP の FQDN です。

**最適化されたログイン フロー**

必須ではありませんがユーザーのログイン エクスペリエンスを向上させることができるステップの 1 つは、Azure AD の事前認証の前にユーザーに表示される OAuth ログオン プロンプトを抑制することです。 

1. **[Access]\(アクセス\)** > **[Guided Configuration]\(ガイド付き構成\)** に移動して、ヘッダー ベースのアプリケーションの行の右端にある小さい南京錠のアイコンを選択して、厳密な構成のロックを解除します

   ![最適化されたログイン フローを示スクリーンショット](./media/partner-f5/optimized-login-flow.png)

   厳格な構成のロックを解除すると、ウィザードの UI を使用してさらに変更を行うことができなくなり、アプリケーションの発行されたインスタンスに関連付けられているすべての BIG-IP オブジェクトを、直接管理できるようになります。

2. **[Access]\(アクセス\)** > **[Profiles/ Policies]\(プロファイル/ポリシー\)** > **[Access Profiles (Per-session Policies)]\(アクセス プロファイル (セッション単位のポリシー)\)** に移動し、アプリケーションのポリシー オブジェクトの **[Per-Session Policy]\(セッション単位のポリシー\)** 編集リンクを選択します。

   ![アクセス プロファイルを示すスクリーンショット](./media/partner-f5/access-profile.png)

3. 小さい十字を選択して OAuth のログオン ページ ポリシー オブジェクトを削除し、プロンプトが表示されたら、前のノードへの接続を選択します。

   ![OAuth のログオン ページを示すスクリーンショット](./media/partner-f5/oauth-logon.png)

4. 左上隅にある **[Apply Access Policy]\(アクセス ポリシーの適用\)** を選択して、ビジュアル エディターのタブを閉じます。次にアプリケーションへの接続を試みると、Azure AD B2C のサインイン ページに直接移動します。

>[!Note]
>厳格モードを再度有効にして構成をデプロイすると、Guided Configuration UI の外部で実行されたすべての設定が上書きされるので、運用サービスの場合は、すべての構成オブジェクトを手動で作成することによってこのシナリオを実装することが推奨されます。

### <a name="troubleshooting"></a>トラブルシューティング

保護されたアプリケーションにアクセスできない場合は、構成の誤りなど、いくつもの要因の可能性が考えられます。

- BIG-IP のログは、認証と SSO のすべての問題を探し出すための優れた情報源です。 トラブルシューティングを行う場合は、ログの詳細レベルを上げる必要があります。

  1. **[Access Policy]\(アクセス ポリシー\)** > **[Overview]\(概要\)** > **[Event Logs]\(イベント ログ\)** > **[Settings]\(設定\)** に移動します。

  2. 発行されたアプリケーションの行を選択し、**[Edit]\(編集\)** > **[Access System Logs]\(システム ログへのアクセス\)** を選択します。

  3. SSO の一覧から **[Debug]\(デバッグ\)** を選択して、**[OK]** を選択します。 これで、ログを調べる前に問題を再現できるようになりますが、完了したら忘れずに元に戻しておいてください。

- Azure AD B2C の認証が成功した直後に、BIG-IP ブランドのエラーが表示される場合は、Azure AD から BIG-IP への SSO に関連する問題である可能性があります。

  1. **[Access]\(アクセス\)** > **[Overview]\(概要\)** > **[Access reports]\(アクセス レポート\)** に移動します。

  2. 過去 1 時間のレポートを実行して、ログで手掛かりを確認します。 セッションに対する [View session variables]\(セッション変数の表示\) リンクも、APM が Azure AD から予想される要求を受信しているかどうかを把握するのに役立ちます。

- BIG-IP のエラー ページが表示されない場合は、問題は、バックエンド要求や、BIG-IP からアプリケーションへの SSO に関係している可能性が高くなります。

  1. **[Access Policy]\(アクセス ポリシー\)** > **[Overview]\(概要\)** > **[Active Sessions]\(アクティブ セッション\)** に移動します。

  2. アクティブなセッションのリンクを選択します。

- この場所にある [View Variables]\(変数の表示\) リンクも、根本的な原因を特定するのに役立ちます。BIG-IP の APM で適切なセッション属性を取得できない場合は特にそうです。
その場合、アプリケーションのログは、それらの属性をヘッダーとして受信したかどうかを理解するのに役立ちます。

- Guided Configuration v8 を使用している場合は、Azure AD B2C の認証が成功した後で次のような BIG-IP エラーが生成される既知の問題に注意してください。  

  ![エラー メッセージを示すスクリーンショット](./media/partner-f5/error-message.png)

  これは、Azure AD B2C によって発行されたトークンの署名を BIG-IP で検証できないことによるポリシー違反です。 同じアクセス ログで、問題のさらに詳細な情報を提供できる必要があります。

  ![アクセス ログを示すスクリーンショット](./media/partner-f5/access-log.png)

  正確な根本原因は F5 のエンジニアリングによって調査されていますが、問題は、デプロイの間に AGC によって自動 JWT の設定が有効にされず、そのために APM で現在のトークン署名キーを取得できないことに関連しているようです。

  解決されるまで、この問題を回避する 1 つの方法は、この設定を手動で有効にすることです。 

  1. **[Access]\(アクセス\)** > **[Guided Configuration]\(ガイド付き構成\)** に移動して、ヘッダー ベースのアプリケーションの行の右端にある小さい南京錠のアイコンを選択します。

  2. 管理された構成のロックを解除したら、**[Access]\(アクセス\)** > **[Federation]\(フェデレーション\)** > **[OAuth Client/Resource Server]\(OAuth クライアント/リソース サーバー\)** > **[Providers]\(プロバイダー\)** に移動します。

  3. Azure AD B2C 構成のプロバイダーを選択します。

  4. **[Use Auto JWT]\(自動 JWT を使用する\)** チェック ボックスをオンにしてから、**[Discover]\(検出\)**、**[Save]\(保存\)** の順に選択します。

    OpenID URI メタデータを通じて提供されたトークン署名証明書のキー ID (KID) が、キー (JWT) のフィールドに表示されるようになるはずです。
  
  5. 最後に、左上隅の F5 のロゴの横にある黄色の **[Apply Access Policy]\(アクセス ポリシーを適用する\)** オプションを選択します。 それらの設定を適用し、**[Apply]\(適用\)** を再び選択してアクセス プロファイルの一覧を更新します。

[OAuth クライアントとリソース サーバーのトラブルシューティングのヒント](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-oauth-configuration-14-1-0/apm-oauth-client-and-resource-server.html#GUID-774384BC-CF63-469D-A589-1595D0DDFBA2)の詳細については、F5 のガイダンスを参照してください
