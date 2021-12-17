---
title: Microsoft Dynamics 365 Fraud Protection で Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: リスクのある不正なアカウントを特定するために、Microsoft Dynamics 365 Fraud Protection で Azure Active Directory B2C を構成するためのチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 5/12/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 83715b34578176ba08fb364cfae11df8b1cf1209
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044724"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用した Microsoft Dynamics 365 Fraud Protection の構成

このサンプル チュートリアルでは、[Microsoft Dynamics 365 Fraud Protection](/dynamics365/fraud-protection/overview) (DFP) を Azure Active Directory (AD) B2C と統合する方法について説明します。

Microsoft DFP は、不正なアカウントとログインの作成を試行されるリスクを評価する機能を組織に提供します。Microsoft DFP の評価をお客様が使用して、新しい偽のアカウントを作成したり既存のアカウントを侵害したりしようとする疑わしい試みを調べたり防いだりすることができます。

このサンプルでは、Microsoft DFP デバイスのフィンガープリントとアカウントの作成およびサインイン評価 API エンドポイントを Azure AD B2C のカスタム ポリシーに組み込む方法を紹介します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- [Azure AD B2C テナント](./tutorial-create-tenant.md)。 テナントは Azure サブスクリプションにリンクされています。

- Microsoft DFP [サブスクリプション](https://dynamics.microsoft.com/pricing/#Sales)を取得します。 [評価版クライアント バージョン](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)を設定することもできます。

## <a name="scenario-description"></a>シナリオの説明

Microsoft DFP 統合には次のコンポーネントが含まれます。

- **Azure AD B2C テナント**: ユーザーを認証し、Microsoft DFP のクライアントとして機能します。 ターゲット ポリシーを実行するすべてのユーザーの識別データと診断データを収集する、フィンガープリント スクリプトをホストします。 後で、Microsoft DFP によって返されるルール評価結果に基づいて、サインインまたはサインアップの試行をブロックまたはチャレンジします。

- **カスタム UI テンプレート**: Azure AD B2C によってレンダリングされるページの HTML コンテンツをカスタマイズするために使用します。 これらのページには、Microsoft DFP フィンガープリントに必要な JavaScript スニペットが含まれています

- **Microsoft DFP フィンガープリント サービス**: 動的に埋め込まれたスクリプトです。デバイスのテレメトリとセルフアサート ユーザーの詳細をログに記録して、ユーザーが意思決定プロセスで後で使用するために一意に識別できるフィンガープリントを作成します。

- **Microsoft DFP API エンドポイント**: 決定結果を提供し、クライアント アプリケーションによって実行される操作を反映した、最終的な状態を受け入れます。 Azure AD B2C は、REST API コネクタを使用して Microsoft DFP エンドポイントと直接通信します。 Microsoft DFP がライセンス付与されインストールされている Azure AD テナントへの client_credentials 許可を介して API 認証が発生し、ベアラー トークンが取得されます。

次のアーキテクチャの図に、この実装を示します。

![Microsoft Dynamics 365 Fraud Protection のアーキテクチャ図を示す画像](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーがサインアップを選択して新しいアカウントを作成し、そのページに情報を入力します。 Azure AD B2C でユーザー属性が収集されます。
| 2. | Azure AD B2C で Microsoft DFP API が呼び出され、ユーザー属性が渡されます。
| 3. | Microsoft DFP API でその情報が使用され、処理された後に、Azure AD B2C に結果が返されます。
| 4. | Azure AD B2C が、Microsoft DFP API から情報を受信します。 それが失敗応答である場合、ユーザーにエラー メッセージが表示されます。 成功応答が表示された場合、ユーザーは認証され、ディレクトリに書き込まれます。

## <a name="set-up-the-solution"></a>ソリューションのセットアップ

1. Azure AD B2C へのフェデレーションを可能にするように構成された、[Facebook アプリケーションを作成](./identity-provider-facebook.md#create-a-facebook-application)します。
2. 作成した [Facebook シークレット](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key)を Identity Experience Framework ポリシー キーとして追加します。

## <a name="configure-your-application-under-microsoft-dfp"></a>Microsoft DFP でのアプリケーションの構成

Microsoft DFP を使用するように [Azure AD テナントを設定](/dynamics365/fraud-protection/integrate-real-time-api)します。

## <a name="set-up-your-custom-domain"></a>自分のカスタム ドメインを設定する

運用環境では、[カスタム ドメインを Azure AD B2C](./custom-domain.md?pivots=b2c-custom-policy) と [Microsoft DFP フィンガープリント サービス](/dynamics365/fraud-protection/device-fingerprinting#set-up-dns)に使用する必要があります。 ブラウザーのプライバシー設定がドメイン間の Cookie をブロックしないようにするために、両方のサービスのドメインは同じルート DNS ゾーンに存在しなければなりません。これは、非運用環境では必要ありません。

例を次に示します。

| 環境 | サービス | Domain |
|:------------|:---------------|:---------------|
| 開発 | Azure AD B2C | `contoso-dev.b2clogin.com` |
| 開発 | Microsoft DFP フィンガープリント | `fpt.dfp.microsoft-int.com` |
| UAT | Azure AD B2C | `contoso-uat.b2clogin.com` |
| UAT | Microsoft DFP フィンガープリント | `fpt.dfp.microsoft.com` |
| Production | Azure AD B2C | `login.contoso.com` |
| Production | Microsoft DFP フィンガープリント | `fpt.login.contoso.com` |

## <a name="deploy-the-ui-templates"></a>UI テンプレートをデプロイする

1. 提供された [Azure AD B2C UI テンプレート](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/ui-templates)を、Azure Blob Storage のように公開されたインターネット ホスティング サービスにデプロイします。

2. `https://<YOUR-UI-BASE-URL>/` の値をデプロイ場所のルート URL に置き換えます。

  >[!NOTE]
  >後で Azure AD B2C ポリシーを構成するためにベース URL が必要になります。

3. `ui-templates/js/dfp.js` ファイルで、`<YOUR-DFP-INSTANCE-ID>` を Microsoft DFP インスタンス ID に置き換えます。

4. CORS が Azure AD B2C ドメイン名 `https://{your_tenant_name}.b2clogin.com` または `your custom domain` で有効になっていることを確認します。

詳細については、[UI のカスタマイズに関するドキュメント](./customize-ui-with-html.md?pivots=b2c-custom-policy)を参照してください。

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C の構成

### <a name="add-policy-keys-for-your-microsoft-dfp-client-app-id-and-secret"></a>Microsoft DFP クライアント アプリ ID とシークレットのポリシー キーを追加する

1. Microsoft DFP がセットアップされている Azure AD テナントで、[Azure AD アプリケーションを作成して管理者の同意を付与します](/dynamics365/fraud-protection/integrate-real-time-api#create-azure-active-directory-applications)。
2. このアプリケーション登録のシークレット値を作成し、アプリケーションのクライアント ID とクライアント シークレットの値をメモします。
3. クライアント ID とクライアント シークレットの値を [Azure AD B2C テナント 内のポリシー キーとして](./policy-keys-overview.md)保存 します。

 >[!NOTE]
 >ポリシー キーは後で Azure AD B2C ポリシーを構成するために必要となります。

### <a name="replace-the-configuration-values"></a>構成値を置き換える

提供された[カスタム ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies)内にある次のプレースホルダーを見つけて、ご自分のインスタンスの対応する値に置き換えます。

| プレースホルダー | 置換後の文字列 | ノート |
| :-------- | :------------| :-----------|
|{Settings:Production} | ポリシーを実稼働モードでデプロイするかどうか | `true` または `false`  |
|{Settings:Tenant} | テナントの短い名前 |  `your-tenant` - your-tenant.onmicrosoft.com より  |
| {Settings:DeploymentMode}    |  使用する Application Insights デプロイ モード   |  `Production` または `Development`  |
|  {Settings:DeveloperMode}    | ポリシーを Application Insights 開発者モードでデプロイするかどうか      |   `true` または `false`    |
|  {Settings:AppInsightsInstrumentationKey}  |  Application Insights インスタンスのインストルメンテーション キー*   |  `01234567-89ab-cdef-0123-456789abcdef` |
|  {Settings:IdentityExperienceFrameworkAppId}  | Azure AD B2C テナントに構成されている IdentityExperienceFramework アプリのアプリ ID  | `01234567-89ab-cdef-0123-456789abcdef`|
|  {Settings:ProxyIdentityExperienceFrameworkAppId}  |   Azure AD B2C テナントに構成されている ProxyIdentityExperienceFramework アプリのアプリ ID |   `01234567-89ab-cdef-0123-456789abcdef`|
| {Settings:FacebookClientId}  | B2C とのフェデレーション用に構成した Facebook アプリのアプリ ID    | `000000000000000`   |
|   {Settings:FacebookClientSecretKeyContainer}  | Facebook のアプリ シークレットを保存したポリシー キーの名前    |  `B2C_1A_FacebookAppSecret` |
|   {Settings:ContentDefinitionBaseUri} |  UI ファイルをデプロイしたエンドポイント   | `https://<my-storage-account>.blob.core.windows.net/<my-storage-container>`   |
|  {Settings:DfpApiBaseUrl}   |  DFP API インスタンスのベース パス - DFP ポータルにあります   | `https://tenantname-01234567-89ab-cdef-0123-456789abcdef.api.dfp.dynamics.com/v1.0/`   |
| {Settings:DfpApiAuthScope} | DFP API サービスの client_credentials スコープ | `https://api.dfp.dynamics-int.com/.default or https://api.dfp.dynamics.com/.default` |
| {Settings:DfpTenantId} | DFP のライセンス付与とインストールがされている Azure AD テナント (B2C でない) の ID | `01234567-89ab-cdef-0123-456789abcdef` または `consoto.onmicrosoft.com` |
| {Settings:DfpAppClientIdKeyContainer} | DFP クライアント ID を保存するポリシー キーの名前 | `B2C_1A_DFPClientId` |
| {Settings:DfpAppClientSecretKeyContainer} | DFP クライアント シークレットを保存するポリシー キーの名前 | `B2C_1A_DFPClientSecret` |

*Application Insights は任意の Azure AD テナントまたはサブスクリプションでセットアップできます。 この値は省略可能ですが、[デバッグに役立つためお勧めします](./troubleshoot-with-application-insights.md)。

>[!NOTE]
>属性コレクション ページに同意通知を追加します。 ユーザーのテレメトリ情報とユーザー ID 情報がアカウント保護のために記録されることを通知します。

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C ポリシーを構成する

1. ポリシー フォルダーの [Azure AD B2C ポリシー](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies)に移動します。

2. この[ドキュメント](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack)に従って、[LocalAccounts スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)をダウンロードします。

3. Azure AD B2C テナントのポリシーを構成します。

>[!NOTE]
>お使いの特定のテナントに関連付けるため、提供されているポリシーを更新してください。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[Identity Experience Framework]** を選択します。

2. 以前に作成した **SignUpSignIn** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

   b. **[応答 URL]** : **リダイレクト URL** を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. Microsoft DFP サービスは、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

>[!NOTE]
>[Microsoft DFP ルール エンジン](/dynamics365/fraud-protection/rules)を使用する場合は、Microsoft DFP Portal でルールを直接更新します。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Microsoft DFP のサンプル](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
