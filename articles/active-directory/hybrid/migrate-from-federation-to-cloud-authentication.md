---
title: Azure Active Directory でフェデレーションからクラウド認証に移行する
description: この記事には、フェデレーションからクラウド認証へのハイブリッド ID 環境の移行に関する情報が含まれています
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: baselden
author: BarbaraSelden
manager: MartinCo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd25928e245e6561be53ace29b017f476da7f1f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178049"
---
# <a name="migrate-from-federation-to-cloud-authentication"></a>フェデレーションからクラウド認証に移行する 

この記事では、Azure Active Directory [パスワード ハッシュ同期 (PHS)](whatis-phs.md) または[パススルー認証 (PTA)](how-to-connect-pta.md) のいずれかを使用してクラウド ユーザー認証をデプロイする方法について学習します。 [Active Directory フェデレーション サービス (AD FS)](whatis-fed.md) からクラウド認証方法に移行する場合のユース ケースを示しますが、ガイダンスの大部分は他のオンプレミス システムにも適用されます。

続行する前に、[適切な認証方法の選択](choose-ad-authn.md)に関するガイドを確認し、組織に最適な方法を比較してください。

クラウド認証には PHS を使用することをお勧めします。

## <a name="staged-rollout"></a>段階的なロールアウト

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]

段階的なロールアウトは、ドメインを切り替える前に、Azure AD Multi-Factor Authentication (MFA)、条件付きアクセス、漏洩した資格情報の ID 保護、ID 管理などのクラウド認証機能を使用して、一連のユーザーを選択的にテストすることができる優れた方法です。 

