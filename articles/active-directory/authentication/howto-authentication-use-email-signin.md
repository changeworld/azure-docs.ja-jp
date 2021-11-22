---
title: 代替ログイン ID としてメール アドレスを使用して Azure AD にサインインする
description: ユーザーが代替ログイン ID としてメール アドレスを使用して Azure Active Directory にサインインできるようにする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/07/2021
ms.author: justinha
author: calui
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: e7b34e98776f252c2122d58601c8067df208b2f2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486787"
---
# <a name="sign-in-to-azure-ad-with-email-as-an-alternate-login-id-preview"></a>代替ログイン ID としてメール アドレスを使用して Azure AD にサインインする (プレビュー)

> [!NOTE]
> 代替ログイン ID としてメール アドレスを使用した Azure AD へのサインインは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

多くの組織では、ユーザーがオンプレミス ディレクトリ環境と同じ資格情報を使用して Azure Active Directory (Azure AD) にサインインできるようにしたいと考えています。 ハイブリッド認証と呼ばれるこの方法では、ユーザーは 1 組の資格情報を記憶しておくだけでよくなります。

一部の組織では、次の理由によりハイブリッド認証に移行していません。

* 既定で、Azure AD のユーザー プリンシパル名 (UPN) が、オンプレミス UPN と同じ値に設定されています。
* Azure AD UPN を変更すると、オンプレミス環境と Azure AD 環境の間で不一致が生じ、特定のアプリケーションやサービスで問題が発生する可能性があります。
* ビジネスまたはコンプライアンス上の理由により、組織では、Azure AD へのサインインにオンプレミスの UPN を使用したくないと考えています。

ハイブリッド認証への移行を支援するために、ユーザーが代替ログイン ID として各自のメール アドレスでサインインできるように Azure AD を構成できます。 たとえば、*Contoso* が *Fabrikam* にブランド変更した場合、従来の `ana@contoso.com` UPN でのサインインを続行するのではなく、代替ログイン ID としてメール アドレスを使用できます。 アプリケーションまたはサービスにアクセスするために、ユーザーは、UPN 以外のメール アドレス (`ana@fabrikam.com` など) を使用して Azure AD にサインインします。

![代替ログイン ID としてのメール アドレスの図。](media/howto-authentication-use-email-signin/email-alternate-login-id.png)

この記事では、代替ログイン ID として電子メールを有効にして使用する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

代替ログイン ID としてのメール アドレスについて知る必要がある情報を次に示します。

