---
title: Azure AD Connect:クラウド認証 - 段階的なロールアウト | Microsoft Docs
description: 段階的なロールアウトを使用してフェデレーション認証からクラウド認証に移行する方法について説明します。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2596091324acde5c4fdc3f7c467849f90266fec9
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847224"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>クラウド認証: 段階的なロールアウト (パブリック プレビュー)

この機能を使用すると、段階的な方法を使用してフェデレーション認証からクラウド認証に移行できます。

フェデレーション認証から移行すると、いくつかの影響があります。 たとえば、次のいずれかの場合です。
    
-  オンプレミスの MFA サーバーがある 
-  認証にスマート カードを使用している 
-  フェデレーションのみの他の機能を使用している

これらの機能は、クラウド認証に切り替える前に考慮する必要があります。  この機能を試す前に、適切な認証方法の選択に関するガイドを確認することをお勧めします。 詳細については、[こちらの表](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)をご覧ください。

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>前提条件

-   フェデレーション ドメインを持つ Azure AD テナントがあること。

-   パスワード ハッシュ同期 + シームレス SSO **(オプション A)** 、またはパススルー認証 + シームレス SSO **(オプション B)** のどちらに移行するかを決定してあること。 シームレス SSO はオプションですが、シームレス SSO を有効にすることをお勧めします。これにより、企業ネットワーク内からドメインに参加しているマシンを使用するユーザーに対して、サイレント サインイン エクスペリエンスを実現することができます。

-   クラウド認証に移行するユーザーに必要な、適切なテナント ブランドと条件付きアクセス ポリシーをすべて構成してあること。

-   Azure Multi-Factor Authentication の使用を計画している場合は、[パスワード リセットのセルフサービス (SSPR) と Azure MFA の集中型登録](../authentication/concept-registration-mfa-sspr-combined.md)を使用して、ユーザーが認証方法を 1 回で登録できるようにすることをお勧めします。

-   この機能を使用するには、テナントのグローバル管理者である必要があります。

-   特定の AD フォレストでシームレス SSO を有効にするには、ドメイン管理者である必要があります。

## <a name="supported-scenarios"></a>サポートされるシナリオ

以下のシナリオは、段階的なロールアウトでサポートされています。

- この機能は、Azure AD Connect を使用して Azure AD にプロビジョニングされたユーザーに対してのみ機能し、クラウド専用ユーザーには適用されません。

- この機能は、ブラウザーおよび最新の認証クライアント上でのユーザー サインイン トラフィックに対してのみ機能します。 レガシ認証を使用するアプリケーションまたはクラウド サービスは、フェデレーション認証フローにフォールバックします。 (CPU 使用率が先進認証が無効になっている Exchange Online、または先進認証をサポートしていない Outlook 2010。)

## <a name="unsupported-scenarios"></a>サポートされないシナリオ

以下のシナリオは、段階的なロールアウトでサポートされていません。

- 特定のアプリケーションは、認証時に "domain\_hint" クエリ パラメーターを Azure AD に送信します。 これらのフローは続行され、段階的なロールアウトが有効になっているユーザーは引き続き認証にフェデレーションを使用します。

<!-- -->

- 管理者はセキュリティ グループを使用してクラウド認証をロールアウトできます (オンプレミスの AD セキュリティ グループを使用する場合に同期の待機時間を回避するには、クラウド セキュリティ グループをお勧めします)。

    - **1 つの機能に対して最大 10 個のグループ**を使用できます。つまり、パスワード ハッシュ同期、パススルー認証、シームレス SSO のそれぞれに対して、ということです。

    - 入れ子になったグループは**サポートされていません**。 これは、パブリック プレビューのスコープでもあります。

    - 動的グループは、段階的なロールアウトでは**サポートされていません**。

    - グループ内の連絡先オブジェクトによって、グループ フォームの追加がブロックされます。

- 依然として、フェデレーションからクラウド認証への最終的な切り替えを Azure AD Connect または PowerShell を使用して行う必要があります。 この機能によって、ドメインがフェデレーションからマネージドに切り替えられることはありません。

- 段階的なロールアウトでセキュリティ グループを初めて追加する時点では、UX がタイムアウトしないようにするため、200 ユーザーに制限されます。UX にグループを追加した後、必要に応じてグループにユーザーを直接追加できます。

## <a name="get-started-with-staged-rollout"></a>段階的なロールアウトを使ってみる

段階的なロールアウトを使用してパスワード ハッシュ同期 (PHS) サインインをテストする場合は、以下の事前作業を完了して、パスワード ハッシュ同期の段階的なロールアウトを有効にしてください。

使用する PowerShell コマンドレットの詳細については、[AzureAD 2.0 プレビュー](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)をご覧ください

## <a name="pre-work-for-password-hash-sync"></a>パスワード ハッシュ同期のための事前作業

