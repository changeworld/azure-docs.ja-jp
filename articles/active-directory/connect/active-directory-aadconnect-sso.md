---
title: "Azure AD Connect: シームレス シングル サインオン | Microsoft Docs"
description: "このトピックでは、Azure Active Directory (Azure AD) シームレス シングル サインオンについて説明します。この機能により、企業ネットワーク内の企業のデスクトップ ユーザーに真のシングル サインオンを提供できます。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 294a7b7de5c0a95f9f0784f315f202ae2c062e57
ms.lasthandoff: 04/22/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオン

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオンとは

Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) は、企業ネットワークに接続された会社のコンピューターでサインインするユーザーに真のシングル サインオンを提供します。 この機能を有効にすると、ユーザーは Azure AD にサインインするためにパスワードを入力する必要はなくなります。また、ほとんどの場合、ユーザー名を入力する必要もありません。 この機能により、追加のオンプレミス コンポーネントを必要とせずに、ユーザーはクラウド ベースのサービスに簡単にアクセスできるようになります。

シームレス SSO は、Azure AD Connect を使用して有効にすることができ、[パスワード同期](active-directory-aadconnectsync-implement-password-synchronization.md)または[パススルー認証](active-directory-aadconnect-pass-through-authentication.md)と組み合わせることができます。

>[!NOTE]
>この機能は、Active Directory フェデレーション サービス (ADFS) には適用されません。ADFS はこの機能を既に備えています。

特定のユーザーに対してこの機能を有効にするには、次の条件を満たす必要があります。

- ユーザーが会社のデスクトップでサインインしている。
- デスクトップが Active Directory (AD) ドメインに既に参加している。
- デスクトップが、企業のワイヤードまたはワイヤレス ネットワーク上や、VPN 接続などのリモート アクセス接続経由でドメイン コントローラー (DC) に直接接続している。
- サービス エンドポイントがブラウザーのイントラネット ゾーンに含まれている。

上記のいずれかの条件が満たされていない場合、ユーザーはこれまでと同様にユーザー名とパスワードの入力を求められます。

![シームレス シングル サインオン](./media/active-directory-aadconnect-sso/sso1.png)

## <a name="whats-available-during-preview"></a>プレビュー期間中に利用できるもの

>[!NOTE]
>Azure AD シームレス SSO は現在プレビュー段階です。 これは無料の機能であり、この機能を使用するために Azure AD の有料エディションは不要です。

