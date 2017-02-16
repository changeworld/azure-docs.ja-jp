---
title: "Azure AD Connect: シングル サインオン | Microsoft Docs"
description: "このトピックでは、オンプレミス Active Directory (AD) からクラウドベースの Azure Active Directory (Azure AD) と接続済みサービスにシングル サインオンを行うために必要な情報を取り上げます。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 44c5726d0bf621e463aa2f3ab061ed12d48418b0
ms.openlocfilehash: b36d639b5d6b18de3a8233f801cdd0abf3f25825


---

# <a name="what-is-single-sign-on-sso-preview"></a>シングル サインオン (SSO) とは (プレビュー)
シングル サインオンは、[パスワード ハッシュ同期](active-directory-aadconnectsync-implement-password-synchronization.md)または[パススルー認証](active-directory-aadconnect-pass-through-authentication.md)のいずれかを使用して有効にする、Azure Active Directory Connect のオプションです。  有効にすると、ユーザーが会社のコンピューターを使用して企業ネットワークに接続している場合は、ユーザー名を入力するだけで Azure Active Directory (Azure AD) やその他のクラウド サービスにサインインでき、パスワードを入力する必要はありません。

![シングル サインオン](./media/active-directory-aadconnect-sso/sso1.png)

エンド ユーザーに SSO を提供することで、クラウド ベース サービスへのアクセスがより分かりやすくなり、組織は、追加のオンプレミス コンポーネントが不要な、より安全で簡単なプロセスを利用できるようになります。

SSO は AAD Connect を通じて有効にする機能で、パスワード ハッシュ同期またはパス スルー認証と、オンプレミス Active Directory を使って利用します。  環境内でシングル サインオンを使用するエンド ユーザーには、次の要件があります。


- ドメインに参加したコンピューターを利用している
- 企業のワイヤードまたはワイヤレス ネットワークや、VPN 接続などのリモート アクセス接続経由で、ドメイン コントローラーに直接接続している
- ブラウザーのイントラネット ゾーンの一部としてクラウドに Kerberos エンドポイントを定義している

コンピューターが企業ネットワークに接続しておらず Active Directory を利用できないなど、これらの条件のいずれかを満たしていない場合は、シングル サインオンを使用していないときと同じようにパスワードの入力を求められます。