* この機能は、Azure AD Free エディション以上で使用できます。
* この機能を使用すると、クラウドで認証された Azure AD ユーザーに対して、UPN に加え、*ProxyAddresses* を使用したサインインを有効にすることができます。 Azure AD B2B シナリオへの適用に関する詳細については、「[B2B](#b2b-guest-user-sign-in-with-an-email-address)」に記載されています。
* ユーザーが UPN 以外のメール アドレスを使用してサインインすると、[ID トークン](../develop/id-tokens.md)内の `unique_name` と `preferred_username` のクレーム (存在する場合) では UPN 以外のメール アドレスが返されます。
* この機能では、パスワード ハッシュ同期 (PHS) またはパススルー認証 (PTA) による、マネージド認証がサポートされています。
* この機能を構成するには、2 つのオプションがあります。
    * [ホーム領域検出 (HRD) ポリシー](#enable-user-sign-in-with-an-email-address) - テナント全体に対して機能を有効にするには、このオプションを使用します。 グローバル管理者特権が必要です。
    * [段階的なロールアウト ポリシー](#enable-staged-rollout-to-test-user-sign-in-with-an-email-address) - 特定の Azure AD グループで機能をテストするには、このオプションを使用します。 グローバル管理者特権が必要です。

## <a name="preview-limitations"></a>プレビューの制限事項

現在のプレビュー状態では、代替ログイン ID としてのメール アドレスに次の制限が適用されます。

* **ユーザー エクスペリエンス** - UPN 以外のメール アドレスを使用してサインインした場合でも、ユーザーに UPN が表示される場合があります。 次のような動作の例が表示されます。
    * `login_hint=<non-UPN email>` を使用した Azure AD サインインに誘導される場合、ユーザーは UPN でサインインするように求められます。
    * ユーザーが UPN 以外のメール アドレスを使用してサインインし、間違ったパスワードを入力すると、 *[パスワードの入力]* ページが UPN の表示に変わります。
    * Microsoft Office など、一部の Microsoft サイトおよびアプリでは、右上に通常表示される *[アカウント マネージャー]* コントロールに、サインインに使用された UPN 以外のメール アドレスではなく、ユーザーの UPN が表示される場合があります。

* **サポートされていないフロー** - 現在、次のような一部のフローは、UPN 以外のメール アドレスとは互換性がありません。
    * UPN 以外のメール アドレスは、Identity Protection で "*漏洩した資格情報*" のリスク検出と照合されません。 このリスク検出では、UPN を使用して、漏洩した資格情報を照合します。 詳細については、[Azure AD Identity Protection のリスク検出と修復][identity-protection]に関する記事を参照してください。
    * ユーザーが UPN 以外のメール アドレスを使用してサインインしている場合、ユーザーは自分のパスワードを変更できません。 Azure AD セルフサービス パスワード リセット (SSPR) は想定どおりに機能します。 SSPR 中に、ユーザーが UPN 以外のメール アドレスを使用して自分の ID を確認すると、UPN が表示される場合があります。

* **サポートされていないシナリオ** - 次のシナリオはサポートされていません。 UPN 以外のメール アドレスを使用した次へのサインイン:
    * [ハイブリッド Azure AD 参加済みデバイス](../devices/concept-azure-ad-join-hybrid.md)
    * [Azure AD 参加済みデバイス](../devices/concept-azure-ad-join.md)
    * [Azure AD 登録済みデバイス](../devices/concept-azure-ad-register.md)
    * [リソース所有者のパスワード資格情報 (ROPC) を使用したアプリケーション](../develop/v2-oauth-ropc.md)
    * POP3 や SMTP などのレガシ認証を使用したアプリケーション
    * Skype for Business
    * macOS 上の Microsoft Office
    * Microsoft 365 管理者ポータル

* **サポートされていないアプリ** - `unique_name` または `preferred_username` クレームが不変であると想定する場合、または UPN などの特定のユーザー属性に常に一致する場合は、一部のサードパーティアプリケーションが期待どおりに動作しないことがあります。

* **ログ記録** - HRD ポリシーで機能の構成に加えた変更は、監査ログに明示的には表示されません。 また、サインイン ログの *サインイン識別子の種類* フィールドは、常に正確であるとは限らないため、サインインに機能が使用されているかどうかを判断するためには使用しないでください。

* **段階的ロールアウト ポリシー** -次の制限は、段階的ロールアウト ポリシーを使用して機能が有効になっている場合にのみ適用されます。
    * この機能は、他の段階的ロールアウト ポリシーに含まれているユーザーに対しては想定どおりに機能しません。
    * 段階的ロールアウト ポリシーでは、機能ごとに最大 10 個のグループがサポートされます。
    * 段階的ロールアウト ポリシーでは、入れ子になったグループはサポートされていません。
    * 段階的ロールアウト ポリシーでは、動的グループはサポートされていません。
    * グループ内の連絡先オブジェクトによって、段階的ロールアウト ポリシーへのグループの追加がブロックされます。

* **重複値** - テナント内では、クラウド専用ユーザーの UPN が、オンプレミスのディレクトリから同期された別のユーザーのプロキシ アドレスと同じ値になる場合があります。 このシナリオでは、機能を有効にすると、クラウド専用のユーザーは UPN でサインインできなくなります。 この問題の詳細については、「[トラブルシューティング](#troubleshoot)」セクションを参照してください。

## <a name="overview-of-alternate-login-id-options"></a>代替ログイン ID オプションの概要
Azure AD にサインインするために、ユーザーは自分のアカウントを一意に識別する値を入力します。 これまでは、サインイン ID として使用できるのは Azure AD UPN のみでした。

オンプレミスの UPN がユーザーの推奨されるサインイン メールである組織の場合、この方法は優れていました。 そうした組織では、Azure AD UPN をオンプレミスの UPN とまったく同じ値に設定することで、ユーザーの一貫したサインイン エクスペリエンスを実現しています。

### <a name="alternate-login-id-for-ad-fs"></a>AD FS の代替ログイン ID

ただし、組織によっては、オンプレミスの UPN をサインイン ID として使用していません。 オンプレミス環境では、代替ログイン ID を使用してサインインできるようにローカル AD DS を構成しています。 Azure AD UPN をオンプレミスの UPN と同じ値に設定すると、Azure AD でユーザーはその値を使用してサインインするよう求められるため、そうするわけにはいきません。

### <a name="alternate-login-id-in-azure-ad-connect"></a>Azure AD Connect での代替ログイン ID

この問題の一般的な回避策は、Azure AD UPN を、ユーザーがサインインに使用するメール アドレスに設定することでした。 この方法は有効です。ただし、結果的にオンプレミスの AD と Azure AD で UPN が異なります。また、この構成はすべての Microsoft 365 ワークロードと互換性があるわけではありません。

### <a name="email-as-an-alternate-login-id"></a>代替ログイン ID としてのメール アドレス

別の方法は、Azure AD とオンプレミスの UPN を同じ値に同期し、ユーザーが確認済みのメールを使用して Azure AD にサインインできるように Azure AD を構成するというものです。 この機能を実現するには、オンプレミス ディレクトリ内のユーザーの *ProxyAddresses* 属性で 1 つ以上のメール アドレスを定義します。 そうすると、*ProxyAddresses* が Azure AD Connect を使用して自動的に Azure AD と同期されます。


| オプション | 説明 |
|---|---|
| [AD FS の代替ログイン ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) | AD FS ユーザーの代替属性 (Mail など) を使用したサインインを有効にします。 |
| [Azure AD Connect での代替ログイン ID](../hybrid/plan-connect-userprincipalname.md#alternate-login-id) | 代替属性 (Mail など) を Azure AD UPN として同期します。 |
| 代替ログイン ID としてのメール アドレス | Azure AD ユーザーに対して、検証済みドメインの *ProxyAddresses* を使用したサインインを有効にします。 |

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>サインインのメール アドレスを Azure AD に同期する

従来の Active Directory ドメイン サービス (AD DS) 認証または Active Directory フェデレーションサービス (AD FS) 認証は、ネットワーク上で直接行われ、AD DS インフラストラクチャによって処理されます。 ハイブリッド認証では、ユーザーは代わりに Azure AD に直接サインインすることができます。

このハイブリッド認証アプローチをサポートするには、オンプレミスの AD DS 環境を、[Azure AD Connect][azure-ad-connect] を使用して Azure AD に同期させ、PHS または PTA を使用するように構成します。 詳細については、「[Azure AD ハイブリッド ID ソリューションの適切な認証方法を選択する][hybrid-auth-methods]」をご覧ください。

どちらの構成オプションでも、ユーザーはユーザー名とパスワードを Azure AD に送信し、ここで資格情報が検証され、チケットが発行されます。 ユーザーが Azure AD にサインインすると、組織が AD FS インフラストラクチャをホストし管理する必要性はなくなります。

Azure AD Connect によって自動的に同期されるユーザー属性の 1 つが *ProxyAddresses* です。 オンプレミスの AD DS 環境で *ProxyAddresses* 属性の一部として定義されているメール アドレスをユーザーが持っている場合は、Azure AD に自動的に同期されます。 このメール アドレスは、直接 Azure AD サインイン プロセスで、代替ログイン ID として使用できます。

> [!IMPORTANT]
> テナントの確認済みドメインのメールのみが Azure AD に同期されます。 それぞれの Azure AD テナントには、1 つ以上の確認済みドメインがあり、これらは所有権が実証され、テナントに一意にバインドされます。
>
> 詳細については、[Azure AD でのカスタム ドメイン名の追加と確認][verify-domain]に関するページをご覧ください。

## <a name="b2b-guest-user-sign-in-with-an-email-address"></a>メール アドレスを使用した B2B ゲスト ユーザーのサインイン

![B2B ゲスト ユーザーのサインイン用の代替ログイン ID としてのメール アドレスの図。](media/howto-authentication-use-email-signin/email-alternate-login-id-b2b.png)

代替ログイン ID としてのメール アドレスは、"サインイン識別子持ち込み" モデルの下で [Azure AD 企業間 (B2B) コラボレーション](../external-identities/what-is-b2b.md)に適用されます。 代替ログイン ID としてのメール アドレスがホーム テナントで有効となっている場合、Azure AD ユーザーは、UPN 以外のメール アドレスを使用して、リソース テナント エンドポイントでゲスト サインインを実行できます。 この機能を有効にするために、リソース テナントで要求される操作はありません。

## <a name="enable-user-sign-in-with-an-email-address"></a>メール アドレスを使用してユーザーのサインインを有効にする

> [!NOTE]
> この構成オプションには HRD ポリシーを使用します。 詳細については、「[homeRealmDiscoveryPolicy リソースの種類](/graph/api/resources/homeRealmDiscoveryPolicy?view=graph-rest-1.0&preserve-view=true)」を参照してください。

*ProxyAddresses* 属性を適用したユーザーが Azure AD Connect を使用して Azure AD に同期された後、ユーザーがテナントの代替ログイン ID としてメールを使用してサインインできるようにする機能を有効にする必要があります。 この機能を使用することで、UPN 値に対してだけでなく、メール アドレスの *ProxyAddresses* 値に対してサインイン ID を確認するように、Azure AD ログイン サーバーに指示します。

現在、プレビュー期間中は、PowerShell を使用して、代替ログイン ID 機能としてメールを使用したサインインのみを有効にすることができます。 次の手順を完了するには、*グローバル管理者* の権限が必要です。

1. 管理者として PowerShell セッションを開き、[Install-Module][Install-Module] コマンドレットを使用して、*AzureADPreview* モジュールをインストールします。

    ```powershell
    Install-Module AzureADPreview
    ```

    メッセージが表示されたら、 **[Y]** を選択して NuGet をインストールするか、信頼されていないリポジトリからインストールします。

1. [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して "*グローバル管理者*" として Azure AD テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

    このコマンドは、アカウント、環境、およびテナント ID に関する情報を返します。

1. 次のように [Get-AzureADPolicy][Get-AzureADPolicy] コマンドレットを使用して、*HomeRealmDiscoveryPolicy* がテナントに既に存在しているかどうかを確認します。

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. 現在構成されているポリシーがない場合、コマンドは何も返しません。 ポリシーが返された場合は、この手順をスキップし、次の手順に進んで、既存のポリシーを更新します。

    *HomeRealmDiscoveryPolicy* ポリシーをテナントに追加するには、次の例に示すように、[New-AzureADPolicy][New-AzureADPolicy] コマンドレットを使用し、*AlternateIdLogin* 属性を *"Enabled": true* に設定します。

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    ポリシーが正常に作成されると、次の出力例に示すように、コマンドによってポリシー ID が返されます。

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 既にポリシーが構成されている場合は、次のポリシー出力の例に示すように、 *AlternateIdLogin*  属性が有効になっているかどうかを確認します。

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    ポリシーが存在していても、*AlternateIdLogin* 属性が存在していないか有効になっていない場合、または他の属性が保持したいと考えているポリシーに存在している場合は、[Set-AzureADPolicy][Set-AzureADPolicy] コマンドレットを使用して既存のポリシーを更新します。

    > [!IMPORTANT]
    > ポリシーを更新するときには、必ず古い設定と新しい  *AlternateIdLogin* 属性を含めます。

    次の例では、 *AlternateIdLogin* 属性を追加し、既に設定されている可能性のある  *AllowCloudPasswordValidation* 属性を保持します。

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    更新されたポリシーによって変更が表示され、*AlternateIdLogin* 属性が有効になっていることを確認します。

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

ポリシーが適用されている場合は、伝達されて、ユーザーが代替ログイン ID を使用してサインインできるようになるまでに、最大 1 時間かかることがあります。

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>メール アドレスを使用したユーザー サインインをテストするために段階的なロールアウトを有効にする  

> [!NOTE]
>この構成オプションには、段階的ロールアウト ポリシーを使用します。 詳細については、「[featureRolloutPolicy リソースの種類](/graph/api/resources/featurerolloutpolicy?preserve-view=true&view=graph-rest-1.0)」を参照してください。

段階的なロールアウト ポリシーを使用すると、テナント管理者は特定の Azure AD グループに対して各機能を有効にすることができます。 テナント管理者が、段階的なロールアウトを使用して、メール アドレスでのユーザー サインインをテストすることをお勧めします。 管理者は、この機能をテナント全体にデプロイする準備ができたら、[HRD ポリシー](#enable-user-sign-in-with-an-email-address)を使用する必要があります。  


次の手順を完了するには、*グローバル管理者* の権限が必要です。

1. 管理者として PowerShell セッションを開き、[Install-Module][Install-Module] コマンドレットを使用して、*AzureADPreview* モジュールをインストールします。

    ```powershell
    Install-Module AzureADPreview
    ```

    メッセージが表示されたら、 **[Y]** を選択して NuGet をインストールするか、信頼されていないリポジトリからインストールします。

1. [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して "*グローバル管理者*" として Azure AD テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

    このコマンドは、アカウント、環境、およびテナント ID に関する情報を返します。

1. 次のコマンドレットを使用して、既存の段階的なロールアウト ポリシーをすべて一覧表示します。
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

1. この機能について既存の段階的なロールアウト ポリシーが存在しない場合は、段階的ロールアウト ポリシーを新たに作成し、ポリシー ID を書き留めておきます。

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

1. 段階的なロールアウト ポリシーに追加されるグループの directoryObject ID を検索します。 次の手順で使用するため、*Id* パラメーターに返される値をメモに記録します。
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

1. 次の例に示すように、グループを段階的なロールアウト ポリシーに追加します。 *-Id* パラメーターの値を、手順 4 でポリシー ID に対して返された値に置き換え、 *-RefObjectId* パラメーターの値を、手順 5 でメモに記録した *Id* に置き換えます。 グループ内のユーザーが代替ログイン ID としてメール アドレスを使用して Azure AD にサインインできるようになるまで、最大 1 時間かかることがあります。

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
新しいメンバーをグループに追加した場合、代替ログイン ID としてメール アドレスを使用して Azure AD にサインインできるようになるまで、最大で 24 時間かかることがあります。

### <a name="removing-groups"></a>グループの削除

段階的なロールアウト ポリシーからグループを削除するには、次のコマンドを実行します。

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>ポリシーの削除

段階的なロールアウト ポリシーを削除するには、まずポリシーを無効にして、システムから削除します。

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="test-user-sign-in-with-an-email-address"></a>メール アドレスを使用してユーザーのサインインをテストする

ユーザーがメール アドレスを使用してサインインできるかどうかをテストするには、[https://myprofile.microsoft.com][my-profile] に移動し、UPN 以外のメール アドレス (`balas@fabrikam.com` など) を使用してサインインします。 サインイン エクスペリエンスは、UPN を使用したサインインと同じ外観になります。

## <a name="troubleshoot"></a>トラブルシューティング

ユーザーがメール アドレスを使用してサインインするときに問題が発生した場合は、次のトラブルシューティングの手順を確認してください。

1. メール アドレスが代替ログイン ID として有効になってから少なくとも 1 時間が経過していることを確認します。 段階的ロールアウト ポリシーの適用対象であるグループにユーザーが最近追加された場合は、グループに追加されてから少なくとも 24 時間が経過していることを確認します。
1. HRD ポリシーを使用している場合は、Azure AD *HomeRealmDiscoveryPolicy* の *AlternateIdLogin* 定義プロパティが *"Enabled": true* に設定され、*IsOrganizationDefault* プロパティが *True* に設定されていることを確認します。

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```
    段階的ロールアウト ポリシーを使用している場合は、Azure AD *FeatureRolloutPolicy* の *IsEnabled* プロパティが *True* に設定されていることを確認します。

    ```powershell
    Get-AzureADMSFeatureRolloutPolicy
    ```
1. Azure AD で、*ProxyAddresses* 属性にユーザー アカウントのメール アドレスが設定されていることを確認します。

### <a name="conflicting-values-between-cloud-only-and-synced-users"></a>クラウド専用ユーザーと同期されたユーザーの間で競合する値

テナント内では、クラウド専用ユーザーの UPN が、オンプレミス ディレクトリから同期された別のユーザーのプロキシ アドレスと同じ値になる場合があります。 このシナリオでは、機能を有効にすると、クラウド専用のユーザーは UPN でサインインできなくなります。 この問題のインスタンスを検出する手順を次に示します。

1. 管理者として PowerShell セッションを開き、[Install-Module][Install-Module] コマンドレットを使用して、*AzureADPreview* モジュールをインストールします。

    ```powershell
    Install-Module AzureADPreview
    ```

    メッセージが表示されたら、 **[Y]** を選択して NuGet をインストールするか、信頼されていないリポジトリからインストールします。

1. [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して "*グローバル管理者*" として Azure AD テナントにサインインします。

    ```powershell
    Connect-AzureAD
    ```

1. 影響を受けるユーザーを取得します。

    ```powershell
    # Get all users
    $allUsers = Get-AzureADUser -All $true
    
    # Get list of proxy addresses from all synced users
    $syncedProxyAddresses = $allUsers |
        Where-Object {$_.ImmutableId} |
        Select-Object -ExpandProperty ProxyAddresses |
        ForEach-Object {$_ -Replace "smtp:", ""}
    
    # Get list of user principal names from all cloud-only users
    $cloudOnlyUserPrincipalNames = $allUsers |
        Where-Object {!$_.ImmutableId} |
        Select-Object -ExpandProperty UserPrincipalName
    
    # Get intersection of two lists
    $duplicateValues = $syncedProxyAddresses |
        Where-Object {$cloudOnlyUserPrincipalNames -Contains $_}
    ``` 

1. 影響を受けるユーザーを出力するには:

    ```powershell
    # Output affected synced users
    $allUsers |
        Where-Object {$_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    
    # Output affected cloud-only users
    $allUsers |
        Where-Object {!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    ```

1. 影響を受けるユーザーを CSV に出力するには:

    ```powershell
    # Output affected users to CSV
    $allUsers |
        Where-Object {
            ($_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0) -Or
            (!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName)
        } |
        Select-Object ObjectId, DisplayName, UserPrincipalName, @{n="ProxyAddresses"; e={$_.ProxyAddresses -Join ','}}, @{n="IsSyncedUser"; e={$_.ImmutableId.Length -GT 0}}, UserType |
        Export-Csv -Path .\AffectedUsers.csv -NoTypeInformation
    ```

## <a name="next-steps"></a>次のステップ

Azure AD アプリ プロキシや Azure AD Domain Services などのハイブリッド ID の詳細については、[オンプレミス ワークロードのアクセスおよび管理の Azure AD ハイブリッド ID][hybrid-overview] に関するページをご覧ください。

ハイブリッド ID 操作の詳細については、[パスワード ハッシュ同期方法][phs-overview]または[パススルー認証][pta-overview]の同期をご覧ください。

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
