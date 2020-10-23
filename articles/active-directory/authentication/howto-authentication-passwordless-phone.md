---
title: Microsoft Authenticator アプリを使用したパスワードなしのサインイン - Azure Active Directory
description: Microsoft Authenticator アプリを使用した Azure AD へのパスワードなしのサインインを有効にする (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053a489993c31344b96e83253c88eed93b27b145
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964827"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Microsoft Authenticator アプリを使用したパスワードなしのサインインを有効にする (プレビュー)

Microsoft Authenticator アプリを使用すると、パスワードを使用せずに Azure AD アカウントにサインインできます。 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) のテクノロジと同様、Microsoft Authenticator は、キーベースの認証を使用して、デバイスに関連付けられていて生体認証または PIN を使用するユーザー資格情報を有効にします。 この認証方法は、モバイルを含む任意のデバイス プラットフォームと、Microsoft 認証ライブラリと統合されているすべてのアプリまたは Web サイトで使用できます。

![サインインを承認するようユーザーに求めている、ブラウザーでのサインインの例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

ユーザー名の入力後にパスワードの入力を求めるプロンプトが表示されるのではなく、Microsoft Authenticator アプリから電話によるサインインを有効にしているユーザーには、アプリで番号をタップするように求めるメッセージが表示されます。 アプリでのサインイン プロセスを完了するには、ユーザーは、番号を照合し、 **[Approve]\(承認\)** を選択してから、自分の PIN を入力するか生体認証を行う必要があります。

## <a name="prerequisites"></a>前提条件

Microsoft Authenticator アプリでパスワードなしの電話によるサインインを使用するには、次の前提条件を満たしている必要があります。

- 検証方法としてプッシュ通知が許可されている Azure Multi-Factor Authentication。
- iOS 8.0 以降、または Android 6.0 以降を実行しているデバイスにインストールされている最新バージョンの Microsoft Authenticator。

> [!NOTE]
> Azure AD PowerShell を使用して Microsoft Authenticator アプリのパスワードなしサインイン プレビューを有効にした場合は、それがディレクトリ全体に対して有効になります。 この新しい方法の使用を有効にすると、それが PowerShell ポリシーよりも優先されます。 新しい "*認証方法*" メニューを使用して、テナント内のすべてのユーザーに対して有効にすることをお勧めします。そうしないと、新しいポリシーに含まれていないユーザーは、パスワードなしでサインインできなくなります。

## <a name="enable-passwordless-authentication-methods"></a>パスワードなしの認証方法を有効にする

Azure AD でパスワードレス認証を使用するには、まず、統合された登録エクスペリエンスを有効にしてから、パスワードなしの方法に対してユーザーを有効にします。

### <a name="enable-the-combined-registration-experience"></a>統合された登録エクスペリエンスを有効にする

パスワードなしの認証方法の登録機能は、統合された登録機能に依存しています。 ユーザー自身が統合された登録を完了できるようにするには、[統合されたセキュリティ情報の登録を有効にする](howto-registration-mfa-sspr-combined.md)手順に従ってください。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>パスワードなしの電話によるサインインの認証方法を有効にする

Azure AD では、サインイン プロセス中に使用できる認証方法を選択できます。 その後、ユーザーは使用する方法を登録します。

パスワードなしの電話によるサインインの認証方法を有効にするには、次の手順を実行します。

1. "*全体管理者*" アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. *[Azure Active Directory]* を検索および選択してから、 **[セキュリティ]**  >  **[認証方法]**  >  **[認証方法ポリシー (プレビュー)]** の順に移動します。
1. **[Passwordless phone sign-in]\(パスワードなしの電話によるサインイン\)** で、次のオプションを選択します。
   1. **有効にする** - [はい] または [いいえ]
   1. **ターゲット** - [すべてのユーザー] または [ユーザーの選択]
1. 新しいポリシーを適用するには、 **[保存]** を選択します。

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator アプリのユーザー登録と管理

Azure AD でパスワードレス認証方法を使用できるようになったら、ユーザーは次の手順を使用して、パスワードレス認証方法に対して自分自身を登録する必要があります。

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) にアクセスします。
1. サインインし、次に **[方法の追加] > [認証アプリ]** を選択してから、 **[追加]** を選択し、Authenticator アプリを追加します。
1. 指示に従って、お使いのデバイスに Microsoft Authenticator アプリをインストールして構成します。
1. **[完了]** を選択して、Authenticator の構成を完了します。
1. **Microsoft Authenticator** アプリで、登録したアカウントのドロップダウン メニューから **[電話によるサインインを有効にする]** を選択します。
1. アプリの指示に従って、パスワードなしの電話によるサインインに対するアカウントの登録を完了します。

