---
title: "Azure MFA を構成する | Microsoft Docs"
description: "これは、MFA で次に何を実行するかを説明する Azure Multi-factor Authentication のページです。  レポート、不正アクセスの通報、ワンタイム バイパス、カスタム音声メッセージ、キャッシュ、信頼できる IP、およびアプリ パスワードの説明が含まれます。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4dc029e01762d9ab6eb1c930f2941d0c1ac8ed7d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure Multi-Factor Authentication の設定を構成する

この記事は、導入した Azure Multi-factor Authentication を管理する際に役立ちます。  ここでは、Azure Multi-factor Authentication を最大限に活用するために役立つさまざまなトピックについて説明します。  ただし、[Azure Multi-factor Authentication のすべてのバージョン](/multi-factor-authentication-get-started.md#what-features-do-i-need)ですべての機能を使用できるわけではありません。

| 機能 | 説明 | 
|:--- |:--- |
| [ユーザーのブロックおよびブロック解除](#block-and-unblock) |ユーザーのブロック/ブロック解除では、ユーザーが認証要求を受信できないように設定できます。 |
| [不正アクセスのアラート](#fraud-alert) |ユーザーが各自のリソースに対する不正アクセスを通報できるように、不正アクセスのアラートを構成して設定できます。 |
| [ワンタイム バイパス](#one-time-bypass) |ワンタイム バイパスは、多要素認証を "バイパス" することによって、ユーザーを 1 回だけ認証できます。 |
| [カスタム音声メッセージ](#custom-voice-messages) |カスタム音声メッセージは、独自の録音やあいさつを多要素認証で使用できるようにします。 |
| [キャッシュ](#caching-in-azure-multi-factor-authentication) |キャッシュは、以降の認証の試みが自動的に成功する特定の期間を設定できるようにします。 |
| [信頼できる IP](#trusted-ips) |この機能を使用すると、管理者常駐型テナントまたはフェデレーション テナントの管理者は、信頼できる IP を使用して、会社のローカル イントラネットからサインインするユーザーの 2 段階認証をバイパスできます。 |
| [アプリ パスワード](#app-passwords) |アプリ パスワードは、MFA を認識しないアプリケーションが多要素認証をバイパスして動作を続行できるようにします。 |
| [記憶されたデバイスとブラウザーに対する Multi-Factor Authentication の記憶](#remember-multi-factor-authentication-for-devices-that-users-trust) |ユーザーが MFA を使用して正常にサインインした後、一定の日数の間、デバイスを記憶させることができます。 |
| [選択可能な検証方法](#selectable-verification-methods) |ユーザーが使用できる認証方法を選択できます。 |

## <a name="block-and-unblock"></a>ブロックおよびブロック解除
ユーザーのブロック/ブロック解除は、ユーザーが認証要求を受信できないようにする場合に使用できます。 ブロックされているユーザーを認証しようとすると、自動的に拒否されます。 ブロックされているユーザーは、ブロックされた時間から 90 日間ブロックされ続けます。

### <a name="block-a-user"></a>ユーザーのブロック
1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[MFA Server]** > **[ユーザーのブロック/ブロック解除]** に移動します。
3. **[追加]** をクリックしてユーザーをブロックします。
4. **[レプリケーション グループ]** を選択し、ブロックされたユーザー名を **username@domain.com** として入力し、**[理由]** フィールドにコメントを入力します。
5. **[追加]** をクリックしてユーザーのブロックを終了します。

### <a name="unblock-a-user"></a>ユーザーのブロック解除
1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[MFA Server]** > **[ユーザーのブロック/ブロック解除]** に移動します。
3. **[アクション]** 列で、ブロック解除するユーザーの横にある **[ブロック解除]** をクリックします。
4. **[Reason for unblocking](ブロック解除の理由)** フィールドにコメントを入力します。
5. **[ブロック解除]** をクリックして、ユーザーのブロック解除を終了します。

## <a name="fraud-alert"></a>不正アクセスのアラート
ユーザーが各自のリソースに対する不正アクセスを通報できるように、不正アクセスのアラートを構成して設定できます。  ユーザーは、不正アクセスを、モバイル アプリまたは電話で通報できます。

### <a name="turn-on-fraud-alert"></a>不正アクセスのアラートを有効にする
1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[MFA Server]** > **[不正アクセスのアラート]** に移動します。

   ![不正アクセスのアラート](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. **[ユーザーが不正アクセスを通報できるようにする]** を**オン**にします。
4. **[保存]** を選択します。

### <a name="configuration-options"></a>構成オプション

- **不正アクセスが報告されたときのユーザーのブロック** - ユーザーが不正アクセスを報告した場合、そのユーザーのアカウントは 90 日間、または管理者がそのアカウントのブロックを解除するまでブロックされます。 管理者はサインイン レポートを使用してサインインを確認し、将来の不正アクセスを防止するための適切なアクションを実行できます。 その後、管理者はそのユーザーのアカウントの[ブロックを解除](#unblock-a-user)できます。
- **案内メッセージ後に入力する不正アクセス通報コード** - ユーザーが 2 段階認証を実行する電話を受信すると、通常、# を押してサインインを確認します。 不正アクセスを通報する場合は、# を押す前にコードを入力します。 このコードは、既定では **0** ですが、カスタマイズすることができます。

> [!NOTE]
> Microsoft の既定の音声案内では、不正アクセスのアラートを送信するには 0# を押すようにユーザーに指示します。 0 以外のコードを使用する場合は、適切な指示を行う独自のカスタム音声案内を記録し、アップロードする必要があります。

### <a name="view-fraud-reports"></a>不正アクセスの通報を表示する
1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側で、 **[Active Directory]**を選択します。
3. 管理するディレクトリを選択します。 
4. **[構成]** を選択します。
5. Multi-Factor Authentication の **[サービス設定の管理]** を選択します。
6. [サービス設定] ページの下部にある **[ポータルに移動]** を選択します。
7. Azure Multi-Factor Authentication 管理ポータルで、[レポートの表示] の下の **[不正アクセスのアラート]** をクリックします。
8. レポートに表示する日付範囲を指定します。 ユーザー名、電話番号、およびユーザーの状態を指定することもできます。
9. **[実行]** をクリックすると、不正アクセスのレポートが表示されます。 レポートをエクスポートする場合は、**[CSV にエクスポート]** をクリックします。

## <a name="one-time-bypass"></a>ワンタイム バイパス
ワンタイム バイパスは、2 段階認証の実行なしでユーザーを 1 回だけ認証できるようにします。 このバイパスは一時的なものであり、指定された秒数が経過すると無効になります。 モバイル アプリまたは電話で通知または通話に対応しない状況でも、ワンタイム バイパスを有効にすることで、ユーザーは目的のリソースにアクセスできるようになります。

### <a name="create-a-one-time-bypass"></a>ワンタイム バイパスを作成する

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[MFA Server]** > **[ワンタイム バイパス]** に移動します。

   ![ワンタイム バイパス](./media/multi-factor-authentication-whats-next/onetimebypass.png)
3. **[追加]**を選択します。
4. 必要に応じて、このバイパスのレプリケーション グループを選択します。
5. ユーザー名 (username@domain.com の形式)、バイパスが持続する秒数、およびバイパスする理由を入力します。 
6. **[追加]**を選択します。 制限時間はすぐに有効になるため、ユーザーはワンタイム バイパスの有効期限が切れる前にサインインする必要があります。 

### <a name="view-the-one-time-bypass-report"></a>ワンタイム バイパス レポートを表示する
1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側で、 **[Active Directory]**を選択します。
3. 管理するディレクトリを選択します。 
4. **[構成]** を選択します。
5. Multi-Factor Authentication の **[サービス設定の管理]** を選択します。
6. [サービス設定] ページの下部にある **[ポータルに移動]** を選択します。
7. Azure Multi-Factor Authentication 管理ポータルで、[レポートの表示] の下の **[ワンタイム バイパス]** をクリックします。
8. レポートに表示する日付範囲を指定します。 ユーザー名、電話番号、およびユーザーの状態を指定することもできます。
9. **[実行]** をクリックすると、バイパスのレポートが表示されます。 レポートをエクスポートする場合は、**[CSV にエクスポート]** をクリックします。

## <a name="custom-voice-messages"></a>カスタム音声メッセージ
カスタム音声メッセージは、独自の録音やあいさつを 2 段階認証で使用できるようにします。 これらは、Microsoft による録音に加えて使用するか、その代わりに使用できます。

開始する前に、次の制限に注意してください。

* サポートされているファイルの形式は .wav と .mp3 です。
* ファイル サイズの上限は 5 MB です。
* 認証メッセージは、20 秒より短くする必要があります。 20 秒よりも長くなると、メッセージが終わる前にユーザーが応答せず、確認がタイムアウトするために、確認が失敗する可能性があります。

### <a name="set-up-a-custom-message"></a>カスタム メッセージを設定する

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[MFA Server]** > **[電話の設定]** に移動します。

   ![電話の設定](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. **[案内の追加]** を選択します。
4. 案内の種類と言語を選択します。
5. アップロードする .mp3 や .wav サウンド ファイルを選択します。
6. **[追加]**を選択します。

## <a name="caching-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication でのキャッシュ
キャッシュは、その期間中は後続の認証の試みが自動的に成功する特定の期間を設定できるようにします。 これは主に、VPN などのオンプレミス システムによって複数の認証要求が送信され、最初の要求がまだ処理中である場合に使用されます。 キャッシュにより、処理中の最初の認証にユーザーが成功した後で、後続の要求が自動的に処理されるようになります。 

キャッシュは Azure AD へのサインインに使用するためのものではありません。

### <a name="set-up-caching"></a>キャッシュを設定する 
1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[MFA Server]** > **[キャッシュ規則]** に移動します。

   ![キャッシュ規則](./media/multi-factor-authentication-whats-next/cachingrules.png)

4. **[追加]**を選択します。
5. ドロップダウン リストからキャッシュの種類を選択し、最大キャッシュ (秒数) を指定します。 
6. 必要に応じて認証の種類を選択し、アプリケーションを指定します。 
7. **[追加]**を選択します。


## <a name="trusted-ips"></a>信頼できる IP
信頼できる IP は、管理者常駐型テナントまたはフェデレーション テナントの管理者が、会社のローカル イントラネットからサインインするユーザーの 2 段階認証をバイパスするために使用できる Azure MFA の機能です。 この機能は、Azure Multi-Factor Authentication の完全なバージョンで使用で、管理者を対象とする無料バージョンでは使用できません。 Azure Multi-Factor Authentication の完全なバージョンを入手する方法の詳細については、[Azure Multi-Factor Authentication](multi-factor-authentication.md) に関するページを参照してください。

| Azure AD テナントの種類 | 使用可能な信頼できる IP オプション |
|:--- |:--- |
| 管理者常駐型 |<li>特定の IP アドレス範囲 – 管理者は、会社のイントラネットからサインインするユーザーの 2 段階認証をバイパスできる IP アドレスの範囲を指定できます。</li> |
| フェデレーション |<li>すべてのフェデレーション ユーザー - 組織の内部からサインインするすべてのフェデレーション ユーザーは、AD FS によって発行される要求を使用して 2 段階認証をバイパスします。</li><br><li>特定の IP アドレス範囲 – 管理者は、会社のイントラネットからサインインするユーザーの 2 段階認証をバイパスできる IP アドレスの範囲を指定できます。 |

このバイパスは、会社のイントラネット内からのみ機能します。 たとえばすべてのフェデレーション ユーザーを選択しているときに、ユーザーが会社のイントラネットの外部からサインインした場合は、ユーザーが AD FS 要求を提示している場合でも、2 段階認証を使用してそのユーザーを認証する必要があります。 

**企業ネットワーク内のエンド ユーザー エクスペリエンス:**

[信頼できる IP] が無効な場合は、ブラウザー フローでは 2 段階認証が必要であり、以前のリッチ クライアント アプリではアプリ パスワードが必要です。 

[信頼できる IP] アドレスが有効な場合、ブラウザー フローで 2 段階認証は*不要*であり、以前のリッチ クライアント アプリでアプリ パスワードは*不要*です (ユーザーがアプリ パスワードを既に作成していないことを条件とします)。 アプリ パスワードを既に使用している場合は、パスワードが必要です。 

**企業ネットワークの外部のエンド ユーザー エクスペリエンス:**

[信頼できる IP] が有効であっても無効であっても、ブラウザー フローでは 2 段階認証が必要であり、以前のリッチ クライアント アプリではアプリ パスワードが必要です。 

### <a name="enable-named-locations-using-conditional-access"></a>条件付きアクセスを使用した名前付きの場所の有効化

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側で、**[Azure Active Directory]** > **[条件付きアクセス]** > **[名前付きの場所]** を選択します。
3. **[新しい場所]** を選択します。
4. 場所の名前を指定します。
5. **[信頼できる場所としてマークする]** を選択します。
6. IP 範囲を CIDR 表記 (192.168.1.1/24 など) で指定します。
7. **[作成]**

### <a name="enable-trusted-ips-using-conditional-access"></a>条件付きアクセスを使用した信頼できる IP の有効化

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側で、**[Azure Active Directory]** > **[条件付きアクセス]** > **[名前付きの場所]** を選択します。
3. **[MFA の信頼できる IP の構成]** を選択します。
4. [サービス設定] ページの [信頼できる IP] には、次の 2 つのオプションがあります。
   
   * **イントラネット内から送信されたフェデレーション ユーザーからのリクエストの場合**: このチェック ボックスをオンします。 企業ネットワークからサインインしているすべてのフェデレーション ユーザーは、AD FS によって発行される要求を使用して、2 段階認証をバイパスします。 イントラネットの要求を適切なトラフィックに追加する規則が AD FS にあることを確認します。 規則が存在しない場合、AD FS で "c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);" という規則を作成します。

   * **特定範囲の IP から送信されたリクエストの場合** - 用意されているボックスに、IP アドレスを CIDR 表記で入力します。 例: 範囲 xxx.xxx.xxx.1 から xxx.xxx.xxx.254 の場合は xxx.xxx.xxx.0/24、単一の IP アドレスの場合は xxx.xxx.xxx.xxx/32。 最大で 50 の IP アドレス範囲を入力できます。 これらの IP アドレスからサインインしているユーザーは、2 段階認証をバイパスします。
5. **[保存]** を選択します。

### <a name="enable-trusted-ips-using-service-settings"></a>サービス設定を使用した信頼できる IP の有効化

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側で、**[Azure Active Directory]** > **[ユーザーとグループ]** > **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. [サービス設定] ページの [信頼できる IP] には、次の 2 つのオプションがあります。
   
   * **イントラネット内から送信されたフェデレーション ユーザーからのリクエストの場合**: このチェック ボックスをオンします。 企業ネットワークからサインインしているすべてのフェデレーション ユーザーは、AD FS によって発行される要求を使用して、2 段階認証をバイパスします。 イントラネットの要求を適切なトラフィックに追加する規則が AD FS にあることを確認します。 規則が存在しない場合、AD FS で "c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);" という規則を作成します。

   * **特定範囲の IP から送信されたリクエストの場合** - 用意されているボックスに、IP アドレスを CIDR 表記で入力します。 例: 範囲 xxx.xxx.xxx.1 から xxx.xxx.xxx.254 の場合は xxx.xxx.xxx.0/24、単一の IP アドレスの場合は xxx.xxx.xxx.xxx/32。 最大で 50 の IP アドレス範囲を入力できます。 これらの IP アドレスからサインインしているユーザーは、2 段階認証をバイパスします。
6. **[保存]** を選択します。

![信頼できる IP](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>アプリ パスワード
Office 2010 以前や Apple Mail などの一部のアプリは、2 段階認証をサポートしていません。 それらは、2 つ目の認証を受け入れるように構成されていません。 これらのアプリを使用するには、従来のパスワードの代わりに "アプリ パスワード" を使用する必要があります。 アプリ パスワードは、アプリケーションが 2 段階認証をバイパスして動作を続行できるようにします。

> [!NOTE]
> Office 2013 クライアントのための最新の認証
> 
> Office 2013 クライアント (Outlook を含む) 以降は、最新の認証プロトコルをサポートしており、2 段階認証を有効にすることができます。 有効にした場合、これらのクライアントではアプリ パスワードは必要ありません。  詳しくは、「[Office 2013 modern authentication public preview announcement (Office 2013 の最新の認証のパブリック プレビューに関する発表)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」を参照してください。

### <a name="important-things-to-know-about-app-passwords"></a>アプリ パスワードについて理解しておくべきこと
アプリ パスワードについて知っておくべきことの重要な事柄の一覧を次に示します。

* アプリ パスワードは、アプリごとに 1 回だけ入力する必要があります。 ユーザーは、アプリ パスワードを記録する必要はなく、毎回入力する必要はありません。
* 実際のパスワードは自動的に生成され、ユーザーが指定することはありません。 これは、自動的に生成されたパスワードのほうが攻撃者から推測されづらく、より安全なためです。
* 1 ユーザーあたり 40 個というパスワード数の制限があります。 
* パスワードをキャッシュし、オンプレミスで使用するアプリは、アプリ パスワードが組織 ID の外では不明になるため、起動できないことがあります。例として、オンプレミスだが、アーカイブ メールはクラウド内にある Exchange メールがあります。 同じパスワードは機能しません。
* ユーザーのアカウントで多要素認証を有効にしたら、Outlook や Lync などのほとんどのブラウザー以外のクライアントでアプリ パスワードを使用できます。 そのユーザーが管理者アカウントを持つ場合でも、Windows PowerShell などのブラウザー以外のアプリケーションから、アプリ パスワードを使用して、管理操作を実行することはできません。  PowerShell スクリプトを実行するサービス アカウントは強固なパスワードで作成し、そのアカウントに対する 2 段階認証は有効にしないでください。

> [!WARNING]
> クライアントがオンプレミスの自動検出エンドポイントとクラウドの自動検出エンドポイントの両方と通信するハイブリッド環境ではアプリ パスワードは機能しません。 これは、オンプレミスでの認証ではドメイン パスワードが、クラウドでの認証にはアプリ パスワードが必要であるためです。

### <a name="naming-guidance-for-app-passwords"></a>アプリ パスワードの名前付けのガイダンス
アプリ パスワードの名前は、そのパスワードが使用されるデバイスを反映させるようにします。 たとえば、Outlook、Word、Excel などのブラウザーではないアプリケーションがあるラップトップでは、Laptop という名前のアプリ パスワードを 1 つ作成し、そのアプリ パスワードをこれらのアプリケーションで使用します。 次に、デスクトップ コンピューター上の同じアプリケーションで使用される Desktop という名前の別のアプリ パスワードを作成します。 

アプリケーションごとに 1 つのアプリ パスワードではなく、デバイスごとに 1 つのアプリ パスワードを作成することをお勧めします。

### <a name="federated-sso-app-passwords"></a>フェデレーション (SSO) アプリ パスワード
Azure AD は、オンプレミスの Windows Server Active Directory Domain Services (AD DS) とのフェデレーション (シングル サインオン) をサポートします。 組織が Azure AD とフェデレーションされているときに Azure Multi-factor Authentication を使用する場合は、アプリ パスワードを使用するときに、次に示す重要な情報があります。 このセクションは、フェデレーション (SSO) 顧客のみに適用されます。

* アプリ パスワードは Azure AD によって検証されます。したがってフェデレーションをバイパスします。 フェデレーションは、アプリ パスワードを設定するときにのみアクティブに使用されます。
* フェデレーション (SSO) ユーザーの場合、パッシブ フローとは異なり、ID プロバイダー (IdP) には接続されません。 パスワードは組織 ID の中に保存されます。ユーザーが退職した場合、その情報は、DirSync を使用してリアルタイムで 組織 ID に送信される必要があります。 アカウントの無効化/削除を同期させるには最大 3 時間かかる可能性があり、Azure AD 内のアプリ パスワードの無効化/削除が遅れることがあります。
* オンプレミスのクライアント アクセス制御の設定は、アプリ パスワードでは受け入れられません。
* アプリ パスワードに対するオンプレミス以外の認証ログ/監査機能はありません。
* ある種の高度なアーキテクチャ設計では、2 段階認証をクライアントで使用するときに、認証場所によっては、組織のユーザー名とパスワードをアプリ パスワードと組み合わせて使用する必要があります。 オンプレミスのインフラストラクチャに対して認証するクライアントの場合は、組織のユーザー名とパスワードを使用します。 Azure AD に対して認証するクライアントはアプリケーション パスワードを使用します。

  たとえば、以下で構成されるアーキテクチャがあるとします。

  * Active Directory のオンプレミスのインスタンスをAzure AD とフェデレーションしている
  * Exchange をオンラインで使用している
  * Lync を特にオンプレミスで使用している
  * Azure Multi-Factor Authentication を使用している

  ![追加のセキュリティ確認](./media/multi-factor-authentication-whats-next/federated.png)

  これらのインスタンスでは、以下を実行する必要があります。

  * Lync にサインインするときに、組織のユーザー名とパスワードを使用する。
  * Exchange にオンラインで接続している Outlook クライアント経由でアドレス帳にアクセスするときは、アプリ パスワードを使用する。

### <a name="allow-app-password-creation"></a>アプリ パスワードを作成できるようにする
既定では、ユーザーはアプリ パスワードを作成できません。 この機能を有効にする必要があります。 ユーザーがアプリ パスワードを作成できるようにするには、次の手順を使用します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側で、**[Azure Active Directory]** > **[ユーザーとグループ]** > **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
6. **[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可する]** の横のラジオ ボタンをオンにします。

![アプリ パスワードの作成](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>アプリ パスワードを作成する
ユーザーは、初回の登録時にアプリ パスワードを作成できます。 登録プロセスの最後に、アプリ パスワードを作成するためのオプションが表示されます。

アプリ パスワードは、Azure Portal や Office 365 ポータルで設定を変更することで、登録の後で作成することもできます。 アプリ パスワードの詳細と、ユーザーが実行する手順については、「[Azure Multi-Factor Authentication のアプリ パスワードとは](./end-user/multi-factor-authentication-end-user-app-passwords.md)」を参照してください。

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>ユーザーが信頼するデバイスに対する Multi-Factor Authentication を記憶する
ユーザーが信頼するデバイスとブラウザーに対する Multi-Factor Authentication の記憶は、すべての MFA ユーザー向けの無料の機能です。 この設定により、ユーザーが MFA を使用して正常にサインインした後、一定の日数の間、MFA を省略するオプションが得られます。 これにより、ユーザーが同じデバイスで 2 段階認証を実行する回数を最小限に抑えることで使いやすさを強化できます。

ただし、アカウントまたはデバイスが侵害された場合、信頼できるデバイスに対する MFA の記憶はセキュリティに影響する可能性があります。 企業アカウントが侵害された場合や、信頼済みデバイスを紛失したり盗難に遭ったりした場合は、[Multi-Factor Authentication をすべてのデバイスで復元](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user)する必要があります。 この操作により、信頼された状態がすべてのデバイスから失われ、ユーザーは 2 段階認証を再度実行する必要があります。 また、「[2 段階認証設定の管理](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)」に書かれている手順で各自のデバイスの MFA を復元するようユーザーに指示してください。

### <a name="how-it-works"></a>動作のしくみ

Multi-Factor Authentication の記憶機能は、ブラウザーでユーザーがサインイン時に "今後 **X** 日間はこのメッセージを表示しない" というチェック ボックスをオンにしたときに永続的な Cookie を設定することによって実現されています。 この場合、Cookie の有効期限が切れるまでは、同じブラウザーからユーザーが MFA を求められることはありません。 そのユーザーが同じデバイスで異なるブラウザーを開くか、または Cookie をクリアした場合は、再度、認証が求められます。 

非ブラウザー アプリでは、先進認証がサポートされているかどうかに関係なく、"今後 **X** 日間はこのメッセージを表示しない" チェック ボックスは表示されません。 そうしたアプリでは、新しいアクセス トークンが 1 時間おきに支給される更新トークンが使用されます。 更新トークンの検証時に、前回の 2 段階認証が、設定されている日数内に実行されたことが Azure AD によってチェックされます。 

そのため、信頼済みデバイスで MFA を記憶した場合、(通常は毎回認証メッセージが表示される) Web アプリでは認証回数が減りますが、(通常は 90 日おきに認証メッセージが表示される) 先進認証クライアントでは認証回数が増えます。

> [!NOTE]
>Azure MFA サーバーまたはサードパーティの MFA ソリューションを介して AD FS の 2 段階認証を行うとき、Multi-Factor Authentication の記憶機能は、AD FS の "サインインしたままにする" 機能とは互換性がありません。 ユーザーが AD FS の "サインインしたままにする" を選択したうえでさらに、MFA に対して信頼済みデバイスとして指定した場合、"MFA の記憶" 日数が過ぎると本人確認ができなくなります。 Azure AD からは新たに 2 段階認証を行うよう要求されますが、AD FS からは、2 段階認証を再実行するように求められるのではなく、元の MFA クレームと日付を含んだトークンが返されます。 その結果、Azure AD と AD FS との間で本人確認がループ状態に陥ります。 

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication の記憶を有効にする
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側で、**[Azure Active Directory]** > **[ユーザーとグループ]** > **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. [サービス設定] ページの **[Multi-Factor Authentication を記憶する]** で、**[信頼済みデバイスでユーザーが多要素認証を記憶できるようにする]** をオンにします。

   ![デバイスの記憶](./media/multi-factor-authentication-whats-next/remember.png)

6. 信頼済みデバイスで 2 段階認証のバイパスを許可する日数を設定します。 既定値は 14 日です。
7. **[保存]** を選択します。

### <a name="mark-a-device-as-trusted"></a>デバイスを信頼済みとマークする

この機能を有効にすると、ユーザーは、サインイン時に **[今後このメッセージを表示しない]** をオンにすることで、デバイスを信頼済みとしてマークできます。

![今後このメッセージを表示しない - スクリーンショット](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>選択可能な検証方法
ユーザーが使用できる検証方法を選択できます。 次の表で、各方法の概要を説明します。

ユーザーは、自分のアカウントを MFA 用に登録するときに、有効になっているオプションの中から、使用する検証方法を選択します。 登録プロセスのガイダンスについては、「[アカウントへの 2 段階認証の設定](multi-factor-authentication-end-user-first-time.md)」を参照してください。

| 方法 | 説明 |
|:--- |:--- |
| 電話の呼び出し |自動音声通話を行います。 ユーザーは、呼び出しに応答し、電話のキーパッドの # を押して認証を行います。 この電話番号は、オンプレミスの Active Directory には同期されません。 |
| 電話へのテキスト メッセージ |確認コードを含むテキスト メッセージを送信します。 ユーザーは、この確認コードをサインイン インターフェイスに入力するように求められます。 このプロセスを一方向の SMS といいます。 双方向の SMS は、ユーザーが特定のコードを返信する必要があることを意味します。 双方向の SMS は廃止されており、2018 年 11 月 14 日時点でサポートされなくなります。 双方向 SMS を構成しているユーザーは、その時点で、"電話の呼び出し" 認証に自動的に切り替わります。|
| モバイル アプリでの通知 |電話または登録されたデバイスにプッシュ通知を送信します。 ユーザーは通知を表示し、**[確認]** を選択して認証を完了します。 <br>Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。 |
| モバイル アプリからの確認コード |Microsoft Authenticator アプリは、30 秒ごとに新しい OATH 確認コードを生成します。 ユーザーは、この確認コードをサインイン インターフェイスに入力します。<br>Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。 |

### <a name="how-to-enabledisable-authentication-methods"></a>認証方法を有効または無効にする方法
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側で、**[Azure Active Directory]** > **[ユーザーとグループ]** > **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. [サービスの設定] ページの **[検証オプション]** で、使用するオプションをオンまたはオフにします。

   ![検証オプション](./media/multi-factor-authentication-whats-next/authmethods.png)

6. **[Save]** をクリックします。