シームレス SSO は、Web ブラウザー ベースのクライアントと、Kerberos 認証に対応したデスクトップ (Windows ベースのコンピューターなど) での[最新の認証](https://aka.ms/modernauthga)をサポートする Office クライアントでサポートされています。 次の表は、オペレーティング システム別のブラウザー ベースのクライアントの詳細を示しています。

| OS\ブラウザー |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|はい|はい|はい*|なし
|Windows 8.1|はい|はい|はい*|該当なし
|Windows 8|はい|はい|はい*|該当なし
|Windows 7|はい|はい|はい*|該当なし
|Mac|該当なし|いいえ|いいえ|該当なし

\*追加構成が必要です。

>[!NOTE]
>Windows 10 の場合、Azure AD で最適なエクスペリエンスを実現するために、[Azure AD Join](../active-directory-azureadjoin-overview.md) を使用することをお勧めします。

## <a name="how-does-azure-ad-seamless-sso-work"></a>Azure AD シームレス SSO のしくみ

[後述](#how-to-enable-azure-ad-seamless-sso?)するように、シームレス SSO は Azure AD Connect で有効にすることができます。 シームレス SSO を有効にすると、オンプレミス Active Directory (AD) に AZUREADSSOACCT という名前のコンピューター アカウントが作成され、Kerberos の復号化キーが Azure AD と安全に共有されます。 また、Azure AD のサインイン時に使用される 2 つのサービス URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) も作成されます。

このセットアップが完了すると、Azure AD サインインは、統合 Windows 認証 (IWA) を使用するその他のサインインと同様に機能します。 シームレス SSO プロセスは次のように機能します。

ユーザーが、Azure AD によって保護されたクラウド ベースのリソース (SharePoint Online など) にアクセスしようとしているとします。 SharePoint Online は、サインインのためにユーザーのブラウザーを Azure AD にリダイレクトします。

- Azure AD に対するサインイン要求に、`domain_hint` パラメーター (Azure AD テナント (例: contoso.onmicrosoft.com) を識別) または `login_hint` パラメーター (ユーザーのユーザー名 (例: user@contoso.onmicrosoft.com 、user@contoso.com) を識別) が含まれている場合は、次の手順が発生します。
- Azure AD サインイン要求にどちらのパラメーターも含まれていない場合、ユーザーはユーザー名の入力を求められ、その後に次の手順が発生します。

1. Azure AD は、Kerberos チケットを提供するよう、401 認証エラーを通じてクライアントに要求します。
2. クライアントは、Active Directory から (以前にセットアップされたコンピューター アカウントで表される) Azure AD 用のチケットを要求します。
3. Active Directory はコンピューター アカウントを検索し、コンピューター アカウントのシークレットで暗号化された Kerberos チケットをクライアントに返します。 このチケットには、デスクトップに現在サインインしているユーザーの ID が含まれます。
4. クライアントは、Active Directory から取得した Kerberos チケットを Azure AD に送信します。
5. Azure AD は、以前の共有キーを使用して Kerberos チケットを復号化します。 正常に復号化されると、Azure AD はトークンを返すか、リソースで必要とされる多要素認証などの他の検査を実行するようユーザーに要求します。

シームレス SSO は便宜的な機能であり、何らかの理由で失敗しても、ユーザーがこれまでと同様にサインイン ページでパスワードを入力すれば済みます。

このプロセス全体を次の図に示します。

![シームレス シングル サインオン](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>Azure AD シームレス SSO を有効にする方法

### <a name="pre-requisites"></a>前提条件

パススルー認証でシームレス SSO を有効にする場合、パススルー認証機能の要件以外に追加の前提条件はありません。

パスワード同期でシームレス SSO を有効にする場合や、Azure AD Connect と Azure AD の間にファイアウォールがある場合は、次の点を確認してください。

- Azure AD Connect サーバーが `*.msappproxy.net` と通信できる。
- Azure AD Connect (バージョン 1.1.484.0 以上) が、Azure AD に対する HTTPS 要求をポート 443 経由で実行できる。 これは機能の有効化にのみ使用されます。実際のユーザー サインインには使用されません。

>[!NOTE]
> 古いバージョンの Azure AD Connect (1.1.484.0 より前) は、ポート 9090 経由で Azure AD と通信できる必要があります。

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>Azure AD シームレス SSO 機能の有効化

Azure AD シームレス SSO は、Azure AD Connect を使用して有効にすることができます。

Azure AD Connect の新しいインストールを実行する場合は、[カスタム インストール パス](active-directory-aadconnect-get-started-custom.md)を選択します。 [ユーザー サインイン] ページで、[シングル サインオンを有効にする] チェック ボックスをオンにします。

![Azure AD Connect - [ユーザー サインイン]](./media/active-directory-aadconnect-sso/sso8.png)

Azure AD Connect を既にインストールしている場合は、[高速インストール](active-directory-aadconnect-get-started-express.md) パスまたは[カスタム インストール](active-directory-aadconnect-get-started-custom.md) パスを使用してセットアップし、Azure AD Connect で [ユーザー サインインの変更] を選択して、[次へ] をクリックします。 次に、[シングル サインオンを有効にする] チェック ボックスをオンにします。

![Azure AD Connect - [ユーザー サインインの変更]](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

[シングル サインオンを有効にする] ページが表示されるまで、インストール ウィザードの手順を続行します。 (Azure AD Connect を使用して) Azure AD と同期する各 AD フォレストとシームレス SSO を有効にするユーザーに、ドメイン管理者の資格情報を提供する必要があります。 ドメイン管理者の資格情報は Azure AD Connect にも Azure AD にも保存されませんが、前述のコンピューター アカウントの作成と Kerberos SPN の構成にのみ使用されます。

この時点で、シームレス SSO がテナントで有効になります。 ユーザーがこの機能のメリットを享受できるようにするには、引き続き次のセクションの手順を実行する必要があります。

## <a name="rolling-the-feature-out-to-your-users"></a>ユーザーへの機能のロールアウト

既定では、クラウド エンドポイントの URL がブラウザーのイントラネット ゾーンの一部として定義されていない場合、ブラウザーは Kerberos チケットをクラウド エンドポイントに送信しません。 ブラウザーは、URL から適切なゾーン (インターネットまたはイントラネット) を自動的に判断します。 たとえば、http://contoso/ はイントラネット ゾーンにマップされ、http://intranet.contoso.com/ はインターネット ゾーンにマップされます (URL にピリオドが含まれているため)。

Azure AD でのシームレス SSO に使用するサービス URL にはピリオドが含まれるため、各ユーザーのブラウザーのイントラネット ゾーン設定にこれらの URL を明示的に追加する必要があります。 これにより、ブラウザーは現在ログインしているユーザーの Kerberos チケットを Azure AD に自動的に送信するようになります。 URL の追加はコンピューターごとに手動で行うこともできますが、すべてのユーザーを対象に必要な URL をイントラネット ゾーンに追加する最も簡単な方法は、Active Directory でグループ ポリシーを作成することです。

1. グループ ポリシー管理ツールを開きます。
2. **既定のドメイン ポリシー**など、すべてのユーザーに適用されるグループ ポリシーを編集します。
3. **[ユーザーの構成]、[管理用テンプレート]、[Windows コンポーネント]、[Internet Explorer]、[インターネット コントロール パネル]、[セキュリティ]** の順に移動して、**[サイトとゾーンの割り当て一覧]** を選択します。
![シングル サインオン](./media/active-directory-aadconnect-sso/sso6.png)  
4. ポリシーを有効にし、ダイアログ ボックスで次の値とデータを入力します。 これらは、Kerberos チケットの送信先となる Azure AD URL です。

        Value: https://autologon.microsoftazuread-sso.com  
        Data: 1  
        Value: https://aadg.windows.net.nsatc.net  
        Data: 1  
5. **[OK]** をクリックし、もう一度 **[OK]** をクリックします。

次のようになります。![シングル サインオン](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>既定では、Chrome は Internet Explorer と同じ信頼済みサイトの URL セットを使用します。 Chrome に別の設定を構成している場合は、それらの設定を個別に更新する必要があります。

## <a name="troubleshooting-seamless-sso"></a>シームレス SSO のトラブルシューティング

シームレス SSO のトラブルシューティングを行う場合は、次のチェックリストを使用します。

1. Azure AD Connect ツールで、シームレス SSO 機能がテナントで有効になっているかどうかを確認します。 (ポートのブロックなどが原因で) この機能を有効にすることができない場合は、すべての[前提条件](#pre-requisites)を満たしていることを確認します。 機能の有効化に関する問題が引き続き発生する場合は、Microsoft サポートに連絡してください。
2. 両方のサービス URL (https://autologon.microsoftazuread-sso.com と https://aadg.windows.net.nsatc.net) がイントラネット ゾーン設定の一部として定義されていることを確認します。
3. 会社のデスクトップが AD ドメインに参加していることを確認します。
4. ユーザーが AD ドメイン アカウントを使用してデスクトップにログオンしていることを確認します。
5. デスクトップが企業ネットワークに接続されていることを確認します。
6. デスクトップの時刻が Active Directory の時刻およびドメイン コントローラーの時刻と同期されており、時刻のずれが 5 分以内であることを確認します。
7. デスクトップから既存の Kerberos チケットを消去します。 これを行うには、コマンド プロンプトで **klist purge** コマンドを実行します。
8. 潜在的な問題の特定に役立つ、ブラウザーのコンソール ログ ([開発者ツール] の下) を確認します。

### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

ドメイン コントローラーで成功の監査が有効になっている場合、ユーザーがシームレス SSO を使用してサインインするたびに、セキュリティ エントリ (コンピューター アカウント **AzureADSSOAcc$** に関連付けられたイベント 4769) がイベント ログに記録されます。 これらのセキュリティ イベントは、次のクエリを使用して検索できます。

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