[サポートされるシナリオ](how-to-connect-staged-rollout.md#supported-scenarios)と[サポートされていないシナリオ](how-to-connect-staged-rollout.md#unsupported-scenarios)については、段階的なロールアウトの実装計画を参照してください。 ドメインを切り替える前に、段階的なロールアウトを使用してテストすることをお勧めします。

段階的なロールアウトを構成する方法については、Azure AD で段階的なロールアウトを使用したクラウド認証への移行に関する[段階的なロールアウトの対話型ガイド](https://mslearn.cloudguides.com/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD)を参照してください。

## <a name="migration-process-flow"></a>移行プロセス フロー

![クラウド認証に移行するためのプロセス フロー](media/deploy-cloud-user-authentication/process-flow-migration.png)

## <a name="prerequisites"></a>前提条件

移行を開始する前に、これらの前提条件を満たしていることを確認してください。

### <a name="required-roles"></a>必要なロール

段階的なロールアウトを使用するには、テナントのグローバル管理者である必要があります。 

特定の Windows Active Directory フォレストでシームレス SSO を有効にするには、ドメイン管理者である必要があります。

### <a name="step-up-azure-ad-connect-server"></a>Azure AD Connect サーバーのステップアップ

[Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) をインストールするか、[最新バージョンにアップグレードします](how-to-upgrade-previous-version.md)。 Azure AD Connect サーバーをステップアップすると、AD FS からクラウド認証方法への移行にかかる時間を数時間から数分に短縮できる可能性があります。 

### <a name="document-current-federation-settings"></a>現在のフェデレーション設定をドキュメント化する

現在のフェデレーション設定を確認するには、[Get-MsolDomainFederationSettings](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login) コマンドレットを実行します。 

ご利用のフェデレーションの設計とデプロイのドキュメント用にカスタマイズされた可能性のある、すべての設定を確認します。 具体的には、**PreferredAuthenticationProtocol**、**SupportsMfa**、および **PromptLoginBehavior** のカスタマイズを確認します。

### <a name="back-up-federation-settings"></a>フェデレーション設定をバックアップする

このデプロイでは、AD FS ファーム内の他の証明書利用者は変更されませんが、設定をバックアップできます。

 - Microsoft [AD FS Rapid Restore Tool](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) を使用して、既存のファームを復元するか、新しいファームを作成します。

- 次の PowerShell の例を使用して、Microsoft 365 ID プラットフォームの証明書利用者信頼と、追加したすべての関連カスタム要求規則をエクスポートします。

    ```powershell

    (Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"

    ```

## <a name="plan-the-project"></a>プロジェクトを計画する

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)し、プロジェクトにおけるその利害関係者の役割がよく理解されていることを確認します。

### <a name="plan-communications"></a>連絡を計画する

クラウド認証に移行した後、Azure AD を通じて認証される Microsoft 365 およびその他のリソースにアクセスするためのユーザーのサインイン エクスペリエンスが変更されます。 ネットワークの外部のユーザーには、Azure AD サインイン ページのみが表示されます。 

ユーザー エクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡します。

### <a name="plan-the-maintenance-window"></a>メンテナンス期間の計画

ドメイン変換の後、Azure AD では最大 4 時間、何らかのレガシ認証要求を Exchange Online から AD FS サーバーに送信し続ける可能性があります。 遅延は、[レガシ アプリケーション認証](../fundamentals/concept-fundamentals-block-legacy-authentication.md)用の Exchange Online キャッシュがフェデレーションからクラウド認証へのカットオーバーを認識するために最大 4 時間かかる可能性があるためです。

この 4 時間の期間中、レガシ認証を使用するアプリケーションに再認証するときに、ユーザーに資格情報の入力を繰り返し求める場合があります。 ユーザーはまだ AD FS に対して正常に認証できますが、フェデレーションの信頼は削除されるため、Azure AD ではユーザーの発行したトークンが受け入れられなくなります。

既存のレガシ クライアント (Exchange ActiveSync、Outlook 2010/2013) に影響することはありません。Exchange Online では、一定の期間、それらの資格情報のキャッシュが保持されるためです。 キャッシュは、ユーザーを自動的に再認証するために使用されます。 ユーザーが AD FS に戻る必要はありません。 これらのクライアントのためにデバイスに格納されている資格情報は、キャッシュがクリアされた後、自身を自動的に再認証するために使用されます。 ドメイン変換プロセスの結果として、パスワードの入力を求めるメッセージがユーザーに表示されることはありません。

先進認証クライアント (Office 2016 と Office 2013、iOS、および Android アプリ) では、リソースへのアクセスを継続するための新しいアクセス トークンを取得するために、AD FS に戻るのではなく、有効な更新トークンが使用されます。 これらのクライアントに、ドメイン変換プロセスの結果としてパスワードの入力を求めるメッセージを表示する必要はありません。 クライアントは、追加の構成を行わなくても機能し続けます。

### <a name="plan-for-rollback"></a>ロールバックのための計画

> [!TIP]
> ロールバックする必要がある場合は、営業時間外にドメインのカットオーバーを計画してください。 

ロールバックを計画する場合は、[ドキュメント化された現在のフェデレーション設定](#document-current-federation-settings)を使用して、[フェデレーションの設計とデプロイに関するドキュメント](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)を確認してください。 

ロールバック プロセスでは、[Convert-MSOLDomainToFederated](/powershell/module/msonline/convert-msoldomaintofederated) コマンドレットを使用して、マネージド ドメインをフェデレーション ドメインに変換する必要があります。 必要に応じて、追加の要求規則を構成します。

## <a name="migration-considerations"></a>移行に関する注意事項 

移行に関する主な注意事項を次に示します。

### <a name="plan-for-customizations-settings"></a>カスタマイズ設定を計画する 

Azure AD では onload.js ファイルを複製できません。 AD FS インスタンスが大幅にカスタマイズされ、onload.js ファイル内の特定のカスタマイズ設定に依存している場合は、Azure AD が現在のカスタマイズ要件を満たしていることを確認し、適切に計画します。 これらの今後の変更をユーザーに伝えます。

#### <a name="sign-in-experience"></a>サインイン エクスペリエンス

Azure AD サインイン エクスペリエンスをカスタマイズすることはできません。 Azure AD にサインインするには、ユーザーが以前にサインインした方法に関係なく、ユーザー プリンシパル名 (UPN) や電子メールなどの完全修飾ドメイン名が必要です。 

#### <a name="organization-branding"></a>組織のブランド化

[Azure AD サインイン ページをカスタマイズ](../fundamentals/customize-branding.md)できます。 サインイン ページでの AD FS の一部の視覚的変更は、変換後に行う必要があります。 

>[!NOTE] 
>無料の Azure AD ライセンスでは、Microsoft 365 ライセンスを持っている場合を除いて、組織のブランド化を使用できません。

### <a name="plan-for-conditional-access-policies"></a>条件付きアクセス ポリシーを計画する

現在認証に条件付きアクセスを使用しているかどうか、または AD FS でアクセス制御ポリシーを使用しているかどうかを評価します。 

AD FS アクセス制御ポリシーを同等の Azure AD [条件付きアクセス ポリシー](../conditional-access/overview.md)と [Exchange Online のクライアント アクセス規則](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules)に置き換えることを検討します。 条件付きアクセスには Azure AD またはオンプレミスのグループを使用できます。

**レガシ認証を無効にする** - レガシ認証プロトコルに関連してリスクが増加するため、[レガシ認証をブロックする条件付きアクセス ポリシー](../conditional-access/howto-conditional-access-policy-block-legacy.md)を作成します。

### <a name="plan-support-for-mfa"></a>MFA のサポートを計画する

Azure AD 内の各フェデレーション ドメインには、SupportsMFA フラグがあります。 

**SupportsMFA フラグが True に設定されている場合**、Azure AD によってユーザーがリダイレクトされ、AD FS または他のフェデレーション プロバイダーで MFA を実行します。 たとえば、MFA を必要とする条件付きアクセス ポリシーが構成されているアプリケーションにユーザーがアクセスしている場合、そのユーザーは AD FS にリダイレクトされます。 AD FS で認証方法として Azure AD MFA を追加すると、構成が完了した後で Azure AD MFA を呼び出すことができます。

**SupportsMFA フラグが False に設定されている場合**、Azure MFA を使用していない可能性があります。おそらく、AD FS 証明書利用者に対して要求規則を使用して MFA をトリガーしています。 

次の Windows PowerShell コマンドレットを使用して、**SupportsMFA** フラグの状態を確認できます。
```powershell
 Get-MsolDomainFederationSettings –DomainName yourdomain.com
 ```

>[!NOTE] 
>Microsoft MFA Server はサポート終了間近です。使用している場合は、Azure AD MFA に移行する必要があります。 詳細については、 **[Microsoft MFA Server から Azure Multi-Factor Authentication への移行に関するドキュメント](../authentication/how-to-migrate-mfa-server-to-azure-mfa.md)** を参照してください。
Azure AD MFA の使用を計画している場合は、 **[セルフサービス パスワード リセット (SSPR) と Multi-Factor Authentication の統合された登録](../authentication/concept-registration-mfa-sspr-combined.md)** を使用して、ユーザーが認証方法を 1 回で登録できるようにすることをお勧めします。 

## <a name="plan-for-implementation"></a>実装を計画する

このセクションには、サインイン方法を切り替えてドメインを変換する前の事前作業が含まれています。

### <a name="create-necessary-groups-for-staged-rollout"></a>段階的なロールアウトに必要なグループを作成する

*段階的なロールアウトを使用していない場合は、この手順をスキップします。*

段階的なロールアウト用のグループを作成します。 また、条件付きアクセス ポリシーを追加する場合は、条件付きアクセス ポリシー用のグループも作成する必要があります。

クラウド専用グループとも呼ばれる、Azure AD で管理されているグループを使用することをお勧めします。 ユーザーを MFA に移動する場合と条件付きアクセス ポリシーの両方に、Azure AD セキュリティ グループまたは Microsoft 365 グループを使用できます。 詳細については、[Azure AD セキュリティ グループの作成](../fundamentals/active-directory-groups-create-azure-portal.md)と[管理者向け Microsoft 365 グループの概要](/microsoft-365/admin/create-groups/office-365-groups)に関するページを参照してください。

グループ内のメンバーは、段階的なロールアウトに対して自動的に有効になります。 段階的なロールアウトでは、入れ子になったグループと動的グループはサポートされていません。

### <a name="pre-work-for-sso"></a>SSO の事前作業

使用する SSO のバージョンは、デバイスの OS と参加状態によって異なります。

- **Windows 10、Windows Server 2016、およびそれ以降のバージョンの場合**、[Azure AD 参加済みデバイス](../devices/concept-azure-ad-join.md)、[ハイブリッド Azure AD 参加済みデバイス](../devices/concept-azure-ad-join-hybrid.md)、[Azure AD 登録済みデバイス](../devices/concept-azure-ad-register.md)で、[プライマリ更新トークン (PRT)](../devices/concept-primary-refresh-token.md) 経由の SSO を使用することをお勧めします。 

- **Windows 7 および 8.1 デバイスの場合**、ドメインに参加している[シームレス SSO](how-to-connect-sso.md) を使用して、コンピューターを Azure AD に登録することをお勧めします。 Windows 10 デバイスの場合のように、これらのアカウントを同期させる必要はありません。 ただし、[PowerShell を使用してシームレス SSO の事前作業](how-to-connect-staged-rollout.md#pre-work-for-seamless-sso)を完了する必要があります。

### <a name="pre-work-for-phs-and-pta"></a>PHS と PTA の事前作業

サインイン方法の選択に応じて、[PHS](how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) または [PTA の事前作業](how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication)を完了します。

## <a name="implement-your-solution"></a>ソリューションを実装する

最後に、サインイン方法を計画どおりに PHS または PTA に切り替え、ドメインをフェデレーションからクラウド認証に変換します。 

### <a name="using-staged-rollout"></a>段階的なロールアウトを使用する場合

段階的なロールアウトを使用している場合は、次のリンクの手順に従ってください。

1. [テナントの特定の機能の段階的なロールアウトを有効にします。](how-to-connect-staged-rollout.md#enable-staged-rollout)

2. テストが完了したら、[ドメインをフェデレーションからマネージドに変換します](#convert-domains-from-federated-to-managed)。

### <a name="without-using-staged-rollout"></a>段階的なロールアウトを使用しない場合 

この変更を有効にするには、2 つのオプションがあります。

- **オプション A:** Azure AD Connect を使用して切り替える
  
  *最初に Azure AD Connect を使用して AD FS/ping フェデレーション環境を構成した場合に使用できます*。

- **オプション B:** Azure AD Connect と PowerShell を使用して切り替える
 
  *最初に Azure AD Connect を使用してフェデレーション ドメインを構成しなかった場合、またはサードパーティのフェデレーション サービスを使用している場合に使用できます*。

これらのオプションのいずれかを選択するには、現在の設定を把握している必要があります。

#### <a name="verify-current-azure-ad-connect-settings"></a>現在の Azure AD Connect 設定を確認する

次の図に示すように、[Azure AD ポータル](https://aad.portal.azure.com/)にサインインし、 **[Azure AD Connect]** を選択して、**USER SIGN_IN** の設定を確認します。

![現在の Azure AD Connect 設定を確認する](media/deploy-cloud-user-authentication/current-user-settings-on-azure-ad-portal.png)


**フェデレーションが構成された方法を確認するには:**

1. Azure AD Connect サーバーで、**Azure AD Connect** を開き、 **[構成]** を選択します。

2. **[追加のタスク] > [フェデレーションの管理]** で、 **[フェデレーション構成の表示]** を選択します。 

    ![[フェデレーションの管理] を表示する](media/deploy-cloud-user-authentication/manage-federation.png)

    このセクションに AD FS 構成が表示される場合は、AD FS が最初に Azure AD Connect を使用して構成されたと見なすことができます。 例として、次の図を参照してください。

    ![AD FS 構成を表示する](media/deploy-cloud-user-authentication/federation-configuration.png)

    AD FS が現在の設定の一覧に表示されていない場合は、PowerShell を使用して、ドメインをフェデレーション ID からマネージド ID に手動で変換する必要があります。

#### <a name="option-a"></a>オプション A

**Azure AD Connect を使用してフェデレーションから新しいサインイン方法に切り替える**

1. Azure AD Connect サーバーで、**Azure AD Connect** を開き、 **[構成]** を選択します。

2. **[追加のタスク]** ページで、 **[ユーザー サインインの変更]** を選択し、 **[次へ]** を選択します。
   
    ![[追加のタスク] を表示する](media/deploy-cloud-user-authentication/additional-tasks.png)

3. **[Azure AD に接続]** ページで、全体管理者アカウントの資格情報を入力します。

4. **[ユーザー サインイン]** ページで次の操作を行います。

    - **[パススルー認証]** オプション ボタンを選択した場合は、 **[シングル サインオンを有効にする]** をオンにしてから、 **[次へ]** を選択します。

    -  **[パスワード ハッシュの同期]** オプション ボタンを選択した場合は、 **[ユーザー アカウントを変換しない]** チェック ボックスがオンになっていることを確認します。 このオプションは非推奨になりました。 **[シングル サインオンを有効にする]** をオンにしてから、 **[次へ]** を選択します。

      ![[ユーザー サインイン] ページで [シングル サインオンを有効にする] をオンにする](media/deploy-cloud-user-authentication/user-sign-in.png)

5. **[シングル サインオンを有効にする]** ページで、ドメイン管理者アカウントの資格情報を入力し、 **[次へ]** を選択します。

    ![[シングル サインオンを有効にする] ページ](media/deploy-cloud-user-authentication/enable-single-sign-on.png)

    シームレス SSO を有効にするには、ドメイン管理者アカウントの資格情報が必要です。 このプロセスでは、管理者特権のアクセス許可を必要とする、以下のアクションが実行されます。
      - オンプレミスの Active Directory インスタンスに、(Azure AD を表す) AZUREADSSO という名前のコンピューター アカウントが作成されます。
      - コンピューター アカウントの Kerberos 復号化キーが、Azure AD と安全に共有されます。
      - Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) が作成されます。

    ドメイン管理者の資格情報は Azure AD Connect または Azure AD に格納されず、プロセスが正常に終了したときに破棄されます。 これらは、この機能を有効にするために使用されます。

6. **[構成の準備完了]** ページで、 **[構成が完了したら、同期プロセスを開始してください]** チェック ボックスがオンになっていることを確認します。 次に、 **[構成]** を選択します。

    ![[構成の準備完了] ページ](media/deploy-cloud-user-authentication/ready-to-configure.png)

 > [!IMPORTANT] 
 > この時点で、すべてのフェデレーション ドメインがマネージド認証に変更されます。 選択したユーザーのサインイン方法は、新しい認証方法です。

1. Azure AD ポータルで、 **[Azure Active Directory]** を選択してから、 **[Azure AD Connect]** を選びます。

2. 以下の設定を確認します。

      - **[フェデレーション]** が **[無効]** に設定されている。
      - **[シームレス シングル サインオン]** が **[有効]** に設定されている。
      - **[パスワード ハッシュの同期]** が **[有効]** に設定されている。

  ![ 現在のユーザー設定を再確認する](media/deploy-cloud-user-authentication/reverify-settings.png)

3. PTA に切り替える場合は、次の手順に従います。

##### <a name="deploy-more-authentication-agents-for-pta"></a>PTA 用に追加の認証エージェントをデプロイする

>[!NOTE]
> PTA では、Azure AD Connect サーバー、および Windows サーバーを実行しているオンプレミス コンピューターで軽量のエージェントをデプロイする必要があります。 待ち時間を短縮するには、Active Directory ドメイン コントローラーのできるだけ近くにエージェントをインストールします。

ほとんどのお客様の場合、高可用性と必要な容量を提供するのに、2 つまたは 3 つの認証エージェントがあれば十分です。 テナントには、最大 12 個のエージェントを登録できます。 最初のエージェントは、常に Azure AD Connect サーバー自体にインストールされます。 エージェントの制限事項とエージェントのデプロイ オプションの詳細については、「[Azure Active Directory パススルー認証:現在の制限事項](how-to-connect-pta-current-limitations.md)」を参照してください。

1. **[パススルー認証]** を選択します。
2. **[パススルー認証]** ページで、 **[ダウンロード]** ボタンを選択します。
3. **[エージェントのダウンロード]** ページで、 **[Accept terms and download]\(利用規約に同意してダウンロード\)** を選択します。

    追加の認証エージェントのダウンロードが開始されます。 セカンダリ認証エージェントは、ドメイン参加済みサーバーにインストールします。

4. 認証エージェントのインストールを実行します。 インストール中に、グローバル管理者アカウントの資格情報を入力する必要があります。

    ![ Microsoft Azure AD Connect 認証エージェント](media/deploy-cloud-user-authentication/install-azure-ad-connect-installation-agent.png)

5. 認証エージェントがインストールされたら、PTA の正常性ページに戻って、追加のエージェントの状態を確認できます。

#### <a name="option-b"></a>オプション B

**Azure AD Connect と PowerShell を使用してフェデレーションから新しいサインイン方法に切り替える**

*最初に Azure AD Connect を使用してフェデレーション ドメインを構成しなかった場合、またはサードパーティのフェデレーション サービスを使用している場合に使用できます。*

Azure AD Connect サーバーで、[オプション A](#option-a) の手順 1 ～ 5 に従います。[ユーザー サインイン] ページで、 **[構成しない]** オプションが事前に選択されていることがわかります。

![ [ユーザー サインイン] ページの [構成しない] オプションを参照する](media/deploy-cloud-user-authentication/do-not-configure-on-user-sign-in-page.png)

1. Azure AD ポータルで、 **[Azure Active Directory]** を選択してから、 **[Azure AD Connect]** を選びます。 

2. 以下の設定を確認します。

  - **[フェデレーション]** が **[有効]** に設定されている。
  - **[シームレス シングル サインオン]** が **[無効]** に設定されている。
  - **[パスワード ハッシュの同期]** が **[有効]** に設定されている。

    ![ Azure portal で現在のユーザー設定を確認する](media/deploy-cloud-user-authentication/verify-current-user-settings-on-azure-ad-portal.png)

**PTA のみの場合** は、次の手順に従って、追加の PTA エージェント サーバーをインストールします。

1. Azure AD ポータルで、 **[Azure Active Directory]** を選択してから、 **[Azure AD Connect]** を選びます。

2. **[パススルー認証]** を選択します。 状態が **[アクティブ]** であることを確認します。

    ![ パススルー認証の設定](media/deploy-cloud-user-authentication/pass-through-authentication-settings.png)

   認証エージェントがアクティブでない場合は、次の手順でドメインの変換プロセスを続行する前に、これらの[トラブルシューティング手順](tshoot-connect-pass-through-authentication.md)を完了します。 PTA エージェントが正常にインストールされたことと、Azure portal でそれらの状態が **[アクティブ]** になっていることを確認する前にドメインを変換すると、認証が停止する危険性があります。

3. [追加の認証エージェントをデプロイします](#deploy-more-authentication-agents-for-pta)。

### <a name="convert-domains-from-federated-to-managed"></a>ドメインをフェデレーションからマネージドに変換する

**この時点では、フェデレーション認証はまだアクティブであり、ドメインのために機能しています**。 デプロイを続行するには、各ドメインをフェデレーション ID からマネージド ID に変換する必要があります。

>[!IMPORTANT]
> すべてのドメインを同時に変換する必要はありません。 運用環境テナントのテスト ドメインや、ユーザー数が最も少ないドメインから開始することができます。

**Azure AD PowerShell モジュールを使用して、変換を完了します。**

1. PowerShell で、全体管理者アカウントを使用して Azure AD にサインインします。

2. 最初のドメインを変換するには、次のコマンドを実行します。
   ```powershell
    Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
    ```
    「[Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)」を参照してください

3. Azure AD ポータルで、 **[Azure Active Directory] > [Azure AD Connect]** の順に選択します。

4. 次のコマンドを実行して、ドメインがマネージドに変換されたことを確認します。
    ```powershell
    Get-MsolDomain -DomainName <domain name>
    ```
## <a name="complete-your-migration"></a>移行を完了する

サインアップ方法を確認し、変換プロセスを完了するには、次のタスクを実行します。

### <a name="test-the-new-sign-in-method"></a>新しいサインイン方法をテストする

テナントでフェデレーション ID が使用されていたときに、ユーザーは Azure AD サインイン ページから AD FS 環境にリダイレクトされていました。 フェデレーション認証ではなく、新しいサインイン方法を使用するようにテナントが構成されたので、ユーザーは AD FS にリダイレクトされません。 

**代わりに、ユーザーは Azure AD サインイン ページから直接サインインします。**

次のリンクの手順に従います - [PHS/PTA とシームレス SSO を使用してサインインを検証します](how-to-connect-staged-rollout.md#validation) (必要な場合)

### <a name="remove-a-user-from-staged-rollout"></a>段階的なロールアウトからユーザーを削除する

段階的なロールアウトを使用した場合は、カットオーバーが完了したら、段階的ロールアウト機能を無効にすることを忘れないでください。 

**段階的ロールアウト機能を無効にするには、コントロールを [無効] に戻します。**

### <a name="sync-userprincipalname-updates"></a>UserPrincipalName の更新を同期する

これまで、次の条件が両方とも当てはまらない限り、オンプレミス環境から同期サービスを使用する、**UserPrincipalName** 属性の更新はブロックされていました。

   - ユーザーがマネージド (非フェデレーション) ID ドメインに存在する。
   - ユーザーにライセンスが割り当てられていない。

この機能を確認または有効にする方法については、「[userPrincipalName の更新を同期する](how-to-connect-syncservice-features.md)」を参照してください。

## <a name="manage-your-implementation"></a>実装を管理する

### <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>シームレス SSO の Kerberos 復号化キーのロールオーバー

Active Directory ドメイン メンバーがパスワードの変更を送信する方法に合わせて、少なくとも 30 日ごとに Kerberos 復号化キーをロールオーバーすることをお勧めします。 関連するデバイスが AZUREADSSO コンピューター アカウント オブジェクトにアタッチされていないため、ロールオーバーは手動で実行する必要があります。

FAQ の「[AZUREADSSO コンピューター アカウントの Kerberos 復号化キーをロールオーバーするにはどうすればよいですか](how-to-connect-sso.md)」を参照してください。

### <a name="monitoring-and-logging"></a>監視およびログ記録

ソリューションの可用性を維持するには、認証エージェントを実行するサーバーを監視します。 認証エージェントでは、一般的なサーバー パフォーマンス カウンターに加えて、認証の統計情報とエラーを把握するのに役立つパフォーマンス オブジェクトが公開されます。

認証エージェントによって、操作のログが、アプリケーションとサービス ログにある Windows イベント ログに記録されます。 トラブルシューティングのためのログを有効にすることもできます。

段階的なロールアウトで実行されるさまざまなアクションを確認するために、[PHS、PTA、またはシームレス SSO のイベントを監査できます](how-to-connect-staged-rollout.md#auditing)。

### <a name="troubleshoot"></a>トラブルシューティング

お客様のサポート チームは、フェデレーションからマネージドへの変更中または変更後に発生する認証の問題のトラブルシューティング方法を理解する必要があります。 以下のトラブルシューティングのドキュメントは、お客様のサポート チームが一般的なトラブルシューティングの手順と、問題の特定および解決に役立つ適切な対処を理解するために役立ちます。

-  [Azure AD PHS](tshoot-connect-password-hash-synchronization.md)
- [Azure AD PTA](tshoot-connect-pass-through-authentication.md)
- [Azure AD シームレス SSO](tshoot-connect-sso.md)

## <a name="decommission-ad-fs-infrastructure"></a>AD FS インフラストラクチャの使用を停止する

### <a name="migrate-app-authentication-from-ad-fs-to-azure-ad"></a>アプリ認証を AD FS から Azure AD に移行する

移行では、まずアプリケーションがオンプレミスでどのように構成されているかを評価し、その構成を Azure AD にマッピングすることが必要です。

SAML/WS-FED または Oauth プロトコルを使用してオンプレミス アプリケーションと SaaS アプリケーションで AD FS を引き続き使用する予定の場合は、ユーザー認証のためにドメインを変換した後、AD FS と Azure AD の両方を使用します。 この場合、[Azure AD アプリケーション プロキシ](../app-proxy/what-is-application-proxy.md)またはいずれかの [Azure AD パートナー統合](../manage-apps/secure-hybrid-access.md)を使用したセキュリティで保護されたハイブリッド アクセス (SHA) により、オンプレミスのアプリケーションとリソースを保護できます。 アプリケーション プロキシまたはいずれかのパートナーを使用すると、お使いのオンプレミスのアプリケーションにセキュリティで保護されたリモート アクセスを提供できます。 ユーザーは [シングル サインオン](../manage-apps/add-application-portal-setup-sso.md)の後、簡単に任意のデバイスからアプリケーションに接続できるようになります。

現在 ADFS とフェデレーションされている SaaS アプリケーションを、Azure AD に移動できます。 [Azure アプリ ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)の組み込みコネクタを使用するか、[Azure AD にアプリケーションを登録する](../develop/quickstart-register-app.md)ことによって、Azure AD で認証を行うように再構成します。

詳細については、次のページを参照してください。 

- [アプリケーション認証を Active Directory フェデレーション サービス (AD FS) から Azure Active Directory に移動する](../manage-apps/migrate-adfs-apps-to-azure.md)
- [開発者向けの AD FS から Azure AD へのアプリケーション移行プレイブック](/samples/azure-samples/ms-identity-adfs-to-aad/ms-identity-dotnet-adfs-to-aad)

### <a name="remove-relying-party-trust"></a>証明書利用者信頼を削除する

Azure AD Connect Health がある場合は、Azure portal から[使用状況を監視](how-to-connect-health-adfs.md)できます。 使用状況に新しい認証要求が表示されていない場合は、すべてのユーザーとクライアントが Azure AD を通じて正常に認証されていることを確認したら、Microsoft 365 の証明書利用者信頼を削除しても問題ありません。

AD FS を他の目的で (つまり、他の証明書利用者信頼で) 使用していない場合は、この時点で AD FS の使用を停止できます。

## <a name="next-steps"></a>次のステップ

- [アプリケーションの移行について学習する](../manage-apps/migration-resources.md)
- [その他の ID 機能をデプロイする](../fundamentals/active-directory-deployment-plans.md)