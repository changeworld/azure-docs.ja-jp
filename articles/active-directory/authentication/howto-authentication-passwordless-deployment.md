---
title: Azure Active Directory でパスワードレス認証のデプロイを計画する
description: パスワードレス認証をデプロイするための手順
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/28/2021
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5597ca90a2264d192273ebfd57ecfd51382c3c4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349302"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Azure Active Directory でパスワードレス認証のデプロイを計画する

パスワードは、主な攻撃ベクトルの 1 つです。 悪意のあるアクターは、ソーシャル エンジニアリング、フィッシング、スプレー攻撃を使用してパスワードを侵害します。 パスワードレス認証戦略により、これらの攻撃のリスクを軽減できます。

Microsoft では、Azure Active Directory (Azure AD) と統合される次の [3 つのパスワードレス認証オプション](concept-authentication-passwordless.md)を提供しています。

* [Microsoft Authenticator アプリ](./concept-authentication-passwordless.md#microsoft-authenticator-app) - ユーザーが任意のプラットフォームまたはブラウザーにサインインできるようにすることで、あらゆる iOS や Android フォンを強力なパスワードレスの資格情報に変えます。

* [FIDO2 準拠のセキュリティ キー](./concept-authentication-passwordless.md#fido2-security-keys) - キオスクなどの共有マシンにサインインするユーザー、電話の使用が制限されている状況、および高い特権を持つ ID に対して便利です。 

* [Windows Hello for Business](./concept-authentication-passwordless.md#windows-hello-for-business) - 専用の Windows コンピューターを使用するユーザーに最適です。 

> [!NOTE]
> すべてのリンクを含む、この計画のオフライン バージョンを作成するには、お使いのブラウザーの PDF ファイルへの出力機能を使用します。

## <a name="use-the-passwordless-methods-wizard"></a>パスワードレス方法ウィザードを使用する

[Azure portal](https://portal.azure.com/) 上で、対象ユーザーごとに適切な方法を選択するのに役立つ、パスワードレス方法ウィザードを利用できるようになりました。 適切な方法をまだ決定していない場合は、[https://aka.ms/passwordlesswizard](https://aka.ms/passwordlesswizard) を参照してからこの記事に戻り、選択した方法の計画を続行してください。 **このウィザードにアクセスするには、管理者権限が必要です。**

## <a name="passwordless-authentication-scenarios"></a>パスワードなし認証のシナリオ

Microsoft のパスワードレス認証方法では、さまざまなシナリオを実現できます。 組織のニーズ、前提条件、および各認証方法の機能を考慮して、パスワードなし認証戦略を選択します。 

次の表に、デバイスの種類別のパスワードレス認証方法の一覧を示します。 推奨事項は **太字** で示されています。

| デバイスの種類| パスワードレス認証方法 |
| - | - |
| 専用の Windows 以外のデバイス| <li> **Microsoft Authenticator アプリ** <li> セキュリティ キー |
| 専用の Windows 10 コンピューター (バージョン1703 以降)| <li> **Windows Hello for Business** <li> セキュリティ キー |
| 専用の Windows 10 コンピューター (バージョン1703 より前)| <li> **Windows Hello for Business** <li> Microsoft Authenticator アプリ |
| 共有デバイス: タブレット、モバイル デバイス| <li> **Microsoft Authenticator アプリ** <li> ワンタイム パスワードによるサインイン |
| キオスク (レガシ)| **Microsoft Authenticator アプリ** |
| キオスクと共有コンピューター ‎(Windows 10)| <li> **セキュリティ キー** <li> Microsoft Authenticator アプリ |


## <a name="prerequisites"></a>前提条件 

パスワードレスのデプロイを開始する前に、前提条件を満たしていることを確認してください。

### <a name="required-roles"></a>必要なロール

このデプロイに必要な最小特権ロールを次に示します。
<p>

| Azure AD ロール| 説明 |
| - | -|
| グローバル管理者| 統合された登録エクスペリエンスを実装するため。 |
| 認証管理者| 認証方法を実装および管理するため。 |
| User| デバイス上で Authenticator アプリを構成するか、Web または Windows 10 でのサインイン用のセキュリティ キー デバイスを登録するため。 |

このデプロイ計画の一環として、すべての[特権アカウント](../privileged-identity-management/pim-configure.md)に対してパスワードレス認証を有効にすることをお勧めします。

### <a name="microsoft-authenticator-app-and-security-keys"></a>Microsoft Authenticator アプリとセキュリティ キー

前提条件は、選択したパスワードレス認証方法によって決まります。

| 前提条件| Microsoft Authenticator アプリ| FIDO2 セキュリティ キー|
| - | -|-|
| [Azure AD 多要素認証 (MFA) とセルフサービス パスワード リセット (SSPR) の統合された登録](howto-registration-mfa-sspr-combined.md)が有効になっている| √| √|
| [ユーザーが Azure AD MFA を実行できる](howto-mfa-getstarted.md)| √| √|
| [ユーザーが Azure AD MFA と SSPR の登録をしている](howto-registration-mfa-sspr-combined.md)| √| √|
| [ユーザーがモバイル デバイスを Azure Active Directory に登録している](../devices/overview.md)| √| |
| Microsoft Edge や Mozilla Firefox (バージョン 67 以降) などのサポートされているブラウザーを使用している Windows 10 バージョン 1809 以降。 ネイティブ サポートには 1903 以降のバージョンをお勧めします。| | √|
| 互換性のあるセキュリティ キー。 [Microsoft でテストおよび検証済みの FIDO2 セキュリティ キー](concept-authentication-passwordless.md)、または他の互換性のある FIDO2 セキュリティ キーを確実に使用します。| | √|


### <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows Hello for Business の前提条件とデプロイ パスは、オンプレミス、ハイブリッド、クラウドのみのどの構成にデプロイするかに大きく依存します。 また、デバイスの参加戦略にも依存します。 

Windows Hello for Business を選択し、[ウィザードを完了](https://aka.ms/passwordlesswizard)して、組織に適した前提条件とデプロイを決定します。

![ウィザードで Windows Hello for Business を選択する](media/howto-authentication-passwordless-deployment/passwordless-wizard-select.png)


このウィザードでは、入力を使用して、従うべきステップバイステップの計画が作成されます。

## <a name="plan-the-project"></a>プロジェクトを計画する

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md)し、プロジェクトにおけるその利害関係者の役割がよく理解されていることを確認します。

### <a name="plan-a-pilot"></a>パイロットを計画する

パスワードなしの認証をデプロイするときは、最初に 1 つ以上のパイロット グループを有効にする必要があります。 この目的のためだけにグループを作成することができます。 パイロットに参加するユーザーをグループに追加します。 その後、選択したグループに対して、新しいパスワードなしの認証方法を有効にします。 「[パイロットのベスト プラクティス](../fundamentals/active-directory-deployment-plans.md)」を参照してください。

### <a name="plan-communications"></a>連絡を計画する

エンド ユーザーへの連絡には、次の情報を含める必要があります。

* [Azure AD MFA と SSPR の両方の統合された登録に関するガイダンス](howto-registration-mfa-sspr-combined.md)

* [Microsoft Authenticator アプリのダウンロード](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)

* [Microsoft Authenticator アプリでの登録](howto-authentication-passwordless-phone.md)

* [電話を使用したサインイン](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)

Microsoft では、エンド ユーザー向けの連絡テンプレートを提供しています。 連絡のドラフト作成に役立つ、[認証のロールアウトに関する資料](https://aka.ms/MFAtemplates)をダウンロードしてください。 このロールアウトの資料にはカスタマイズ可能なポスターと電子メール テンプレートが含まれており、それを使用して、組織内の今後のパスワードレス認証オプションについてユーザーに通知することができます。

## <a name="plan-user-registration"></a>ユーザーの登録を計画する

ユーザーは、**統合されたセキュリティ情報のワークフロー** ([https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)) の一部として、パスワードレス方法を登録します。 Azure AD は、セキュリティ キーと Microsoft Authenticator アプリの登録、および認証方法に対する他のすべての変更をログに記録します。 

パスワードを持っていない、初めて使用するユーザーに対しては、セキュリティ情報を [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo.md) で登録するための[一時アクセス パスコード](howto-authentication-temporary-access-pass.md)を管理者が提供できます。 これは期間限定のパスコードであり、強力な認証要件を満たしています。 **一時アクセス パスはユーザーごとのプロセスです**。

また、この方法を使用すると、ユーザーがセキュリティ キーや Microsoft Authenticator アプリなどの認証要素を紛失したり忘れたりした場合でも簡単に回復することができます。しかし、**新しい強力な認証方法を登録する** には、サインインする必要があります。 

>[!NOTE] 
> 一部のシナリオでセキュリティ キーまたは Microsoft Authenticator アプリを使用できない場合は、ユーザー名とパスワードを使用した多要素認証を別の登録済み方法と共にフォールバック オプションとして使用できます。

## <a name="plan-for-and-deploy-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを計画してデプロイする

[Microsoft Authenticator アプリ](concept-authentication-passwordless.md)は、あらゆる iOS や Android フォンを、強力なパスワードレスの資格情報に変えます。 これは Google Play ストアまたは Apple App Store から無償でダウンロードできます。 ユーザーに [Microsoft Authenticator アプリをダウンロードしてもらい](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)、指示に従って電話によるサインインを有効にしてもらいます。

### <a name="technical-considerations"></a>技術的な考慮事項

**Active Directory フェデレーション サービス (AD FS) 統合** - ユーザーが Microsoft Authenticator のパスワードレスの資格情報を有効にすると、そのユーザーの認証では、既定で承認のための通知が送信されます。 ハイブリッド テナントのユーザーは、[代わりにパスワードを使用する] を選択しない限り、サインインのために AD FS に転送されることはありません。 このプロセスでは、オンプレミスの条件付きアクセス ポリシーとパススルー認証 (PTA) フローもバイパスされます。 ただし、login_hint が指定されている場合、ユーザーは AD FS に転送され、パスワードレスの資格情報を使用するオプションはバイパスされます。

**Azure MFA サーバー** - 組織のオンプレミスの Azure MFA サーバーを介した多要素認証が有効になっているエンド ユーザーは、1 つのパスワードレスの電話によるサインイン資格情報を作成して使用できます。 ユーザーがその資格情報で Microsoft Authenticator アプリの複数のインストール (5 つ以上) をアップグレードしようとすると、この変更はエラーになることがあります。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなりました。 サインイン イベント時に多要素認証が必要な新しいお客様は、クラウドベースの Azure AD Multi-Factor Authentication (MFA) を使用していただく必要があります。 2019 年 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。 Azure MFA Server から Azure Active Directory MFA に移行することをお勧めします。

**デバイス登録** - Authenticator アプリをパスワードレス認証に使用するには、デバイスを Azure AD テナントに登録する必要があり、共有デバイスは使用できません。 デバイスは 1 つのテナントにのみ登録できます。 この制限は、Microsoft Authenticator アプリを使用する電話サインインに対して、1 つの職場または学校アカウントのみがサポートされることを意味します。

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリでの電話によるサインインをデプロイする

記事「[Microsoft Authenticator アプリを使用したパスワードなしのサインインを有効にする](howto-authentication-passwordless-phone.md)」の手順に従って、組織内でのパスワードなしの認証方法として Microsoft Authenticator アプリを有効にします。

### <a name="testing-microsoft-authenticator-app"></a>Microsoft Authenticator アプリのテスト

次に示すのは、Microsoft Authenticator アプリでのパスワードレス認証のサンプル テスト ケースです。

| シナリオ| 予想される結果 |
| - |-|
| ユーザーは Microsoft Authenticator アプリを登録できる| ユーザーは https://aka.ms/mysecurityinfo からアプリを登録できる |
| ユーザーは電話によるサインインを有効にできる| 職場アカウント用に構成された電話サインイン |
| ユーザーは電話でサインインしてアプリにアクセスできる| ユーザーは、電話でのサインイン フローを経て、アプリケーションに到達します。 |
| Microsoft Authenticator のパスワードレス サインインをオフにして、電話によるサインイン登録へのロールバックをテストする。 これは Azure AD ポータルの [認証方法] 画面から行う| 以前に有効にしたユーザーが、Microsoft Authenticator からパスワードなしのサインインを使用できなくなります。 |
| Microsoft Authenticator アプリから電話によるサインインを削除する| Microsoft Authenticator で職場アカウントが使用できなくなります |


### <a name="troubleshoot-phone-sign-in"></a>電話によるサインインのトラブルシューティング


| シナリオ| 解決策 |
| - |-|
| ユーザーが、統合された登録を実行できない。| [統合された登録](concept-registration-mfa-sspr-combined.md)が有効になっていることを確認します。 |
| ユーザーが Authenticator アプリで電話によるサインインを有効にできない。| ユーザーがデプロイのスコープ内にいることを確認します。 |
| ユーザーはパスワードレス認証のスコープ内にいないが、パスワードなしのサインインのオプションを表示され、それを実行できない。| ポリシーが作成される前に、ユーザーがアプリケーションで電話によるサインインを有効にした場合に発生します。 サインインを有効にするには、パスワードレス サインインが有効になっているユーザー グループにユーザーを追加します。 "サインインをブロックするには": ユーザーにそのアプリケーションから資格情報を削除させます。 |


## <a name="plan-for-and-deploy-fido2-compliant-security-keys"></a>FIDO2 準拠のセキュリティ キーを計画してデプロイする

互換性のあるセキュリティ キーを有効にします。 パスワードレス エクスペリエンスと互換性があることが判明しているキーを提供する [FIDO2 セキュリティ キー プロバイダーの一覧はこちら](concept-authentication-passwordless.md)をご覧ください。

### <a name="plan-security-key-lifecycle"></a>セキュリティ キーのライフサイクルを計画する

キーのライフサイクルの準備と計画を行います。

**キーの配布** - 組織にキーをプロビジョニングする方法を計画します。 集中的なプロビジョニング プロセスを使用することも、エンド ユーザーが FIDO 2.0 と互換性のあるキーを購入できるようにすることもできます。

 **キーのアクティブ化** - エンド ユーザーは、自分でセキュリティ キーをアクティブ化する必要があります。 エンド ユーザーは、[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) でセキュリティ キーを登録し、最初の使用時に 2 番目の要素 (PIN または生体認証) を有効にします。 初めて使用するユーザーの場合は、TAP を使用してセキュリティ情報を登録できます。

 **キーの無効化** - 管理者がユーザー アカウントに関連付けられている FIDO2 キーを削除する場合は、次に示すように、ユーザーの認証方法からキーを削除することで実行できます。 詳細については、「[キーを無効にする](howto-authentication-passwordless-security-key.md#disable-a-key)」を参照してください。

 

**新しいキーを発行する** - ユーザーは [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) にアクセスして、新しい FIDO2 キーを登録できます。 

### <a name="technical-considerations"></a>技術的な考慮事項

セキュリティ キーを使用して行えるパスワードレスのサインインのデプロイには、次の 3 種類があります。

* サポートされているブラウザー上の Azure AD Web アプリ

* Azure AD 参加済み Windows 10 デバイス

* Hybrid Azure AD 参加済み Windows 10 デバイス 

  * クラウドベースとオンプレミスの両方のリソースへのアクセスを提供します。 オンプレミスのリソースへのアクセスの詳細については、「[FIDO2 キーを使用したオンプレミスのリソースへの SSO](howto-authentication-passwordless-security-key-on-premises.md)」を参照してください

**Azure AD Web アプリと Azure AD 参加済み Windows デバイスの場合** は、以下を使用します。

* Microsoft Edge や Mozilla Firefox (バージョン 67 以降) などのサポートされているブラウザーを使用している Windows 10 バージョン 1809 以降。

* Windows 10 バージョン 1809 では、FIDO2 サインインがサポートされており、場合によっては、FIDO2 キーの製造元のソフトウェアをデプロイする必要があります。 バージョン 1903 以降を使用することをお勧めします。

**Hybrid Azure AD ドメイン参加済みデバイスの場合** は、以下を使用します。 

* Windows 10 バージョン 2004 以降 

* Windows Server 2016 または 2019 を実行している、完全にパッチが適用されたドメイン サーバー。 

* 最新バージョンの Azure AD Connect。

#### <a name="enable-windows-10-support"></a>Windows 10 のサポートを有効にする

FIDO2 セキュリティ キーを使用して Windows 10 のサインインを有効にするには、Windows 10 で資格情報プロバイダー機能を有効にする必要があります。 次のいずれかを選択します。

* [Intune を使用して資格情報プロバイダーを有効にする](howto-authentication-passwordless-security-key-windows.md)

  * Intune のデプロイを推奨します。

* [プロビジョニング パッケージを使用して資格情報プロバイダーを有効にする](howto-authentication-passwordless-security-key-windows.md)

  * Intune のデプロイを使用できない場合は、管理者が各コンピューターにパッケージをデプロイして、資格情報プロバイダー機能を有効にする必要があります。 パッケージのインストールは、次のいずれかのオプションを使用して実行できます。
    * グループ ポリシーまたは Configuration Manager
    * Windows 10 コンピューターへのローカル インストール

* [グループ ポリシーを使用して資格情報プロバイダーを有効にする](howto-authentication-passwordless-security-key-windows.md)

   * ハイブリッド Azure AD 参加済みデバイスでのみサポートされます。

#### <a name="enable-on-premises-integration"></a>オンプレミス統合を有効にする

[オンプレミスのリソースへのパスワードレス セキュリティ キー サインインを有効にする (プレビュー)](howto-authentication-passwordless-security-key-on-premises.md) 方法に関する記事の手順に従います。

> [!IMPORTANT] 
> FIDO2 セキュリティ キーを Windows 10 のサインインに利用するためには、これらの手順をすべての Hybrid Azure AD 参加済みデバイスに対しても実行する必要があります。


### <a name="key-restrictions-policy"></a>キーの制限ポリシー

セキュリティ キーをデプロイするときに、FIDO2 キーの使用を、組織によって承認された特定の製造元のみに制限することもできます。 キーを制限するには、Authenticator Attestation GUID (AAGUID) が必要です。 [AAGUID を取得するには 2 つの方法があります](howto-authentication-passwordless-security-key.md#security-key-authenticator-attestation-guid-aaguid)。

![キーの制限を適用する方法](media/howto-authentication-passwordless-deployment/security-key-enforce-key-restriction.png)


セキュリティ キーが制限されている場合、ユーザーが FIDO2 セキュリティ キーを登録しようとすると次のエラーが表示されます。

![キーが制限されているときのセキュリティ キー エラー](media/howto-authentication-passwordless-deployment/security-key-restricted-error.png)


ユーザーがセキュリティ キーを登録した後に AAGUID が制限された場合、次のメッセージが表示されます。

![AAGUID が制限されている場合のユーザー向けビュー](media/howto-authentication-passwordless-deployment/security-key-block-user-window.png)


*キーの制限ポリシーによってブロックされた FIDO2 キー

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 セキュリティ キーによるサインインをデプロイする

記事「[パスワードなしのセキュリティ キー サインインを有効にする](howto-authentication-passwordless-security-key.md)」の手順に従って、組織でのパスワードレス認証方法として FIDO2 セキュリティ キーを有効にします。 

### <a name="testing-security-keys"></a>セキュリティ キーのテスト

セキュリティ キーによるパスワードレス認証のサンプル テスト ケースを以下に示します。

#### <a name="passwordless-fido-sign-in-to-azure-active-directory-joined-windows-10-devices"></a>Azure Active Directory に参加している Windows 10 デバイスへのパスワードなし FIDO サインイン


| シナリオ (Windows ビルド)| 予想される結果 |
| - |-|
| ユーザーは FIDO2 デバイス (1809) を登録できる| ユーザーは、[設定] > [アカウント] > サインイン オプション > [セキュリティ キー] を使用して、FIDO2 デバイスを登録できます |
| ユーザーは FIDO2 デバイス (1809) をリセットできる| ユーザーは、製造元のソフトウェアを使用して FIDO2 デバイスをリセットできます |
| ユーザーは FIDO2 デバイス (1809) を使用してサインインできる| ユーザーはサインイン ウィンドウからセキュリティ キーを選択し、正常にサインインできます。 |
| ユーザーは FIDO2 デバイス (1903) を登録できる| ユーザーは、[設定] > [アカウント] > サインイン オプション > [セキュリティ キー] で、FIDO2 デバイスを登録できます |
| ユーザーは FIDO2 デバイス (1903) をリセットできる| ユーザーは、[設定] > [アカウント] > サインイン オプション > [セキュリティ キー] で、FIDO2 デバイスをリセットできます |
| ユーザーは FIDO2 デバイス (1903) を使用してサインインできる| ユーザーはサインイン ウィンドウからセキュリティ キーを選択し、正常にサインインできます。 |


#### <a name="passwordless-fido-sign-in-to-azure-ad-web-apps"></a>Azure AD Web アプリへのパスワードなしの FIDO サインイン


| シナリオ| 予想される結果 |
| - |-|
| ユーザーは Microsoft Edge を使用して aka.ms/mysecurityinfo で FIDO2 デバイスを登録できる| 登録は成功します |
| ユーザーは Firefox を使用して aka.ms/mysecurityinfo で FIDO2 デバイスを登録できる| 登録は成功します |
| ユーザーは Microsoft Edge を使用して FIDO2 デバイスで OneDrive Online にサインインできる| サインインは成功します |
| ユーザーは Firefox を使用して FIDO2 デバイスで OneDrive Online にサインインできる| サインインは成功します |
| Azure Active Directory ポータルの [認証方法] ウィンドウ内で FIDO2 セキュリティ キーをオフにすることにより、FIDO2 デバイス登録のロールバックをテストする| ユーザーは、 <li> 自分のセキュリティ キーを使用してサインインするように求められます。 <li> 正常にサインインできますが、次のエラーが表示されます。"会社のポリシーでは、別の方法を使用してサインインする必要があります。" <li>別の方法を選択して、正常にサインインすることができます。 ウィンドウを閉じてもう一度サインインし、同じエラー メッセージが表示されないことを確認します。 |


### <a name="troubleshoot-security-key-sign-in"></a>セキュリティ キーによるサインインのトラブルシューティング

| シナリオ| 解決策 |
| - | -|
| ユーザーが、統合された登録を実行できない。| [統合された登録](concept-registration-mfa-sspr-combined.md)が有効になっていることを確認します。 |
| ユーザーが[セキュリティの設定](https://aka.ms/mysecurityinfo)でセキュリティ キーを追加できない。| [セキュリティ キー](howto-authentication-passwordless-security-key.md)が有効になっていることを確認します。 |
| ユーザーが Windows 10 のサインイン オプションでセキュリティ キーを追加できない。| [Windows サインイン用のセキュリティ キー](concept-authentication-passwordless.md)が有効になっていることを確認します。 |
| **エラー メッセージ**:We detected that this browser or OS doesn't support FIDO2 security keys. (このブラウザーまたは OS では FIDO2 セキュリティ キーがサポートされていないことが検出されました。)| パスワードなしの FIDO2 セキュリティ デバイスは、Windows 10 バージョン 1809 以降のサポートされているブラウザー (Microsoft Edge、Firefox バージョン 67) でのみ登録できます。 |
| **エラー メッセージ**:会社のポリシーでは、別の方法を使用してサインインする必要があります。| テナントでセキュリティ キーが有効になっていることを確認します。 |
| ユーザーが Windows 10 バージョン 1809 でセキュリティ キーを管理できない| バージョン 1809 では、FIDO2 キー ベンダーによって提供されるセキュリティ キー管理ソフトウェアを使用する必要があります。 ベンダーにサポートについてお問い合わせください。 |
| FIDO2 のセキュリティ キーが破損している可能性がある - どうすればテストできるか。| [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) に移動して、テスト アカウントの資格情報を入力します。問題のあるセキュリティ キーをプラグインして、画面の右上にある [+] ボタンを選択し、[作成] を選択して作成プロセスを実行します。 このシナリオが失敗する場合、デバイスに欠陥がある可能性があります。 |

## <a name="manage-passwordless-authentication"></a>パスワードレス認証を管理する

[Azure portal](https://portal.azure.com/) 上でユーザーのパスワードレス認証方法を管理するには、ユーザー アカウントを選択し、[認証方法] を選択します。

### <a name="microsoft-graph-apis"></a>Microsoft Graph API 

Microsoft Graph の認証方法 API を使用して、パスワードレス認証方法を管理することもできます。 次に例を示します。

* ユーザーの FIDO2 セキュリティ キーの詳細を取得し、ユーザーがキーを紛失した場合は削除することができます。

* ユーザーの Microsoft Authenticator 登録の詳細を取得し、ユーザーが電話を紛失した場合は削除することができます。

* セキュリティ キーと Microsoft Authenticator アプリの認証方法ポリシーを管理します。

Microsoft Graph で管理できる認証方法の詳細については、「[Azure AD の認証方法 API の概要](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)」を参照してください。

### <a name="rollback"></a>ロールバック

パスワードなし認証は軽量の機能であり、エンド ユーザーへの影響は最小限に抑えられますが、ロールバックが必要になる場合があります。

ロールバックを行うには、管理者が Azure portal にサインインし、目的の強力な認証方法を選択して、有効にするオプションを [いいえ] に変更する必要があります。 このプロセスにより、すべてのユーザーのパスワードなし機能が無効になります。

![パスワードレス ロールバック](media/howto-authentication-passwordless-deployment/passwordless-rollback.png)

FIDO2 セキュリティ デバイスを既に登録しているユーザーには、次回のサインイン時にセキュリティ デバイスを使用するよう求めるメッセージが表示され、次のエラーが表示されます。

![パスワード ロールバックのエラー ウィンドウ](media/howto-authentication-passwordless-deployment/passswordless-rollback-error-window.png)

### <a name="reporting-and-monitoring"></a>レポートと監視

Azure AD には、技術やビジネスの分析情報を提供するレポートがあります。 ビジネスおよび技術アプリケーションの所有者に、組織の要件に基づいてこれらのレポートの所有権を引き受けさせ、レポートを使用させます。

次の表に、一般的なレポート シナリオの例をいくつか示します。

| リスクの管理| 生産性の向上| ガバナンスとコンプライアンス| other|
|-|-|-|-|
| レポートの種類| 認証方法 - 統合されたセキュリティ登録に登録されているユーザー| 認証方法 – アプリ通知に登録されているユーザー| サインイン: テナントにアクセスしているユーザーとその方法を確認する |
| 潜在的なアクション| 対象ユーザーがまだ登録されていない| Microsoft Authenticator アプリまたはセキュリティ キーの導入を推進する| アクセスを取り消すか、管理者に対して追加のセキュリティ ポリシーを適用する |


#### <a name="track-usage-and-insights"></a>使用状況と分析情報を追跡する

Azure AD では、次の場合に監査ログにエントリが追加されます。

* 管理者は [認証方法] セクションで変更を行います。

* ユーザーが、Azure AD 内で資格情報に対して何らかの種類の変更を行った場合。

* ユーザーが、Win 10 マシン上でセキュリティ キーに対して自分のアカウントを有効/無効にするか、セキュリティ キーの第 2 の要素をリセットした場合。 イベント ID: 4670 および 5382 を参照してください。

**Azure AD ではほとんどの監査データが 30 日間保持され**、Azure 管理ポータルまたは API でデータを分析システムにダウンロードして利用できます。 データ保持期間を長くする必要な場合は、[Microsoft Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk、Sumo Logic などの SIEM ツールでログをエクスポートして使用します。 監査、傾向分析やその他のビジネス ニーズに応じて、必要であれば保持期間を長くすることをお勧めします。

[認証方法のアクティビティ] ダッシュボードには、[登録] と [使用状況] という 2 つのタブがあります。

[[登録] タブ](https://portal.azure.com/) には、パスワードレス認証や、その他の認証方法を利用できるユーザーの数が表示されます。 このタブには次の 2 つのグラフが表示されます。

* 認証方法ごとの登録ユーザー数。

* 認証方法ごとの最近の登録。

![認証方法を表示する [登録] タブ](media/howto-authentication-passwordless-deployment/monitoring-registration-tab.png)

[[使用状況] タブ](https://portal.azure.com/)には、認証方法ごとのサインイン回数が表示されます。

![認証方法を表示する [使用状況] タブ](media/howto-authentication-passwordless-deployment/monitoring-usage-tab.png)

詳細については、[Azure AD 組織全体の登録済みの認証方法と使用状況の追跡](howto-authentication-methods-activity.md)に関する記事を参照してください。

#### <a name="sign-in-activity-reports"></a>サインイン アクティビティ レポート

[サインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md)を使用すると、さまざまなアプリケーションへのサインインに使用された認証方法を追跡できます。 

ユーザー行を選択し、 **[認証の詳細]** タブを選択すると、どのサインイン アクティビティにどの認証方法が使用されたかが表示されます。

![サインイン アクティビティのレポート](media/howto-authentication-passwordless-deployment/reporting-sign-in-activity.png)

## <a name="next-steps"></a>次のステップ

* [パスワードレス認証のしくみを学習する](concept-authentication-passwordless.md)

* [その他の ID 機能をデプロイする](../fundamentals/active-directory-deployment-plans.md)
