---
title: "Azure AD の電子メールのセキュリティ保護に関する推奨ポリシー | Microsoft Docs"
description: "電子メールのポリシーと構成の適用方法に関する Microsoft の推奨ポリシーについて説明します。"
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 05/24/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 1747ba5ff55780322eeaae5396099ec373289c98
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017


---

# <a name="recommended-policies"></a>推奨ポリシー
 
この記事では、組織の電子メールをセキュリティで保護し、電子メール クライアントで先進認証と条件付きアクセスをサポートする場合に役立つ、推奨されるポリシーについて説明します。 また、ユーザーに最高の SSO エクスペリエンスを提供するために推奨される既定のプラットフォーム クライアント構成と、条件付きアクセスの技術的前提条件についても説明します。

## <a name="prerequisites"></a>前提条件

組織では、このドキュメントの残りの部分で説明されているポリシーを実装する前に、次のいくつかの前提条件を満たす必要があります。
* [名前付きネットワークを構成します](https://docs.microsoft.com/azure/active-directory/active-directory-known-networks-azure-portal)。 Azure AD Identity Protection を使用すると、利用可能なすべてのセッション データを収集および分析して、リスク スコアを生成できます。 Azure AD の名前付きネットワーク構成で、組織のネットワークのパブリック IP 範囲を指定することをお勧めします。 これらの範囲からのトラフィックには低いリスク スコアが与えられるため、企業環境外からのトラフィックはより高いリスク スコアとして扱われます。
* [すべてのユーザーを Multi-Factor Authentication (MFA) に登録します](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-manage-users-and-devices)。 Azure AD Identity Protection では、Azure MFA を使用して追加のセキュリティ検証を実行します。 すべてのユーザーに Azure MFA への事前登録を求めることをお勧めします。
* [ドメイン参加済み Windows コンピューターの自動デバイス登録を有効にします](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)。 条件付きアクセスにより、サービスに接続するデバイスがドメインに参加しているか、準拠デバイスであることを保証できます。 これを Windows コンピューターでサポートするには、デバイスを Azure AD に登録する必要があります。  この記事では、自動デバイス登録を構成する方法について説明します。  AD FS は必須要件であることに注意してください。
* **サポート チームを準備します**。 MFA を完了できないユーザーのためのプランを立ててください。 たとえば、ユーザーをポリシー除外グループに追加する、ユーザーのために新しい MFA 情報を登録するなどです。 これらのセキュリティ上重要な変更のいずれかを行う前に、実際のユーザーから依頼されたものであることを確認する必要があります。 ユーザーの管理者に承認を手伝ってもらうことは、有効な手順です。
* [オンプレミスの AD へのパスワード ライトバックを構成します](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)。 パスワード ライトバックにより、Azure AD は、アカウントのセキュリティ侵害のリスクが高いことが検出されたときにオンプレミスのパスワードを変更するようにユーザーに要求できます。 Azure AD Connect を使用してこの機能を有効にする方法には、次の 2 つがあります。 パスワード ライトバックは、Azure AD Connect のセットアップ ウィザードの [オプション機能] 画面で有効にできますし、Windows PowerShell を使用して有効にすることもできます。  
* [先進認証を有効にし](https://support.office.com/en-us/article/Enable-Exchange-Online-for-modern-authentication-58018196-f918-49cd-8238-56f57f38d662)、[レガシ エンドポイントを保護します](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-supported-apps)。  条件付きアクセスの対象となるのは、先進認証を使用するモバイル アプリケーションとデスクトップ アプリケーションです。 アプリケーションでレガシ認証プロトコルが使用されている場合は、条件が適用されていても、アクセスできてしまう可能性があります。 そこで、どのアプリケーションが条件付きアクセス規則に対応しているかと、他のアプリケーション エントリ ポイントをセキュリティで保護するために必要となる手順を理解しておくことが重要となります。
* Rights Management をアクティブ化して、[Azure Information Protection を有効にします](https://docs.microsoft.com/en-us/information-protection/get-started/infoprotect-tutorial-step1)。 電子メールで Azure Information Protection を使用して、電子メールの分類から開始します。 クイック スタート チュートリアルに従って、ポリシーをカスタマイズおよび発行します。  

### <a name="recommended-email-clients"></a>推奨される電子メール クライアント
次の電子メール クライアントは、先進認証と条件付きアクセスをサポートしています。 Azure Information Protection は、まだ一部のクライアントでは利用できません。

|プラットフォーム|クライアント|バージョン/メモ|Azure Information Protection|
|:-------|:-----|:------------|:--------------------|
|**Windows**|Outlook|2016、2013 ([先進認証を有効にする]((https://support.office.com/en-us/article/Enable-Modern-Authentication-for-Office-2013-on-Windows-devices-7dc1c01a-090f-4971-9677-f1b192d6c910))|あり|
|**iOS**|Outlook|[最新](https://itunes.apple.com/us/app/microsoft-outlook-email-and-calendar/id951937596?mt=8)|いいえ|
|**Android**|Outlook|[最新](https://play.google.com/store/apps/details?id=com.microsoft.office.outlook&hl=en)|いいえ|
|**macOS**|近日サポート予定||いいえ|
|**Linux**|サポートされていません||いいえ|

#### <a name="additional-client-software"></a>追加のクライアント ソフトウェア
Azure Information Protection で保護されたドキュメントにアクセスするには、追加のソフトウェアが必要になる場合があります。 Azure Information Protection で保護されたドキュメントを作成および表示するには、[サポートされているソフトウェアおよびドキュメント形式](https://docs.microsoft.com/information-protection/get-started/requirements-applications)を使用していることを確認してください。

## <a name="recommended-client-configuration-for-sso-and-conditional-access"></a>SSO および条件付きアクセスを使用するうえで推奨されるクライアント構成
このセクションでは、ユーザーに最高の SSO エクスペリエンスを提供するために推奨される既定のプラットフォーム クライアント構成と、条件付きアクセスの技術的前提条件について説明します。

### <a name="windows-devices"></a>Windows デバイス
Windows 10 (バージョン 1703 以降) をお勧めします。これは、Azure がオンプレミスと Azure AD の両方で可能な限りスムーズな SSO エクスペリエンスを提供するように設計されているためです。 職場または学校から支給されたデバイスは、Azure AD に直接参加するように構成する必要があります。また、組織がオンプレミス AD ドメイン参加を使用している場合、これらのデバイスは [Azure AD にサイレント モードで自動的に登録](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)されるように構成する必要があります。 

BYOD Windows デバイスの場合、ユーザーは [職場または学校アカウントを追加] を使用できます。 BYOD Windows デバイスの場合、ユーザーは [職場または学校アカウントを追加] を使用できます。 Windows 10 で Chrome ブラウザーを使用しているユーザーは、Edge/IE と同じスムーズなサインイン エクスペリエンスを得るために[拡張機能をインストール](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?utm_source=chrome-app-launcher-info-dialog)する必要があることに注意してください。 また、組織にドメイン参加済み Windows 7 デバイスがある場合、Windows 10 以外のコンピューター向け Microsoft Workplace Join [パッケージをインストール](https://www.microsoft.com/en-us/download/details.aspx?id=53554)することでデバイスを Azure AD に登録できます。

### <a name="ios-devices"></a>iOS デバイス

条件付きアクセスまたは MFA ポリシーをデプロイする前に [Microsoft Authenticator アプリ](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)をユーザー デバイスにインストールすることをお勧めします。 少なくとも、職場または学校アカウントを追加して Azure AD に[デバイスを登録することをユーザーに求める](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time)場合や、ユーザーが Intune ポータル サイト アプリをインストールして自分のデバイスを管理対象として登録する場合は、このアプリをインストールする必要があります。 これは、構成された条件付きアクセス ポリシーによって異なります。

### <a name="android-devices"></a>Android デバイス

ユーザーには、条件付きアクセス ポリシーをデプロイする前か、特定の認証試行で必要になった時点で [Intune ポータル サイト アプリ](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal&hl=en
)と [Microsoft Authenticator アプリ](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)をインストールするようお勧めしています。 アプリをインストールした後、ユーザーは Azure AD に登録するか、デバイスを Intune に登録するよう求められる可能性があります。 これは、構成された条件付きアクセス ポリシーによって異なります。 

さらに、Android for Work または Samsung Knox をサポートする OEM およびバージョンで会社所有のデバイス (COD) を標準化して、Intune MDM ポリシーで電子メール アカウントを管理および保護できるようにすることもお勧めします。

## <a name="tiers-of-security-and-protection"></a>セキュリティと保護のレベル

ほとんどの組織に、セキュリティとデータ保護に関する特定の要件があります。 これらの要件は、業界や組織内の職務によって異なります。 たとえば、法務部門や Office 365 の管理者の場合は、電子メールのやり取りに関して、他の事業部門のユーザーには必要のない高いレベルのセキュリティおよび情報保護管理が必要になることがあります。 

各業界には、独自の専門的な諸規制もあります。 この記事では、考え得るすべてのセキュリティ オプションや業界または職務ごとの推奨事項を示すのではなく、ニーズの細分性に基づいて適用可能な、電子メールの 3 レベルのセキュリティと保護 ([ベースライン、機密、および高度規制](https://go.microsoft.com/fwlink/p/?linkid=841656)) に関する推奨事項を提供しています。  

**ベースライン**。 データにアクセスする ID とデバイスに加えて、データを保護するための最低限の基準を確立することをお勧めします。 ベースラインの推奨事項に従うことにより、多くの組織のニーズを満たす強力な既定の保護を提供できます。 

**機密**。 顧客によっては、より高いレベルで保護する必要のあるデータのサブセットを取り扱っていたり、そのより高いレベルで全データを保護する必要があったりします。 Office 365 環境のすべてまたは特定のデータ セットに対して強化された保護を適用することができます。 機密データにアクセスする ID とデバイスを同等のセキュリティ レベルで保護することをお勧めします。 

**高度規制**。 組織によっては、ごくわずかな極秘データや営業秘密、規制されたデータを扱っていることもあります。 Microsoft では、ID とデバイスに対する追加の保護を含め、組織がこれらの要件を満たすのに役立つ機能を提供しています。 

### <a name="default-protection-mechanism-recommendations"></a>既定の保護メカニズムに関する推奨事項


前に定義したセキュリティと保護の各レベルに対する既定の保護メカニズムの推奨事項を次の表に示します。

|保護メカニズム|ベースライン|重要|高度規制|
|:-------------------|:-------|:--------|:---------------|
|**MFA を適用する**|中レベル以上のサインイン リスク|低レベル以上のサインイン リスク|すべての新しいセッション|
|**パスワードの変更を強制する**|リスクの高いユーザーの場合|リスクの高いユーザーの場合|リスクの高いユーザーの場合|
|**Intune アプリケーション保護を強制する**|あり|あり|あり|
|**Intune の登録を強制する (COD)**|準拠デバイスまたはドメイン参加済みデバイスを必須とする|準拠デバイスまたはドメイン参加済みデバイスを必須とする|準拠デバイスまたはドメイン参加済みデバイスを必須とする|

### <a name="device-ownership"></a>デバイスの所有権
上記の表は、多くの組織が従業員全体のモバイル生産性を向上させるために、会社所有のデバイス (COD) を組み合わせたもののほか、個人のデバイス、つまり Bring Your Own Device (BYOD) をサポートするうえでの傾向を反映したものです。 Intune App Protection ポリシーは、COD と BYOD の両方で Outlook モバイル アプリやその他の Office モバイル アプリから電子メール データが漏洩しないように保護します。  

会社所有のデバイスは、追加の保護と制御を適用するために、Intune によって管理されているか、ドメインに参加している必要があります。  データの機密性によっては、特定の数のユーザーまたは特定のアプリケーションについては BYOD を許可しないという選択もあり得ます。

## <a name="baseline"></a>ベースライン 
このセクションでは、ベースライン レベルのデータ、ID、およびデバイス保護を対象に、電子メールのセキュリティ保護に関する推奨事項について説明します。 これらの推奨事項は、多くの組織の既定の保護ニーズを満たします。
>[!NOTE]
>以下のポリシーは付加的なものであり、他のポリシーにプラスして構成されます。 各セクションでは、各レベルに適用される追加分のみを説明します。
>

### <a name="conditional-access-policy-settings"></a>条件付きアクセス ポリシーの設定

#### <a name="identity-protection"></a>Identity Protection 
前のセクションで説明したように、ユーザーにシングル サインオン (SSO) エクスペリエンスを提供できます。 [リスク イベント](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)に基づいて必要なときに介入するだけで済みます。  

* 中レベル以上のサインイン リスクに対して MFA を求める
* リスクの高いユーザーに対してはセキュリティで保護されたパスワードの変更を求める

>[!IMPORTANT]
>このポリシーの推奨事項では、[パスワード同期](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)と[セルフサービスのパスワード リセット](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)が求められます。
>

#### <a name="data-loss-prevention"></a>データ損失の防止 
デバイスおよびアプリ管理ポリシーの目標は、デバイスを紛失したり、盗難に遭ったりしたときのデータ損失を防ぐことです。 これは、データへのアクセスを PIN で保護すること、デバイス上のデータを暗号化すること、およびデバイスのセキュリティが侵害されていないことを確認することによって実現できます。

|ポリシーの推奨事項|Description|
|:--------------------|:----------|
|**ユーザーによる PC 管理を必須とする**|ユーザーに対し、各自の PC を Active Directory ドメインに参加させるか、Intune または Configuration Manager を使用した管理の対象として登録するよう求めます|
|**グループ ポリシー オブジェクト (GPO) またはドメイン参加済み PC の Configuration Manager ポリシーを介してセキュリティ設定を適用する**|管理対象 PC を構成して、BitLocker、ウイルス対策、およびファイアウォールを有効にするポリシーをデプロイします|
|**ユーザーによるモバイル デバイス管理を必須とする**|電子メールへのアクセスに使用されるユーザー デバイスを Intune で管理するか、会社の電子メールへのアクセスを Intune App Protection ポリシーで保護されているモバイル電子メール アプリ (Outlook Mobile など) に限定するよう求めます|
|**管理対象デバイスに Intune デバイス コンプライアンス ポリシーを適用する**|管理対象の会社のモバイル デバイスおよび Intune によって管理される PC に対して、Intune デバイス コンプライアンス ポリシーを適用します。このポリシーでは、6 文字以上の PIN、デバイスの暗号化、正常なデバイス (脱獄されていない、root 化されていない、正常性構成証明に合格している) に加えて、可能な場合は、Lookout や SkyCure のようなサード パーティの MTP によって低リスクと見なされるデバイスが求められます|
|**管理対象外デバイスで実行されている管理対象アプリに対して Intune App Protection ポリシーを適用する**|管理対象外の個人のモバイル デバイスで実行されている管理対象アプリに対して Intune App Protection ポリシーを適用します。このポリシーでは、6 文字以上の PIN、デバイスの暗号化、デバイスが正常であること (脱獄されていない、root 化されていない、正常性構成証明に合格している) が求められます|

### <a name="user-impact"></a>ユーザーへの影響

ほとんどの組織において、ユーザーがいつどのような条件で Office 365 にサインインして電子メールにアクセスするのかを想定できることが重要です。  

ユーザーは、通常、次の状況を除いてシングル サインオン (SSO) の恩恵を受けます。 
* Exchange Online の認証トークンを要求するとき
  * 中レベル以上のサインイン リスクが検出され、ユーザーが現在のセッションでまだ MFA を実行していない場合、ユーザーに対して MFA が要求されることがあります。  
  * ユーザーは、Intune App Protection SDK をサポートする電子メール アプリを使用するか、Intune 準拠デバイスまたは AD ドメイン参加済みデバイスから電子メールにアクセスする必要があります。 
* リスクのあるユーザーがサインインして MFA を正常に完了すると、パスワードの変更を求められます。

## <a name="sensitive"></a>重要

このセクションでは、機密レベルのデータ、ID、およびデバイス保護を対象に、電子メールのセキュリティ保護に関する推奨事項について説明します。 これらの推奨事項は、より高いレベルで保護する必要のあるデータのサブセットを取り扱っている顧客や、そのより高いレベルで全データを保護する必要がある顧客向けです。 

Office 365 環境のすべてまたは特定のデータ セットに対して強化された保護を適用することができます。 たとえば、保護されたアプリ間でのみ機密データが共有されるようにポリシーを適用して、データの損失を防ぐことができます。 機密データにアクセスする ID とデバイスを同等のセキュリティ レベルで保護することをお勧めします。 

### <a name="conditional-access-policy-settings"></a>条件付きアクセス ポリシーの設定
#### <a name="identity-protection"></a>Identity Protection 

前のセクションで説明したように、ユーザーにシングル サインオン (SSO) エクスペリエンスを提供できます。 [リスク イベント](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)に基づいて必要なときに介入するだけで済みます。   

* 低レベル以上のリスク セッションで MFA を必須とする
 * リスクの高いユーザーに対してはセキュリティで保護されたパスワードの変更を求める

>[!IMPORTANT]
>このポリシーの推奨事項では、[パスワード同期](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)と[セルフサービスのパスワード リセット](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)が求められます。
>

#### <a name="data-loss-prevention"></a>データ損失の防止 

これらのデバイスおよびアプリ管理ポリシーの目標は、デバイスを紛失したり、盗難に遭ったりしたときのデータ損失を防ぐことです。 これは、データへのアクセスを PIN で保護すること、デバイス上のデータを暗号化すること、およびデバイスのセキュリティが侵害されていないことを確認することによって実現できます。

|ポリシーの推奨事項|Description|
|:--------------------|:----------|
|**ユーザーによる PC 管理を必須とする**|電子メールへのアクセスを許可するにあたり、ユーザーに対し、各自の PC を Active Directory ドメインに参加させるか、Intune または Configuration Manager を使用した管理の対象として登録して、それらのデバイスをポリシーに準拠させるよう求めます|
|**グループ ポリシー オブジェクト (GPO) またはドメイン参加済み PC の Configuration Manager ポリシーを介してセキュリティ設定を適用する**|管理対象 PC を構成して、BitLocker、ウイルス対策、およびファイアウォールを有効にするポリシーをデプロイします|
|**ユーザーによるモバイル デバイス管理を必須とする**|電子メールへのアクセスに使用されるユーザー デバイスを Intune で管理するか、会社の電子メールへのアクセスを Intune App Protection ポリシーで保護されているモバイル電子メール アプリ (Outlook Mobile など) に限定するよう求めます|
|**管理対象デバイスに Intune デバイス コンプライアンス ポリシーを適用する**|管理対象の会社のモバイル デバイスおよび Intune によって管理される PC に対して、Intune デバイス コンプライアンス ポリシーを適用します。このポリシーでは、6 文字以上の PIN、デバイスの暗号化、正常なデバイス (脱獄されていない、root 化されていない、正常性構成証明に合格している) に加えて、可能な場合は、Lookout や SkyCure のようなサード パーティの MTP によって低リスクと見なされるデバイスが求められます|
|**管理対象外デバイスで実行されている管理対象アプリに対して Intune App Protection ポリシーを適用する**|管理対象外の個人のモバイル デバイスで実行されている管理対象アプリに対して Intune App Protection ポリシーを適用します。このポリシーでは、6 文字以上の PIN、デバイスの暗号化、デバイスが正常であること (脱獄されていない、root 化されていない、正常性構成証明に合格している) が求められます|

### <a name="user-impact"></a>ユーザーへの影響

ほとんどの組織において、ユーザーがいつどのような条件で Office 365 電子メールにサインインするのかを想定できることが重要です。 

ユーザーは、通常、次の状況を除いてシングル サインオン (SSO) の恩恵を受けます。 
* Exchange Online の認証トークンを要求するとき
  * 低レベル以上のサインイン リスクが検出され、ユーザーが現在のセッションでまだ MFA を実行していない場合、ユーザーに対して MFA が要求されます。  
  * ユーザーは、Intune App Protection SDK をサポートする電子メール アプリを使用するか、Intune 準拠デバイスまたは AD ドメイン参加済みデバイスから電子メールにアクセスする必要があります。 
* リスクのあるユーザーがサインインして MFA を正常に完了すると、パスワードの変更を求められます。

## <a name="highly-regulated"></a>高度規制
このセクションでは、高度規制レベルのデータ、ID、およびデバイス保護を対象に、電子メールのセキュリティ保護に関する推奨事項について説明します。 これらの推奨事項は、極秘データ、営業秘密、または規制されたデータの取り扱いがごくわずかである顧客を対象にしています。 Microsoft では、ID とデバイスに対する追加の保護を含め、組織がこれらの要件を満たすのに役立つ機能を提供しています。 

### <a name="conditional-access-policy-settings"></a>条件付きアクセス ポリシーの設定
#### <a name="identity-protection"></a>Identity Protection 

Microsoft では、高度規制レベルについては、すべての新しいセッションに対して MFA を適用することをお勧めしています。
* すべての新しいセッションで MFA を必須とする
* リスクの高いユーザーに対してはセキュリティで保護されたパスワードの変更を求める

>[!IMPORTANT]
>このポリシーの推奨事項では、[パスワード同期](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)と[セルフサービスのパスワード リセット](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)が求められます。
>

#### <a name="data-loss-prevention"></a>データ損失の防止
これらのデバイスおよびアプリ管理ポリシーの目標は、デバイスを紛失したり、盗難に遭ったりしたときのデータ損失を防ぐことです。 これは、データへのアクセスを PIN で保護すること、デバイス上のデータを暗号化すること、およびデバイスのセキュリティが侵害されていないことを確認することによって実現できます。

高度規制レベルについては、Intune 準拠デバイスまたはドメイン参加済みデバイスでのみ実行される Intune App Protection ポリシーをサポートするアプリを必須とすることをお勧めします。

|ポリシーの推奨事項|Description|
|:--------------------|:----------|
|**ユーザーによる PC 管理を必須とする**|電子メールへのアクセスを許可するにあたり、ユーザーに対し、各自の PC を Active Directory ドメインに参加させるか、Intune または Configuration Manager を使用した管理の対象として登録して、それらのデバイスをポリシーに準拠させるよう求めます|
|**グループ ポリシー オブジェクト (GPO) またはドメイン参加済み PC の Configuration Manager ポリシーを介してセキュリティ設定を適用する**|管理対象 PC を構成して、BitLocker、ウイルス対策、およびファイアウォールを有効にするポリシーをデプロイします|
|**ユーザーによるモバイル デバイス管理を必須とする**|電子メールへのアクセスに使用されるユーザー デバイスを Intune で管理するか、会社の電子メールへのアクセスを Intune App Protection ポリシーで保護されているモバイル電子メール アプリ (Outlook Mobile など) に限定するよう求めます|
|**管理対象デバイスに Intune デバイス コンプライアンス ポリシーを適用する**|管理対象の会社のモバイル デバイスおよび Intune によって管理される PC に対して、Intune デバイス コンプライアンス ポリシーを適用します。このポリシーでは、6 文字以上の PIN、デバイスの暗号化、正常なデバイス (脱獄されていない、root 化されていない、正常性構成証明に合格している) に加えて、可能な場合は、Lookout や SkyCure のようなサード パーティの MTP によって低リスクと見なされるデバイスが求められます|

### <a name="user-impact"></a>ユーザーへの影響
ほとんどの組織において、ユーザーがいつどのような条件で Office 365 電子メールにサインインするのかを想定できることが重要です。 

* シングル サインオン セッションの最長有効期間は 1 日です。 セッションの有効期限が切れた後、ユーザーは MFA で再認証する必要があります。
* リスクのあるユーザーがサインインして MFA を完了すると、パスワードの変更を求められます。
* Exchange Online の認証トークンを要求するとき
  * ユーザーは、新しいセッションを開始するたびに MFA を実行するよう求められます。  
  * ユーザーは、Intune App Protection SDK をサポートする電子メール アプリを使用する必要があります
  * ユーザーは、Intune 準拠デバイスまたは AD ドメイン参加済みデバイスから電子メールにアクセスする必要があります。 
 
 ## <a name="next-steps"></a>次のステップ
 [推奨ポリシーのデプロイ](secure-email-deploy-recommended-policies.md)