## <a name="supported-clients"></a>サポートされているクライアント
シングル サインオンは Web ブラウザー ベースのクライアントと、Windows などの Kerberos 認証に対応したコンピューターの[最新の認証](https://aka.ms/modernauthga)をサポートする Office クライアントでサポートされています。  次の表は、オペレーティング システム別のブラウザー ベースのクライアントの詳細を示しています。

| OS\ブラウザー |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- |--- |
|Windows 10|はい|はい|はい*|なし
|Windows 8.1|はい|はい|はい*|該当なし
|Windows 8|はい|はい|はい*|該当なし
|Windows 7|はい|はい|はい*|該当なし
|Mac|該当なし|該当なし|該当なし|該当なし

* 個別の構成が必要です。

>[!NOTE]
>Windows 10 ベースのクライアントで Azure AD を快適に利用するためには、[Azure AD 参加](../active-directory-azureadjoin-overview.md)の使用をお勧めします。

## <a name="how-single-sign-on-works"></a>シングル サインオンのしくみ

Azure AD Connect でシングル サインオンを有効にすると、オンプレミス Active Directory で AZUREADSSOACCT という名前のコンピューター アカウントが作成され、Kerberos の復号化キーが Azure AD と安全に共有されます。  さらに、クライアントと Azure AD の認証時に使用される、クラウド URL を表す&2; つの Kerberos サービス プリンシパル名 (SPN) が作成されます。

この設定が完了すると、統合 Windows 認証 (IWA) ベースのその他のアプリケーションと同じ方法で認証が行われます。  IWA の動作に慣れていれば、Azure AD でのシングル サインオンも同様に使用できます。  動作が分からない場合は、次の IWA の処理方法をご覧ください。

![シングル サインオン](./media/active-directory-aadconnect-sso/sso2.png)

最初に、ユーザーが SharePoint Online などの Azure AD から発行されたトークンを信頼するリソースへのアクセスを試みます。  SharePoint Online は、Azure AD で認証するユーザーをリダイレクトします。 ユーザーは、Azure AD が組織でシングル サインオンが有効であるかどうかを確立できるようにユーザー名を提供します。 組織でシングル サインオンが有効であると仮定した場合、次の操作が行われます。

1.  Azure AD は、Kerberos チケットを提供するよう、401 認証エラーを通じてクライアントに要求します。
2.  クライアントは、Active Directory から Azure AD 用のチケットを要求します。
3.  Active Directory は、Azure AD Connect によって作成されたコンピューター アカウントを検索し、コンピューター アカウントの秘密キーで暗号化された Kerberos チケットをクライアントに返します。 チケットには、コンピューターにサインインしているユーザーの ID が含まれます。
4.  クライアントは、Active Directory から取得した Kerberos チケットを Azure AD に送信します。
5.  Azure AD はこれまで共有していたキーを使用して Kerberos チケットを復号化し、ユーザーにトークンを返すか、リソースに必要な多要素認証などの他の認証情報を提供するように要求します。

シングル サインオンは便宜的な機能であり、何らかの理由で失敗した場合には、ログイン ページで通常通りパスワードの入力が求められます。

## <a name="enabling-sso-with-pass-through-authentication-or-password-hash-sync"></a>パススルー認証またはパスワード ハッシュ同期による SSO の有効化
Azure AD Connect では、パススルー認証またはパスワード ハッシュ同期を使って、シングル サインオンを簡単に有効にできます。  コンピューター アカウントで Kerberos サービス プリンシパル名 (SPN) の構成を許可するには、同期する各フォレスト内のいずれかのドメインに対するドメイン管理者権限が必要です。  ユーザー名とパスワードは Azure AD Connect にも Azure AD にも保存されず、この操作のみに使用されます。

Azure AD Connect をインストールするときにカスタム インストールを選択すると、ユーザーのサインイン ページでシングル サインオン オプションを選択できます。 詳細については、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」を参照してください。

![シングル サインオン](./media/active-directory-aadconnect-sso/sso3.png)

シングル サインオンを有効にすると、ウィザードを通じた操作だけで、シングル サインオン ページが利用できるようになります。

![シングル サインオン](./media/active-directory-aadconnect-sso/sso4.png)

表示されている各フォレストに適切なアカウントの詳細を指定すると、Azure ディレクトリでシングル サインオンが有効になります。

>[!NOTE]
>SSO を構成するには、Azure AD Connect がポート 9090 (TCP) で *.msappproxy.net と通信できる必要があります。 これは構成中にのみ必要であり、エンド ユーザーによる認証中には使用されません。

## <a name="ensuring-clients-sign-in-automatically"></a>クライアント サインインの自動化
既定では、URL がイントラネット ゾーン内のものとして定義されていない場合、ブラウザーは Web サーバーに資格情報を送信しません。  一般に、ブラウザーは URL を調べて、正しいゾーンを判断します。  たとえば、URL が http://intranet/ となっている場合は、URL がイントラネット ゾーンにマッピングされているため、ブラウザーは資格情報を自動的に送信します。  ただし、URL に http://intranet.contoso.com/ のようにピリオドが含まれている場合は、サーバーは資格情報を自動的に送信せず、インターネット サイトとのものとして URL を処理します。

Azure AD でのシングル サインオンに使用される URL はグローバルにルーティング可能なホスト名であることから、ピリオドが含まれます。そのため、ブラウザーが現在ログインしているユーザーの資格情報を Kerberos チケットの形式で Azure AD に送信するためには、コンピューターのイントラネット ゾーンに明示的に追加する必要があります。  Active Directory でグループ ポリシーを作成すると、必要な URL を簡単にイントラネット ゾーンに追加できます。

1.  グループ ポリシー管理ツールを開きます。
2.  すべてのユーザーに適用されるグループ ポリシーを編集します。  既定のドメイン ポリシーなどです。
3.  **[ユーザーの構成]、[管理用テンプレート]、[Windows コンポーネント]、[Internet Explorer]、[インターネット コントロール パネル]、[セキュリティ]** の順に移動して、**[サイトとゾーンの割り当て一覧]** を選択します。
![シングル サインオン](./media/active-directory-aadconnect-sso/sso6.png) </br>
4.  ポリシーを有効にし、ダイアログ ボックスで次の値とデータを入力します。</br>

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1'
5.  入力すると次のようになります。![シングル サインオン](./media/active-directory-aadconnect-sso/sso7.png)

6.  [OK] をクリックし、もう一度 [OK] をクリックします。

これで、シングル サインオンを使用する準備が整いました。

>[!NOTE]
>既定では、Chrome は Internet Explorer と同じ信頼済みサイトの URL セットを使用します。  Chrome に異なる設定を構成している場合は、これらを個別に更新する必要があります。

## <a name="troubleshooting-single-sign-on-issues"></a>シングル サインオンの問題のトラブルシューティング
シングル サインオンを行うクライアントが、次のように正しく構成されているかどうかをご確認ください。

1.  https://autologon.microsoftazuread-sso.com と https://aadg.windows.net.nsatc.net の両方がイントラネット ゾーン内に定義されている
2.  ワークステーションがドメインに参加している
3.  ユーザーがドメイン アカウントを使用してログオンしている
4.  コンピューターが企業ネットワークに接続されている
5.  コンピューターの時刻が Active Directory と同期されており、ドメイン コントローラーの時刻と正しい時刻とのずれが 5 分以内になっている
6.  クライアントの既存の Kerberos チケットを消去します。たとえば、コマンド プロンプトから "klist purge" コマンドを実行します。

これらの要件が満たされていることが確認できたら、ブラウザーのコンソール ログで追加情報を確認します。  コンソール ログは開発者ツールにあり、  潜在的な問題の特定に利用できます。

## <a name="event-log-entries"></a>イベント ログ エントリ
成功の監査が有効になっていると、ユーザーがシングル サインオンを使用してログインするたびに、ドメイン コント ローラーのイベント ログにエントリが記録されます。  これらのイベントは、コンピューター アカウント AzureADSSOAcc$ に関連付けられているセキュリティ イベント 4769 のイベント ログで確認できます。  次のフィルターでは、コンピューター アカウントに関連付けられているすべてのセキュリティ イベントを検索できます。

    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>



<!--HONumber=Jan17_HO1-->