1. Azure AD Connect の  [[オプション機能]](how-to-connect-install-custom.md#optional-features)  ページから、パスワード ハッシュ同期を有効にします。 

   ![Azure Active Directory Connect の [オプション機能] ページのスクリーンショット](media/how-to-connect-staged-rollout/sr1.png)

1. すべてのユーザーのパスワード ハッシュが Azure AD に同期されるように、完全なパスワード ハッシュ同期サイクルが実行されたことを確認します。 パスワード ハッシュ同期の状態を確認するには、[こちら](tshoot-connect-password-hash-synchronization.md)で PowerShell 診断を使用します。

   ![AAD Connect のトラブルシューティング ログのスクリーンショット](./media/how-to-connect-staged-rollout/sr2.png)

段階的なロールアウトを使用してパススルー認証 (PTA) サインインをテストする場合は、以下の事前作業を完了して、PTA の段階的なロールアウトを有効にしてください。

## <a name="pre-work-for-pass-through-authentication"></a>パススルー認証のための事前作業

1. パススルー認証エージェントを実行する、Windows Server 2012 R2 以降を実行しているサーバーを特定します (**Azure AD Connect サーバーは選択しないでください**)。 そのサーバーがドメインに参加していて、選択したユーザーを Active Directory で認証できること、および送信ポートや URL で Azure AD と通信できることを確認します (詳しい[前提条件](how-to-connect-sso-quick-start.md)をご覧ください)。

1. Microsoft Azure AD Connect 認証エージェントを[ダウンロード](https://aka.ms/getauthagent) して、そのサーバーにインストールします。 

1.  [高可用性](how-to-connect-sso-quick-start.md)を有効にするため、他のサーバーに追加の認証エージェントをインストールします。

1. [スマート ロックアウトの設定](../authentication/howto-password-smart-lockout.md)が適切に構成されていることを確認します。 これは、ユーザーのオンプレミスの Active Directory アカウントが攻撃者によってロックアウトされないようにするためです。

段階的なロールアウトのために選択したサインイン方法 (PHS または PTA) に関係なく、シームレス SSO を有効にすることをお勧めします。 次の事前作業を完了して、段階的なロールアウトのためにシームレス SSO を有効にしてください。

## <a name="pre-work-for-seamless-sso"></a>シームレス SSO のための事前作業

PowerShell を使用して AD フォレストでシームレス SSO を有効にします。 複数の AD フォレストがある場合は、各フォレストについて個別にそれを有効にしてください。 シームレス SSO は、段階的なロールアウト用に選択したユーザーに対してのみトリガーされ、既存のフェデレーション セットアップには影響を与えません。

**手順の概要**

1. まず、Azure AD Connect サーバーにログインします。

2. %programfiles%\\Microsoft Azure Active Directory Connect フォルダーに移動します。

3. 次のコマンドを使用して、シームレス SSO PowerShell モジュールをインポートします:  `Import-Module .\AzureADSSO.psd1`。

4. PowerShell を管理者として実行します。 PowerShell で、 `New-AzureADSSOAuthenticationContext` を呼び出します。 このコマンドでは、テナントのグローバル管理者の資格情報を入力できるダイアログ ボックスが表示されます。

5.  `Get-AzureADSSOStatus | ConvertFrom-Json` を呼び出します。 このコマンドでは、この機能が有効になっている AD フォレストの一覧 (\"ドメイン\" の一覧を参照) が表示されます。 これは、既定ではテナント レベルで false に設定されています。

   > **例:** 
   > ![Windows PowerShell の出力の例](./media/how-to-connect-staged-rollout/sr3.png)

6.  `$creds = Get-Credential` を呼び出します。 メッセージが表示されたら、目的の AD フォレストのドメイン管理者の資格情報を入力します。

7. `Enable-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 このコマンドでは、シームレス SSO に必要なこの特定の Active Directory フォレスト用のオンプレミスのドメイン コントローラーから AZUREADSSOACC コンピューターのアカウントを作成します。

8. シームレス SSO を使用するには、URL がイントラネット ゾーンに含まれている必要があります。 グループ ポリシーを使用してこれらの URL を展開するには、[シームレス シングル サインオンに関するクイックスタート](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)をご覧ください。

9.  完全なチュートリアルについては、シームレス SSO 用の[デプロイ計画](https://aka.ms/SeamlessSSODPDownload)をダウンロードすることもできます。

## <a name="enable-staged-rollout"></a>段階的なロールアウトの有効化

グループ内の選択した一連のユーザーに特定の機能 (パススルー認証、パスワード ハッシュ同期、シームレス SSO) をロールアウトするには、次の手順に従います。

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>テナントで特定の機能を段階的にロールアウトできるようにする

これらのオプションのいずれかをロールアウトすることができます

-   パスワード ハッシュ同期 + シームレス SSO **(オプション A)**

-   パススルー認証 + シームレス SSO **(オプション B)**

-   パスワード ハッシュ同期 + パススルー認証 + シームレス SSO **-\>** ***サポートされていません***

以下の手順を実行します。

1. 次の URL を使用して Azure AD ポータルにログインし、プレビュー UX にアクセスします。

   > <https://aka.ms/stagedrolloutux>

2. [マネージド ユーザー サインインの段階的なロールアウトを有効にする (プレビュー)] をクリックします

   *例:* (**オプション B**) パスワード ハッシュ同期とシームレス SSO を有効にする場合は、次に示すように、[パスワード ハッシュ同期] および [シームレスなシングル サインオン] 機能をスライドして **[オン]** にしてください。

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. 各グループを機能に追加して、パススルー認証とシームレスなシングル サインオンを有効にします。 UX のタイムアウトを避けるために、最初は、セキュリティ グループに含まれるメンバーが 200 人を超えないようにしてください。

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >グループ内のメンバーは、段階的なロールアウトに対して自動的に有効になります。 入れ子になったグループと動的グループは、段階的なロールアウトではサポートされていません。

## <a name="auditing"></a>監査

段階的なロールアウトのために実行するさまざまなアクションについて、監査イベントが有効にされました。

- パスワード ハッシュ同期、パススルー認証、シームレス SSO に対して段階的なロールアウトを有効にしたときの監査イベント。

  >[!NOTE]
  >StagedRollout を使用して SeamlessSSO を **[オン]** にしたときにログに記録される監査イベント。

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- パスワード ハッシュ同期、パススルー認証、シームレス SSO に対してグループを追加したときの監査イベント。

  >[!NOTE]
  >グループが段階的なロールアウトのためにパスワード ハッシュ同期に追加されたときにログに記録される監査イベント

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- グループに追加されたユーザーが段階的なロールアウトに対して有効にされたときの監査イベント

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>検証

パスワード ハッシュ同期またはパススルー認証を使用したサインイン (ユーザー名とパスワードによるサインイン) をテストするには、次のようにします:

1. エクストラネットからプライベート ブラウザー セッションで <https://myapps.microsoft.com> に移動し、段階的なロールアウト用に選択したユーザー アカウントの UserPrincipalName (UPN) を入力します。

1. ユーザーが段階的なロールアウトの対象になっている場合、そのユーザーはフェデレーション ログイン ページにはリダイレクトされず、代わりに Azure AD テナントのブランド化されたサインイン ページでサインインするように求められます。

1. UserPrincipalName でフィルター処理して、[Azure AD サインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md)にサインインが正常に表示されていることを確認します。

シームレス SSO を使用したサインインをテストするには、次のようにします:

1. イントラネットからプライベート ブラウザー セッションで <https://myapps.microsoft.com>/ に移動し、段階的なロールアウト用に選択したユーザー アカウントの UserPrincipalName (UPN) を入力します。

1. そのユーザーがシームレスな SSO の段階的なロールアウトの対象になっている場合は、ユーザーに "サインインしようとしています..." というメッセージが表示されてから、サイレントにサインインが実行されます。

1. UserPrincipalName でフィルター処理して、[Azure AD サインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md)にサインインが正常に表示されていることを確認します。

フェデレーション プロバイダーでも引き続きユーザーがサインインできることを確認するには、次のようにします。

段階的なロールアウトに対して選択したユーザーについてまだ AD FS でもユーザーがサインインできることを追跡するには、[この手順](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)をご使用ください。 サード パーティのフェデレーション プロバイダーでこれを確認する方法については、ベンダーのドキュメントを確認してください。

## <a name="roll-back"></a>ロールバック

### <a name="remove-a-user-from-staged-rollout"></a>段階的なロールアウトからユーザーを削除する

1.  グループからユーザーを削除すると、そのユーザーの段階的なロールアウトが無効になります。

2.  段階的なロールアウト機能を無効にする場合は、機能を **[オフ]** 状態に切り替えて、段階的なロールアウトを無効にしてください。


## <a name="frequently-asked-questions"></a>よく寄せられる質問

-   **Q:顧客はこの機能を運用環境で使用できますか。**

-   A:はい。この機能は運用テナントで使用できますが、まずテスト テナントでこの機能を試してみることをお勧めします。

-   **Q:この機能を使用して、一部のユーザーがフェデレーション認証を使用し、その他のユーザーがクラウド認証を使用するという永続的な "共存" を維持することはできますか。**

-   A:いいえ。この機能は、フェデレーションからクラウド認証に段階的に移行し、最終的にクラウド認証に切り替えるように設計されています。 予期しない認証フローにつながる可能性があるため、永続的な混在状態はお勧めしません。

-   **Q:PowerShell を使用して段階的なロールアウトを実行できますか。**

-   A:はい。PowerShell を使用して段階的なロールアウトを実行する方法については、[こちら](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)をご覧ください。

## <a name="next-steps"></a>次の手順
- [AzureAD 2.0 プレビュー](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
