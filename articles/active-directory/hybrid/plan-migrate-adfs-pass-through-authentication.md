---
title: Azure AD Connect:Azure AD でフェデレーションからパススルー認証に移行する | Microsoft Docs
description: お客様のハイブリッド ID 環境をフェデレーションからパススルー認証に移行する方法についての情報。
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c7d236769d5e9adca0402affc2d0eccdf78a6837
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107754"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>Azure AD でフェデレーションからパススルー認証に移行する
次のドキュメントでは、AD FS からパススルー認証への移行についてのガイダンスを提供します。

>[!NOTE]
>このドキュメントのダウンロード可能なコピーは、[こちら](https://aka.ms/ADFSTOPTADPDownload)で入手できます。

## <a name="prerequisites-for-pass-through-authentication"></a>パススルー認証の前提条件
移行するには、以下の前提条件を満たす必要があります。
### <a name="update-azure-ad-connect"></a>Azure AD Connect を更新する

パススルー認証に移行する手順を正常に実行するには、最低でも [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 が必要です。 このバージョンでは、サインイン変換の実行方法が大幅に変更されており、フェデレーションからクラウド認証への移行にかかる時間全体が数時間から数分に短縮できる可能性があります。

> [!IMPORTANT]
> 古いドキュメント、ツール、およびブログでは、ドメインをフェデレーションからマネージドに変換する際にユーザーを変換する手順が必要であると記載されています。 **ユーザーの変換**は必要なくなり、Microsoft ではそのことを反映するようドキュメントやツールを更新中です。

Azure AD Connect を最新バージョンに更新するには、この[更新手順](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)に従ってください。

### <a name="plan-authentication-agent-number-and-placement"></a>認証エージェントの数と配置を計画する

パススルー認証は、Azure AD Connect サーバーとお客様のオンプレミス Windows サーバーに軽量のエージェントをデプロイすることによって実現されます。 待ち時間を短縮するために、お客様の Active Directory ドメイン コントローラーのできるだけ近くにエージェントをインストールします。

ほとんどのお客様の場合、高可用性と容量を確保するには 2 つか 3 つの認証エージェントで十分です。また、テナントに登録できるエージェントは 12 個以下です。 最初のエージェントは、常に AAD Connect サーバー自体にインストールされます。 エージェントの制限とエージェントのデプロイ オプションを理解するには、[ネットワーク トラフィックの見積もりとパフォーマンスのガイダンスの情報](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations)を参照してください。

### <a name="plan-migration-method"></a>移行方法を計画する

フェデレーション認証から PTA とシームレス SSO に移行するには、2 つの方法があります。 使用する方法は、お客様が最初に AD FS をどのように構成したかによって決まります。

- **Azure AD Connect を使用する**。 AD FS を最初に構成したときに Azure AD Connect を使用した場合、パススルー認証への変更は、Azure AD Connect ウィザードを通じて行う "*必要があります*"。

Azure AD Connect を使用している場合は、ユーザー サインイン方法を変更するときに Set-MsolDomainAuthentication コマンドレットが自動的に実行されるため、それによってお客様の Azure AD テナント内にあるすべての確認済みフェデレーション ドメインがフェデレーション解除されるときに、何も制御できません。  
‎  
> [!NOTE]
> AD FS の構成に最初は AAD Connect が使用され、ユーザー サインインをパススルー認証に変更する場合、この時点で、お客様のテナントに含まれているすべてのドメインがフェデレーション解除されることは避けられません。  
‎
- **Azure AD Connect と PowerShell を使用する**。 この方法は、AD FS が最初に Azure AD Connect で構成されていない場合にのみ使用できます。 ここでも Azure AD Connect ウィザードを使用してユーザー サインイン方法を変更する必要がありますが、主な違いは、それがお客様の AD FS ファームを認識しないため、Set-MsolDomainAuthentication コマンドレットが自動的に実行されないことです。そのため、どのドメインをどの順序で変換するかを完全に制御できます。

どの方法を使用すればよいかを理解するには、次のセクションの手順を実行します。

#### <a name="verify-current-user-sign-in-settings"></a>現在のユーザー サインイン設定を確認する

グローバル管理者アカウントで Azure AD ポータル [https://aad.portal.azure.com](https://aad.portal.azure.com/) にログインして、お客様の現在のユーザー サインイン設定を確認します。 

**[ユーザーのサインイン]** セクションで、**[フェデレーション]** が **[有効]** になっていることと、**[シームレス シングル サインオン]** および **[パススルー認証]** が **[無効]** になっていることを確認します。 

![画像 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>フェデレーションが構成された方法を確認する

   1. Azure AD Connect サーバーに移動し、Azure AD Connect を起動して、**[構成]** を選択します。
   2. **[追加のタスク]** 画面で **[現在の構成を表示する]** を選択し、**[次へ]** を選択します。</br>
   ![画像 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. **[ソリューションのレビュー]** 画面で、**[Active Directory Federation Services (AD FS)]** まで下へスクロールします。</br>
   AD FS 構成がこのセクションに表示される場合は、AD FS が最初に Azure AD Connect を通じて構成されたと見なすことができます。そのため、フェデレーションからマネージドへのドメインの変換は、Azure AD Connect の **[ユーザー サインインの変更]** オプションを介して実行できます。このプロセスの詳細については、**オプション 1: Azure AD Connect を使用したパススルー認証の構成**に関するセクションで説明しています。  
‎
   4. Active Directory フェデレーション サービスが現在の設定の一覧に表示されない場合は、PowerShell を使用してドメインをフェデレーションからマネージドに手動で変換する必要があります。その詳細については、**オプション 2 - Azure AD Connect と PowerShell を使用したフェデレーションから PTA への切り替え**に関するセクションで説明しています。

### <a name="document-current-federation-settings"></a>現在のフェデレーション設定をドキュメント化する

現在のフェデレーション設定を確認するには、Get-MsolDomainFederationSettings コマンドレットを実行します。

コマンドは、次のとおりです。

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

例: 

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```


お客様のフェデレーションの設計とデプロイのドキュメント用にカスタマイズした可能性のあるすべての設定 (特に **PreferredAuthenticationProtocol**、**SupportsMfa**、および **PromptLoginBehavior**) を確認します。

これらの設定による動作の詳細については、以下を参照してください。

[Active Directory フェデレーション サービスの prompt=login パラメーターのサポート](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> SupportsMfa 値が現在 "True" に設定されている場合は、第 2 要素チャレンジをユーザー認証フローに挿入するためにオンプレミス MFA ソリューションを使用していることを意味します。 これは、Azure AD の認証シナリオでは動作しなくなります。代わりに、同じ機能を実行するために、Azure MFA (クラウド ベース) サービスを利用する必要があります。 先に進む前にお客様の MFA 要件を慎重に評価し、お客様のドメインを変換する前に Azure MFA、ライセンスの関連事項、およびエンド ユーザーの登録プロセスをどのように利用するかを理解してください。

#### <a name="backup-federation-settings"></a>フェデレーション設定をバックアップする

このプロセス中に AD FS ファーム上の他の証明書利用者への変更は行われませんが、復元できるように AD FS ファームの現在の有効なバックアップを確保しておくことをお勧めします。 そうするには、無料の Microsoft [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) を使用できます。 このツールを使用すると、AD FS をバックアップし、既存のファームまたは新しいファームに復元できます。

AD FS Rapid Restore Tool を使用しない場合は、少なくとも、"Microsoft Office 365 ID プラットフォーム" の証明書利用者信頼と、お客様が追加した可能性があるすべての関連カスタム要求規則をエクスポートする必要があります。 これは、次の PowerShell の例を使用して実行できます

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>デプロイに関する考慮事項と AD FS の使用状況

### <a name="validate-your-current-ad-fs-usage"></a>現在の AD FS の使用状況を確認する

フェデレーションからマネージドに変換する前に、Azure AD や Office 365、その他のアプリケーション (証明書利用者信頼) のために AD FS を現時点でどのように使用しているかを詳しく調べる必要があります。 具体的には、次の表を検討する必要があります。

| 状況| 対処 |
|-|-|
| それらの他のアプリケーションのために AD FS を保持しようとしている。| AD FS と Azure AD の両方を使用することになるため、エンドユーザー エクスペリエンスを検討する必要があります。 ユーザーは、いくつかのシナリオで認証が 2 回必要になる可能性があります。1 回は Azure AD に対するもので (これによって、以降の Office 365 などの他のアプリケーションに対する SSO を取得します)、もう 1 回は証明書利用者信頼として AD FS にまだバインドされているすべてのアプリケーションに対するものです。 |
| AD FS が大幅にカスタマイズされていて、Azure AD に複製できない onload.js ファイル内の特定のカスタマイズ設定に依存している (たとえば、ユーザーがユーザー名を UPN ではなく SamAccountName 形式でのみ入力するようにサインイン エクスペリエンスを変更したり、大幅にブランド化されたログイン エクスペリエンスにしたりしている)| 続行する前に、Azure AD が現在のカスタマイズ要件を満たせることを確認する必要があります。 詳細情報とガイダンスについては、AD FS のブランド化と AD FS のカスタマイズのセクションを参照してください。|
| AD FS を介してレガシ認証クライアントをブロックしている。| レガシ認証クライアントをブロックするために現在 AD FS にある制御を、[レガシ認証用の条件付きアクセス制御](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)と [Exchange Online のクライアント アクセス規則](https://aka.ms/EXOCAR)の組み合わせに置き換えることを検討します。 |
| ユーザーに、AD FS への認証時にオンプレミスの MFA サーバー ソリューションに対する MFA を行うことを要求している。| オンプレミス MFA ソリューションを介してマネージド ドメインの認証フローに MFA チャレンジを挿入することはできませんが、Azure MFA サービスを使用して、ドメインの変換以降はそのようにすることができます。 現在、ユーザーが Azure MFA を使用していない場合は、1 回限りのエンドユーザー登録手順が必要になります。これには、お客様による準備とエンド ユーザーへの連絡が必要です。 |
| 現在、Office 365 へのアクセスを制御するために AD FS でアクセス制御ポリシー (AuthZ 規則) を使用している。| これらを同等の Azure AD [条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)と [Exchange Online のクライアント アクセス規則](https://aka.ms/EXOCAR)に置き換えることを検討します。|

### <a name="considerations-for-common-ad-fs-customizations"></a>一般的な AD FS のカスタマイズに関する考慮事項

#### <a name="inside-corporate-network-claim"></a>企業ネットワーク内要求

InsideCorporateNetwork 要求は、ユーザー認証が企業ネットワーク内である場合に、AD FS によって発行されます。 その後、この要求は Azure AD に渡され、ユーザーのネットワークの場所に基づいて多要素認証をバイパスするために使用されます。 現在 AD FS でこれが有効になっているかどうかを判断する方法については、[フェデレーション ユーザー用の信頼できる IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) に関するページを参照してください。

InsideCorporateNetwork 要求は、お客様のドメインがパススルー認証に変換された後は使用できなくなります。 この機能の代わりに、[Azure AD の名前付きの場所](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)を使用できます。

名前付きの場所を構成した後は、ネットワークの場所 "すべての信頼できる場所" または "MFA の信頼できる IP" を含めるか除外するために構成したすべての条件付きアクセス ポリシーを、新たに作成された名前付きの場所を反映するように更新する必要があります。

条件付きアクセスでの場所の条件の詳細については、[Active Directory の条件付きアクセスの場所](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)に関するページを参照してください。

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD 参加済みデバイス

デバイスを Azure AD に参加させると、セキュリティとコンプライアンスのためのアクセス基準に従うことをデバイスに強制する条件付きアクセス規則を作成できるようになり、個人用アカウントでなく組織の職場または学校アカウントを使用してデバイスにサインインすることをユーザーに許可できます。 Hybrid Azure AD 参加済みデバイスでは、お客様の AD ドメイン参加済みデバイスを Azure AD に参加させることができます。 お客様のフェデレーション環境は、この機能を使用して構成されている可能性があります。

お客様のドメインがパススルー認証に変換された後も、ドメインに参加したすべての新しいデバイスでハイブリッド参加が機能し続けるようにするには、Windows 10 クライアントの Active Directory コンピューター アカウントを Azure AD に同期するように Azure AD Connect を構成する必要があります。 Windows 7 および Windows 8 のコンピューター アカウントの場合、ハイブリッド参加ではコンピューターを Azure AD に登録するためにシームレス SSO が使用されるため、Windows 10 デバイスの場合のようにそれらを同期する必要はありません。 ただし、更新された workplacejoin.exe ファイルを (.msi を通じて) これらの下位レベルのクライアントにデプロイする必要があります。そうすることで、それらはシームレス SSO を使用してそれら自体を登録することができます。 [.msi をダウンロード](https://www.microsoft.com/download/details.aspx?id=53554)します。

この要件の詳細については、[ハイブリッド Azure Active Directory 参加済みデバイスの構成方法](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)に関するページを参照してください。

#### <a name="branding"></a>ブランド

お客様の組織は、より関連性の高い情報を表示するために、[ADFS サインイン ページをカスタマイズ](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)している場合があります。 その場合は、同様に [Azure AD サインイン ページをカスタマイズする](https://docs.microsoft.com/azure/active-directory/customize-branding)ことを検討してください。

同様のカスタマイズを行うことができますが、ビジュアル面でいくつかの変更が予想されます。 エンド ユーザーへの連絡に、予想される変更を含めた方がよい場合もあります。

> [!NOTE]
> 会社のブランドを使用できるのは、Azure AD の Premium ライセンスまたは Basic ライセンスを購入した場合、あるいは Office 365 のライセンスを所有している場合だけです。

## <a name="planning-for-smart-lockout"></a>スマート ロックアウトの計画

Azure AD スマート ロックアウトを使用すると、ブルートフォース パスワード攻撃を防御できるほか、パススルー認証が使用されていて、Active Directory にアカウント ロックアウト グループ ポリシーが設定されている場合に、オンプレミスの Active Directory アカウントがロックアウトされるのを防ぐことができます。 

詳細については、[スマート ロックアウト機能とその構成を編集する方法](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)に関するページを参照してください。

## <a name="planning-deployment-and-support"></a>デプロイとサポートの計画

### <a name="plan-the-maintenance-window"></a>メンテナンス期間の計画

ドメイン変換プロセス自体は比較的高速ですが、Azure AD はドメイン変換の完了後も最大 4 時間にわたって、何らかの認証要求をお客様の AD FS サーバーに送信する可能性があります。 この 4 時間の枠内では、さまざまなサービス側のキャッシュによっては、これらの認証が Azure AD によって受け入れられない可能性があります。ユーザーはまだ AD FS に対して正常に認証できるため、エラーを受け取りますが、フェデレーションの信頼が削除されるため、Azure AD はユーザーの発行したトークンを受け入れなくなります。

> [!NOTE]
> この影響を受けるのは、この変換後の時間枠内で、サービス側のキャッシュがクリアされるまでにブラウザーを介してサービスにアクセスするユーザーだけです。 レガシ クライアント (Exchange ActiveSync、Outlook 2010 または 2013) は、影響を受けません。Exchange Online は、一定の期間、それらの資格情報のキャッシュを保持し、それがユーザーを AD FS に戻らせることなく自動的に再認証するために使用されるからです。 これらのクライアントのためにデバイスに格納されている資格情報は、このキャッシュがクリアされた後、それ自体を自動的に再認証するために使用されます。そのため、ドメイン変換プロセスの結果としてパスワードの入力を求めるメッセージがユーザーに表示されることはありません。 逆に、先進認証クライアント (Office 2013 または 2016、IOS、および Android アプリ) では、リソースへのアクセスを継続するための新しいアクセス トークンを取得するために、AD FS に戻るのではなく、有効な更新トークンが使用されます。そのため、ドメイン変換プロセスの結果としてパスワードの入力を求めるメッセージを表示する必要がなく、追加の構成を行わなくても機能し続けます。
> [!IMPORTANT]
> すべてのユーザーがクラウド認証を使用して正常に認証されることを確認するまで、お客様の AD FS 環境をシャットダウンしたり、Office 365 証明書利用者信頼を削除したりしないでください。

### <a name="plan-for-rollback"></a>ロールバックのための計画

重大な問題が見つかり、すぐに解決できない場合は、ソリューションをフェデレーションにロールバックする可能性もあります。 お客様のデプロイが想定どおりに行われない場合にどうするかを計画することは重要です。 デプロイ中にドメインまたはユーザーの変換が失敗した場合、またはフェデレーションにロールバックする必要がある場合は、停止時間を短縮してお客様のユーザーへの影響を軽減する方法を理解する必要があります。

#### <a name="rolling-back"></a>ロールバック

特定のデプロイの詳細については、フェデレーションの設計とデプロイに関するドキュメントを参照してください。 プロセスには以下が含まれる必要があります。

* Convert-MSOLDomainToFederated を使用してマネージド ドメインをフェデレーションに変換する 

* 必要な場合は、追加の要求規則を構成する。

### <a name="plan-change-communications"></a>変更の連絡の計画

デプロイとサポートの計画で重要な部分は、変更点、何が起きるか、何を行う必要があるかについて、お客様のエンド ユーザーに事前に通知することです。 

パススルー認証とシームレス SSO の両方がデプロイされた後、エンドユーザーのサインイン エクスペリエンスは、Azure AD を通じて認証される Office 365 およびその他の関連リソースにアクセスするときに変更されます。 ネットワークの外部のユーザーには、Azure AD サインイン ページのみが表示されるようになりました。外部に接続する Web アプリケーション プロキシ サーバーによって表示されるフォームベースのページにはリダイレクトされません。

連絡に関する戦略の計画には、複数の要素があります。 チェックの内容は次のとおりです

* 以下の手段で、近日公開される機能とリリース済みの機能をユーザーに通知する
  * 電子メールおよびその他の内部通信チャネル
  * ポスターなどのビジュアル
  * 経営幹部による直接の連絡またはその他の連絡
* だれがいつカスタマイズし、連絡を送るかを決める。

## <a name="implementing-your-solution"></a>ソリューションの実装

これでお客様のソリューションの計画が済んだので、実装の準備ができました。 実装には次の要素が含まれます。

   1. シームレス シングル サインオンの準備をする
   2. サインイン方法をパススルー認証に変更し、シームレス SSO を有効にする

## <a name="step-1--prepare-for-seamless-sso"></a>手順 1 – シームレス SSO の準備をする

お客様のデバイスにシームレス SSO を使用するには、Active Directory のグループ ポリシーを使用して、ユーザーのイントラネット ゾーンの設定に Azure AD URL を追加する必要があります。

既定では、ブラウザーは指定 URL から適切なゾーン (インターネットまたはイントラネット) を自動的に判断します。 たとえば、"http://contoso/" はイントラネット ゾーンにマップされ、"http://intranet.contoso.com/" はインターネット ゾーンにマップされます (URL にピリオドが含まれているため)。 Azure AD URL と同様に、クラウド エンドポイントの URL をブラウザーのイントラネット ゾーンに明示的に追加しなければ、ブラウザーは Kerberos チケットをクラウド エンドポイントに送信しません。

必要な変更をお客様のデバイスに[ロールアウトする手順](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)に従ってください。

> [!IMPORTANT]
> この変更を行っても、お客様のユーザーが Azure AD にサインインする方法は変わりません。 ただし、手順 3. に進む前に、この構成がお客様のすべてのデバイスに適用されていることが重要です。また、この構成を受け取っていないデバイスでサインインしているユーザーは、Azure AD にサインインするために、ユーザー名とパスワードを入力するだけでよいということにも注意してください。

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>手順 2 - サインイン方法を PTA に変更し、シームレス SSO を有効にする

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>オプション A: Azure AD Connect を使用した PTA の構成

お客様の AD FS が最初に Azure AD Connect を使用して構成された場合は、この方法を使用します。 お客様の AD FS が最初に Azure AD Connect を使用して構成されていない場合は、この方法を使用できません。

> [!IMPORTANT]
> 以下の手順に従うと、お客様のすべてのドメインがフェデレーションからマネージドに変換されることに注意してください。 詳細については、「[移行方法を計画する](#_Plan_Migration_Method)」のセクションを参照してください。

まず、サインオン方法を変更する必要があります。

   1. Azure AD Connect サーバーで、ウィザードを開きます。
   2. **[ユーザー サインインの変更]** を選択し、**[次へ]** を選択します。 
   3. **[Azure AD に接続]** 画面で、グローバル管理者のユーザー名とパスワードを指定します。
   4. **[ユーザー サインイン]** 画面でオプション ボタンを **[AD FS とのフェデレーション]** から **[パススルー認証]** に変更し、**[シングル サインオンを有効にする]** をオンにして、**[次へ]** を選択します。
   5. [シングル サインオンを有効にする] 画面で、ドメイン管理者アカウントの資格情報を入力し、[次へ] を選択します。  

   > [!NOTE]
   > シームレス シングル サインオンを有効にするには、ドメイン管理者の資格情報が必要です。このプロセスでは、管理者特権のアクセス許可を必要とする以下のアクションが実行されるためです。 ドメイン管理者の資格情報は Azure AD Connect にも Azure AD にも保存されません。 それらは機能を有効にするためだけに使用され、正常に完了した後は破棄されます
   >
   > * お客様のオンプレミスの Active Directory (AD) に AZUREADSSOACC という名前のコンピューター アカウント (Azure AD を表す) が作成されます。
   > * コンピューター アカウントの Kerberos の復号化キーは、Azure AD と安全に共有されます。
   > * また、Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) も作成されます。
   > * ドメイン管理者の資格情報は Azure AD Connect にも Azure AD にも保存されません。 それらは機能を有効にするためだけに使用され、正常に完了した後は破棄されます

   6. **[構成の準備完了]** 画面で **[Start Synchronization process when configuration completes]\(構成が完了したら同期処理を開始する\)** チェック ボックスがオンになっていることを確認します。 次に、**[構成]** を選択します。</br>
   ![画像](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. **Azure AD ポータル**を開き、**[Azure Active Directory]** を選択し、**[Azure AD Connect]** を選択します。
   8. **[フェデレーション] が [無効]** になっており、**[シームレス シングル サインオン]** と **[パススルー認証]** が **[有効]** になっていることを確認します。</br>
   ![画像](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

次に、追加の認証方法をデプロイする必要があります。 **Azure portal** を開き、**[Azure Active Directory]、[Azure AD Connect]** の順に移動して、**[パススルー認証]** をクリックします。

**[パススルー認証]** ページで、[ダウンロード] ボタンをクリックします。 **[エージェントのダウンロード]** 画面で、**[Accept terms and download]\(利用規約に同意してダウンロード\)** をクリックします。

追加の認証エージェントのダウンロードが開始されます。 セカンダリ認証エージェントは、ドメイン参加済みサーバーにインストールします。 

> [!NOTE]
> 最初のエージェントは、Azure AD Connect ツールの [ユーザー サインイン] セクションで行われた構成変更の一部として、常に Azure AD Connect サーバー自体にインストールされます。 追加の認証エージェントは、別のサーバーにインストールされる必要があります。 2 個から 3 個の追加の認証エージェントを使用できるようにすることをお勧めします。 

認証エージェントのインストールを実行します。 インストール中に、**グローバル管理者**アカウントの資格情報を指定する必要があります。

![画像 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![画像 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

認証エージェントがインストールされたら、パススルー認証エージェントの正常性のページに戻って、追加のエージェントの状態を確認できます。


「テストと次のステップ」に進みます。

> [!IMPORTANT]
> オプション B: Azure AD Connect と PowerShell を使用したフェデレーションから PTA への切り替えに関するセクションは、そのセクションの手順が適用されないため、スキップします。  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>オプション B - Azure AD Connect と PowerShell を使用してフェデレーションから PTA に切り替える

お客様のフェデレーションが最初に Azure AD Connect を使用して構成されていない場合は、このオプションを使用します。 まず、パススルー認証を有効にする必要があります。

   1. Azure AD Connect サーバーで、ウィザードを開きます。
   2. **[ユーザー サインインの変更]** を選択し、**[次へ]** を選択します。
   3. **[Azure AD に接続]** 画面で、グローバル管理者のユーザー名とパスワードを指定します。
   4. **[ユーザー サインイン]** 画面でオプション ボタンを **[構成しない]** から **[パススルー認証]** に変更し、**[シングル サインオンを有効にする]** をオンにして、**[次へ]** を選択します。
   5. **[シングル サインオンを有効にする]** 画面で、ドメイン管理者アカウントの資格情報を入力し、**[次へ]** を選択します。

   > [!NOTE]
   > シームレス シングル サインオンを有効にするには、ドメイン管理者の資格情報が必要です。このプロセスでは、管理者特権のアクセス許可を必要とする以下のアクションが実行されるためです。 ドメイン管理者の資格情報は Azure AD Connect にも Azure AD にも保存されません。 それらは機能を有効にするためだけに使用され、正常に完了した後は破棄されます。
   >
   > * お客様のオンプレミスの Active Directory (AD) に AZUREADSSOACC という名前のコンピューター アカウント (Azure AD を表す) が作成されます。
   > * コンピューター アカウントの Kerberos の復号化キーは、Azure AD と安全に共有されます。
   > * また、Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) も作成されます。

   6. **[構成の準備完了]** 画面で **[Start Synchronization process when configuration completes]\(構成が完了したら同期処理を開始する\)** チェック ボックスがオンになっていることを確認します。 次に、**[構成]** を選択します。</br>
   ‎![画像](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   以下の手順は、[構成] を選択すると行われます。
   * パススルー認証エージェントがインストールされます
   * パススルー機能が有効になります
   * シームレス シングル サインオンが有効になります。  
   7. **[フェデレーション]** がまだ **[有効]** になっており、**[シームレス シングル サインオン]** と **[パススルー認証]** が [有効] になったことを確認します。
   ![画像 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. **[パススルー認証]** を選択し、状態が **[アクティブ]** であることを確認します。</br>
   
   認証エージェントがアクティブでない場合は、次の手順でドメインの変換プロセスに進む前に、[これらのトラブルシューティング手順](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)に従ってください。 お客様の PTA エージェントが正常にインストールされたことと、Azure portal でそれらの状態が "アクティブ" と表示されることを確認する前にお客様のドメインを変換すると、認証が停止する危険性があります。  
   9. 次に、追加の認証エージェントをデプロイします。 **Azure portal** を開き、**[Azure Active Directory]**、**[Azure AD Connect]** の順に移動して、**[パススルー認証]** をクリックします。
   10. **[パススルー認証]** ページで、**[ダウンロード]** ボタンをクリックします。 **[エージェントのダウンロード]** 画面で、**[Accept terms and download]\(利用規約に同意してダウンロード\)** をクリックします。
   
   追加の認証エージェントのダウンロードが開始されます。 セカンダリ認証エージェントは、ドメイン参加済みサーバーにインストールします。

  > [!NOTE]
  > 最初のエージェントは、Azure AD Connect ツールの [ユーザー サインイン] セクションで行われた構成変更の一部として、常に Azure AD Connect サーバー自体にインストールされます。 追加の認証エージェントは、別のサーバーにインストールされる必要があります。 2 個から 3 個の追加の認証エージェントを使用できるようにすることをお勧めします。
  
   11. 認証エージェントのインストールを実行します。 インストール中に、**グローバル管理者**アカウントの資格情報を指定する必要があります。</br>
   ![画像 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![画像 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. 認証エージェントがインストールされたら、パススルー認証エージェントの正常性のページに戻って、追加のエージェントの状態を確認できます。

この時点では、フェデレーションはまだ有効であり、お客様のドメインのために機能しています。 デプロイを続行するには、各ドメインをフェデレーションからマネージドに変換し、パススルー認証がドメインに対する認証要求の処理を開始するようにする必要があります。

すべてのドメインを同時に変換する必要はありません。お客様の運用環境テナントのテスト ドメインや、ユーザー数が最も少ないドメインから開始することができます。

変換は、Azure AD PowerShell モジュールを使用して実行されます。

   1. **PowerShell** を開き、**グローバル管理者**アカウントを使用して Azure AD にログインします。
   2. 最初のドメインを変換するには、次のコマンドを実行します。
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. **Azure AD ポータル**を開き、**[Azure Active Directory]** を選択し、**[Azure AD Connect]** を選択します。  
   4. お客様のフェデレーション ドメインをすべて変換できたら、**[フェデレーション] が [無効]** になっており、**[シームレス シングル サインオン]** と **[パススルー認証]** が **[有効]** になっていることを確認します。</br>
   ![画像](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>テストと次のステップ

### <a name="test-pass-through-authentication"></a>パススルー認証のテスト 

お客様のテナントでフェデレーションが使用されていたとき、ユーザーは Azure AD サインイン ページから AD FS 環境にリダイレクトされていました。 フェデレーションではなくパススルー認証を使用するようにテナントが構成されたので、ユーザーは AD FS にリダイレクトされず、代わりに Azure AD サインイン ページから直接サインインします。

シームレス SSO によって自動的にサインインされないように、InPrivate モードで Internet Explorer を開き、Office 365 のログイン ページ ([https://portal.office.com](https://portal.office.com/)) に移動します。 お客様のユーザーの **UPN** を入力し、**[次へ]** をクリックします。 必ず、お客様のオンプレミスの Active Directory と同期された、以前はフェデレーションだったハイブリッド ユーザーの UPN を入力してください。 ユーザーには、ユーザー名とパスワードを入力する画面が表示されます。

![画像 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![画像 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

パスワードを入力すると、Office 365 ポータルにリダイレクトされるはずです。

![画像 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>シームレス シングル サインオンのテスト

   1. 企業ネットワークに接続された、ドメイン参加済みマシンにログインします。 
   2. **Internet Explorer** または **Chrome** を開き、次のいずれかの URL に移動します。   
      ‎  
      ‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (Contoso をご自分のドメインに置き換えてください)。

      ユーザーはすぐに Azure AD サインイン ページにリダイレクトされ、"サインインしようとしています" というメッセージが表示されます。ユーザー名またはパスワードの入力を求められることはないはずです。</br>
   ![画像 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. その後、ユーザーはリダイレクトされ、アクセス パネルに正常にサインインします。

> [!NOTE]
> シームレス シングル サインオンは、ドメイン ヒント (myapps.microsoft.com/contoso.com など) をサポートする Office 365 サービスで機能します。 Office 365 ポータル (portal.office.com) では現在、ドメイン ヒントがサポートされていません。そのため、ユーザーは自分の UPN を入力する必要があると予想されます。 UPN が入力されると、ユーザーの代わりにシームレス シングル サインオンによって Kerberos チケットが取得されるため、パスワードを入力せずにログインできます。
> [!TIP]
> シングル サインオンのエクスペリエンスを向上させるために、[Windows 10 に Azure AD Hybrid Join](https://docs.microsoft.com/azure/active-directory/device-management-introduction) をデプロイすることを検討してください。

### <a name="removal-of-the-relying-party-trust"></a>証明書利用者信頼の削除

すべてのユーザーとクライアントが Azure AD を通じて正常に認証されていることを確認できたら、Office 365 の証明書利用者信頼を削除しても問題ないと考えられます。

AD FS が他の目的で使用されていない場合 (他の証明書利用者信頼が構成されている場合)、この時点で AD FS を使用停止して問題ありません。

### <a name="rollback"></a>ロールバック

重大な問題が見つかり、すぐに解決できない場合は、ソリューションをフェデレーションにロールバックする可能性もあります。

特定のデプロイの詳細については、フェデレーションの設計とデプロイに関するドキュメントを参照してください。 プロセスには以下が含まれる必要があります。

* Convert-MSOLDomainToFederated を使用してマネージド ドメインをフェデレーションに変換する
* 必要な場合は、追加の要求規則を構成する。

### <a name="enable-synchronization-of-userprincipalname-updates"></a>userPrincipalName の更新の同期を有効にする

これまで、オンプレミスから同期サービスを使用して UserPrincipalName 属性を更新することは、次の 2 つの条件が両方とも当てはまらない限り、できませんでした。

* ユーザーが管理対象ユーザー (非フェデレーション ユーザー) である。
* ユーザーにライセンスが割り当てられていない。

この機能を確認または有効にする方法については、次の記事を参照してください。

[userPrincipalName の更新の同期](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>シームレス SSO の Kerberos 復号化キーのロールオーバー

お客様のオンプレミスの AD フォレストで作成された AZUREADSSOACC コンピューター アカウント (Azure AD を表します) の Kerberos 復号化キーを頻繁にロールオーバーすることが重要です。 Active Directory ドメイン メンバーがパスワードの変更を送信する頻度に合わせて、少なくとも 30 日ごとに Kerberos 復号化キーをロールオーバーすることを強くお勧めします。 関連するデバイスが AZUREADSSOACC コンピューター アカウント オブジェクトにアタッチされていないため、ロールオーバーは手動で実行する必要があります。

Kerberos 復号化キーのロールオーバーを開始するには、お客様が Azure AD Connect を実行しているオンプレミス サーバーで、次の手順を行います。

[AZUREADSSOACC コンピューター アカウントの Kerberos 復号化キーをロールオーバーするにはどうすればよいですか](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)。

## <a name="monitoring-and-logging"></a>監視およびログ記録

ソリューションの可用性を維持するには、認証エージェントを実行しているサーバーを監視する必要があります。 認証エージェントでは、一般的なサーバー パフォーマンス カウンターに加えて、認証の統計情報とエラーを把握するために使用できるパフォーマンス オブジェクトが公開されます。

認証エージェントによって、操作のログが Windows イベント ログの "アプリケーションとサービス ログ\Microsoft\AzureAdConnect\AuthenticationAgent\Admin" に記録されます。

必要に応じて、トラブルシューティング ログを有効にすることができます。

詳細については、[監視とログ記録](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure AD Connect の設計概念](plan-connect-design-concepts.md)
- [適切な認証の選択](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [サポートされているトポロジ](plan-connect-design-concepts.md)
