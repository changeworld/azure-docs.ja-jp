---
title: Azure へのサインインにメール アドレスを使うゲスト ユーザーのユーザー アカウントを同期する | Microsoft Docs
description: この記事では、アプリケーションへのサインインに別の ID を使うゲスト ユーザー アカウントを同期する方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: 4e6cf3bb4a691380a5fe22f5afdf749b40f15ef3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>サインインにメール アドレスを使うゲスト ユーザー アカウントを同期する (プレビュー)

>[!NOTE]
> 現在、この機能はパブリック プレビュー段階にあります。

次のシナリオでは、オンプレミス AD 環境に外部ユーザー (代替のサインイン方法を使用するパートナーなど) がいる可能性がある状況に対応しています。

前述の例で、Nina Morin は Fabrikam に勤務し、nmorin@fabrikam.com というメール アドレスを持っています。Nina は Contoso のパートナーであり、Contoso が所有している特定のアプリケーションにアクセスする必要があります。 Contoso は Nina のアカウントを作成し、そのメール アドレスを使用してアプリケーションにサインインするように Nina に指示しました。

オンプレミス アプリケーションの場合、このシナリオはうまく機能しました。 ただし、今回、Contoso はこれらのアプリケーションをクラウドに移行し、パートナーのエクスペリエンスが同じになるようにしたいと考えています。

このシナリオでは、この状況に対応します。


## <a name="pre-requisites-and-assumptions"></a>前提条件と想定環境
このセクションでは、このシナリオの設定を試みる前に知っておく必要がある前提条件と想定環境の一覧について説明します。

### <a name="pre-requisites"></a>前提条件
- Azure AD Connect を構成し、ドメインを検証し、ドメイン フェデレーション設定を構成できるグローバル管理者の資格情報
- Azure AD Connect バージョン 1.1.524.0 以降
- 外部ユーザーのクラウド UPN (例: bmcontoso.com) を設定する検証済みドメイン。
- 外部ユーザーを認証するフェデレーション サービス。 AD FS を使用する場合は、2012 R2 以降である必要があります
- フェデレーションの設定を検証するには、MSOL PowerShell v1.1 がコンピューターにインストールされている必要があります。 詳細については、「[Azure Active Directory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)」を参照してください。


### <a name="assumptions"></a>前提条件 
- Azure AD Connect は既に設定され、正常にインストールされています。 Azure AD Connect のインストール方法については、「[Azure AD Connect とフェデレーション](active-directory-aadconnectfed-whatis.md)」を参照してください。
このドキュメントは以下を前提としています。
- フェデレーション サービスが設定され、ユーザーが正常に認証されています。
- 外部ユーザーが外部メール アドレスを使用して認証できます。
- - サインインの代替 ID の使用が設定され、構成されています。 ユーザーは代替 ID を使用して認証できます。 AD FS を使用して代替 ID を設定する方法の詳細については、「[Configure Alternate Login ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)」(代替ログイン ID の構成) を参照してください。

## <a name="task-1--prepare-the-environment"></a>タスク 1: 環境を準備する
次のタスクは、メール属性などの代替 ID を使用してサインインできるように、外部アカウントの同期を開始する準備を整えるための参考情報です。

2 つ目のタスクに進む前に、以下の表の項目を定義します。

![アーキテクチャ](media/active-directory-aadconnect-guest-sync/guest2.png)

|環境の側面|用途|環境内の実装|
|-----|-----|-----|
|クラウドの UPN 属性|クラウド内の外部ユーザー オブジェクトの UPN を設定する属性。 外部アカウントの UPN サフィックスは、前提条件で定義されているものである必要があります。 これは検証済みのドメインです。|* 例: UserPrincipalName (nmorin@bmcontoso.com)|
|サインイン アドレス|ログイン時に外部ユーザーが入力する属性。 この属性にはメール アドレス形式を指定する必要があります。ほとんどの場合、この属性は外部ユーザーの実際のメール アドレスと一致します。|*例: mail (nmorin@fabrikam.com)|
|Azure AD Connect Scoped Filter|外部 ID を対象にして、このガイドの後半で定義されている同期ルールの範囲を指定できるフィルター。 一般的な範囲の指定方法には、組織内の事前定義された OU、特定の名前付け規則、特定のドメインなどがあります。|* 例: OU に外部が含まれる|
|Azure AD テナント|Azure AD Connect に表示される Azure AD テナントの名前。|例: contoso.onmicrosoft.com|

次のスクリーン ショットには 3 つの枠線があります。
- **外部** OU。Azure AD Connect Scoped Filter で使用されます。外部ユーザーの場所です。
- **mail** 属性。外部ユーザーがサインインに使用します。
- **userPrincipalName** 属性。オンプレミス環境がフェデレーションされている検証済みドメインです。