組織は、Microsoft Authenticator アプリの構成と電話によるサインインの有効化をさらに支援するために、[パスワードではなく電話でサインインする](../user-help/user-help-auth-app-sign-in.md)ようにユーザーに指示することができます。

> [!NOTE]
> ポリシーによって電話によるサインインを使用することが許可されていないユーザーは、Microsoft Authenticator アプリ内でそれを有効にすることができなくなります。  

## <a name="sign-in-with-passwordless-credential"></a>パスワードなしの資格情報でサインインする

管理者がテナントを有効にして、ユーザーが電話によるサインインを有効にするように自分の Microsoft Authenticator アプリを更新すると、ユーザーはパスワードなしのサインインの使用を開始できます。

電話によるサインインの使用を開始するには、サインイン ページでユーザー名を入力し、 **[次へ]** を選択した後、ユーザーは **[Other ways to sign in]\(その他のサインイン方法\)** を選択し、次に **[Microsoft Authenticator アプリで要求を承認する]** を選択する必要がある場合があります。 その後、ユーザーには数字が表示され、Microsoft Authenticator アプリでパスワードを使用する代わりに認証するために適切な番号を選択するように求められます。 ユーザーは、パスワードなしの電話によるサインインを使用すると、別の方法を選択するまで、再度それを使用するように求められます。

![Microsoft Authenticator アプリを使用したブラウザーのサインインの例](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>既知の問題

現在のプレビュー エクスペリエンスには、次の既知の問題があります。

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>パスワードなしの電話によるサインインのオプションが表示されない

ユーザーがパスワードなしの電話によるサインインの確認に回答せずに保留していて、再度サインインしようとすると、ユーザーには、代わりにパスワードを入力するオプションのみが表示される場合があります。 Microsoft Authenticator を開き、パスワードなしの電話によるサインインを引き続き使用するための通知プロンプトに応答してください。

### <a name="federated-accounts"></a>フェデレーション アカウント

ユーザーがパスワードなしの資格情報を有効にすると、Azure AD ログインは login_hint の使用を停止し、ユーザーをフェデレーション ログインの場所に誘導します。 このロジックは、ハイブリッド テナントのユーザーが、[代わりにパスワードを使用する] をクリックする追加の手順を行わずに、サインイン確認のために AD FS に移動されないようにします。

### <a name="azure-mfa-server"></a>Azure MFA サーバー

組織のオンプレミスの Azure MFA サーバーを介して MFA に対して有効にされたエンド ユーザーは、単一のパスワードなしの電話によるサインイン資格情報を引き続き作成し、使用することができます。 ユーザーが資格情報で Microsoft Authenticator の複数のインストール (5 つ以上) をアップグレードしようとすると、この変更はエラーになることがあります。  

### <a name="device-registration"></a>デバイス登録

この新しい、強力な資格情報を作成するための前提条件の 1 つは、Microsoft Authenticator アプリのインストール先となるデバイスが、個々のユーザーに対し、Azure AD テナント内にも登録されていることです。 現在のデバイス登録の制限により、デバイスは、1 つのテナントにしか登録できません。 この制限は、電話によるサインインを有効にできるのは、Microsoft Authenticator アプリの職場または学校の 1 つのアカウントのみであることを意味します。

> [!NOTE]
> デバイスの登録は、デバイスの管理、つまり "MDM" と同じではありません。 この場合、Azure AD ディレクトリでデバイス ID とユーザー ID の両方が関連付けられるだけです。  

## <a name="next-steps"></a>次のステップ

Azure AD 認証とパスワードレスの方法については、次の記事を参照してください。

* [パスワードレス認証のしくみを学習する](concept-authentication-passwordless.md)
* [デバイス登録の詳細](../devices/overview.md#getting-devices-in-azure-ad)
* [Azure Multi-Factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