![User](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>タスク 2: Azure AD Connect を構成する
上記の情報を定義したら、Azure AD Connect 同期ルールの設定に進むことができます。 ルールを設定するには、Azure AD Connect 同期ルール エディターを使用します。 エディターの詳細については、[宣言型のプロビジョニング](active-directory-aadconnectsync-understanding-declarative-provisioning.md)に関するページを参照してください。

### <a name="how-to-configure-the-synchronization-rule"></a>同期ルールを構成する方法
次の手順で Azure AD Connect を構成します。

1. **[スタート]、[Azure AD Connect]、[同期ルール エディター]** の順に移動して、Azure AD Connect 同期ルール エディターを開きます。
2. **同期ルール エディター**画面で、方向が**受信**であることを確認し、右側にある **[新しいルールの追加]** をクリックします。
3. **[説明]** ページで次のように構成し、**[次へ]** をクリックします。
    - **[名前]** - ルールの名前を入力します 
    - **[Connected System]\(接続先システム\)** - オンプレミス AD 環境
    - **[Connected System Object Type]\(接続先システム オブジェクトの種類\)** - ユーザー
    - **[Metaverse Object Type]\(メタバース オブジェクトの種類\)** - 人
    - **[リンクの種類]** - 結合
    - **[優先順位]** - 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. **[スコープ フィルター]** 画面で **[グループの追加]** をクリックします。
5. ドロップダウンを使用してフィルターを構成します。 以下を入力して **[次へ]** をクリックします。 この操作で、外部 OU にあるオブジェクトにのみ適用されるフィルターが作成されます。
    - **[属性]** - dn
    - **[演算子]** - 次を含む
    - **[値]** - 外部
 
 ![filter](media/active-directory-aadconnect-guest-sync/guest4.png)

6. **[Join Rules]\(ルールの結合\)** 画面で **[次へ]** をクリックします。
7. **[変換]** 画面で **[変換の追加]** をクリックします。 次の情報を入力します。
    - **[FlowType]** - 定数
    - **[対象の属性]** - userType
    - **[ソース]** - ゲスト
8. **[変換]** 画面で **[変換の追加]** をクリックします。 次の情報を入力します。
    - **[FlowType]** - 直接
    - **[対象の属性]** - onPremisesUserPrincipalName
    - **[ソース]** - メール
9. **[変換]** 画面で **[変換の追加]** をクリックします。 次の情報を入力します。
    - **[FlowType]** - 直接
    - **[対象の属性]** - userPrincipalName
    - **[ソース]** - userPrincipalName ![変換](media/active-directory-aadconnect-guest-sync/guest5.png)
10. **[追加]** をクリックします。 
11. **同期ルール エディター**画面で、方向が**送信**であることを確認し、右側にある **[新しいルールの追加]** をクリックします。
12. **[説明]** ページで次のように構成し、**[次へ]** をクリックします。
    - **[名前]** - ルールの名前を入力します 
    - **[Connected System]\(接続先システム\)** - Azure AD テナント
    - **[Connected System Object Type]\(接続先システム オブジェクトの種類\)** - ユーザー
    - **[Metaverse Object Type]\(メタバース オブジェクトの種類\)** - 人
    - **[リンクの種類]** - 結合
    - **[優先順位]** - 90
    - 
![ルール](media/active-directory-aadconnect-guest-sync/guest6.png)

13. **[スコープ フィルター]** 画面で **[次へ]** をクリックします。
14. **[Join Rules]\(ルールの結合\)** 画面で **[次へ]** をクリックします。
15. **[変換]** 画面で **[変換の追加]** をクリックします。  次の情報を入力します。
    - **[FlowType]** - 直接
    - **[対象の属性]** - userType
    - **[ソース]** - userType
9. **[変換]** 画面で **[変換の追加]** をクリックします。 次の情報を入力します。
    - **[FlowType]** - 直接
    - **[対象の属性]** - onPremisesUserPrincipalName
    - **[ソース]** - onPremisesUserPrincipalName ![変換](media/active-directory-aadconnect-guest-sync/guest7.png)
10. **[追加]** をクリックします。 
11. これらのルールを構成した後は、完全な同期を実行する必要があります。 PowerShell を使用して完全な同期を開始します。 同期が完了したら、次の手順に進むことができます。

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>タスク 3: フェデレーション
次のタスクは、シナリオを機能させるために必要な設定をいくつか紹介する参考情報です。

Azure で Azure AD PowerShell を使用してフェデレーション設定を確認することができます。 このドキュメントでは、v1.1 の MSOL PowerShell を使用します。 このバージョンは[こちら](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)からインストールできます。

### <a name="verify-the-federation-settings"></a>フェデレーション設定を確認する
次の手順でフェデレーション設定を確認します。
1. Windows PowerShell を開き、次のコマンドを使用して Azure AD に接続します。
``` powershell
      Connect-MSOLservice
```
2.  グローバル管理者の資格情報を入力します
3.  次のコマンドを入力します。
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   フェデレーション情報が返されます。 **ActiveLogonUri** がフェデレーション サーバーの URL であることを確認します。

  ![フェデレーション](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>代替ログイン ID を確認する
このドキュメントでは、ID プロバイダー (Idp) として AD FS を使用します。 別の Idp を使用している場合、これらの手順は異なる可能性があります。

1. Windows PowerShell を開き、次のコマンドを入力します。
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. AD FS 情報が表示されます。  **AlternateLoginID** と **LookupForests** を確認します。

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>タスク 4: テスト
正常に機能していることを確認するには、Idp を使用して認証するように構成されたエンドポイントにサインインする必要があります。 これをテストするために、Azure に Web サイトを展開し、contososampapp.azurewebsites.net という URL を使用しています。

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>代替 ID でサインインできることを確認する
1. エンドポイントにサインインします。</br>
![エンドポイント](media/active-directory-aadconnect-guest-sync/guest10.png)
1. ユーザー名を入力すると、フェデレーション サインイン ページにリダイレクトされます。
![フェデレーション](media/active-directory-aadconnect-guest-sync/guest11.png)
1. 資格情報を入力します。
2. 今回はサインインに成功します。
![Success](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>次の手順
- [Azure Active Directory B2B コラボレーション ユーザーのプロパティ](../../active-directory/b2b/user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [代替ログイン ID を構成する](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect: バージョンのリリース履歴](active-directory-aadconnect-version-history.md)
