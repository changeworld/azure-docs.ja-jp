---
title: レガシ認証をブロックする - Azure Active Directory
description: Azure AD 条件付きアクセスを使用してレガシ認証をブロックすることでセキュリティ体制を強化する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/12/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, dawoo, jebeckha, grtaylor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 707e5b1e2c9adbe3b30a73a75e89ae7cf7a1beab
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520771"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>方法:条件付きアクセスを使用して Azure AD へのレガシ認証をブロックする   

指定したユーザーがお使いのクラウド アプリに簡単にアクセスできるように、Azure Active Directory (Azure AD) ではレガシ認証を含め、幅広い認証プロトコルをサポートしています。 ただし、レガシ認証は、多要素認証 (MFA) をサポートしていません。 MFA は多くの環境で、なりすましに対処するための一般的な要件になっています。 

> [!NOTE]
> 2022 年 10 月 1 日より、使用状況に関わらず、すべての Microsoft 365 テナントで、SMTP 認証を除き、Exchange Online の基本認証を恒久的に無効にする作業を開始する予定です。

Microsoft Identity Security のディレクター、Alex Weinert は 2020 年 3 月 12 日のブログ記事[「New tools to block legacy authentication in your organization」](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) (レガシー認証をブロックするための新しいツール) で、組織がレガシ認証をブロックするべき理由を強調し、それに役立つ Microsoft の他のツールを紹介しています。

> MFA を有効にするには、レガシ認証をブロックする必要もあります。 これは、POP、SMTP、IMAP、MAPI などの従来の認証プロトコルでは、MFA を強制することができないため、敵対者による組織の攻撃において優先するエントリ ポイントにするためです...
> 
>...Azure Active Directory (Azure AD) トラフィックの分析によるレガシ認証の数は著しいです。
> 
> - 99% を超えるパスワード スプレー攻撃がレガシ認証プロトコルを使用します
> - 97% を超える資格情報を含めた攻撃がレガシ認証を使用します
> - レガシ認証を無効にしている組織の Azure AD アカウントは、レガシ認証が有効になっている場合よりも侵害が 67% 低下します
>

お使いの環境で、テナントの保護を向上させるためにレガシ認証をブロックする準備ができている場合は、条件付きアクセスによってこの目標を達成できます。 この記事では、テナント内のすべてのワークロードのレガシ認証をブロックする条件付きアクセス ポリシーを構成する方法について説明します。 

レガシ認証のブロック保護を展開する場合は、すべてのユーザーに対して一度に無効にせずに、段階的なアプローチをお勧めします。 顧客は、まず Exchange Online 認証ポリシーを活用して、プロトコルごとに基本認証の無効化を開始し、(必要に応じて) 準備ができたら条件付きアクセス ポリシーを使用してレガシ認証をブロックすることを選ぶかもしれません。

条件付きアクセスに対応しているライセンスがない顧客は、[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md) を利用してレガシ認証をブロックできます。

## <a name="prerequisites"></a>前提条件

この記事では、Azure AD 条件付きアクセスの[基本的な概念](overview.md)を理解していることを前提としています。

> [!NOTE]
> 条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 条件付きアクセスはサービス拒否 (DoS) 攻撃などのシナリオに対する組織の防御の最前線を意図したものではありませんが、これらのイベントからのシグナルを使用してアクセス権を判定できます。

## <a name="scenario-description"></a>シナリオの説明

Azure AD では、レガシ認証を含め、最も広く使用されている認証および承認のプロトコルを複数サポートしています。 レガシ認証とは、ユーザー名とパスワード情報を収集するために広く使用されている業界標準の方法である基本認証を指します。 通常、レガシ認証クライアントでは、どの種類の第 2 要素認証も適用されません。 通常は、レガシ認証を使用する、または、これのみを使用するアプリケーションの例を次に示します。

- Microsoft Office 2013 以前。
- POP、IMAP、SMTP 認証などの電子メール プロトコルを使用するアプリ。

Office に対する先進認証のサポートの詳細については、[Office クライアント アプリで先進認証のしくみ](/microsoft-365/enterprise/modern-auth-for-office-2013-and-2016?view=o365-worldwide)に関するページを参照してください。

最近は、単一要素認証 (たとえば、ユーザー名とパスワード) では不十分です。 パスワードは、簡単に推測できるうえに、適切なパスワードの選定は難しいため、不十分です。 また、パスワードには、フィッシングやパスワード スプレーなどのさまざまな攻撃に対する脆弱性があります。 パスワードの脅威から保護するために実行できる最も簡単な方法の 1 つは、多要素認証 (MFA) を実装することです。 MFA を使用すれば、攻撃者がユーザーのパスワードを入手したとしても、認証に成功してデータにアクセスするには、パスワードだけでは不十分です。

レガシ認証を使用しているアプリからはテナントのリソースにアクセスできないようにするには、どうしたらいいでしょうか。 条件付きアクセス ポリシーを使用して、それらのアプリをブロックすることをお勧めします。 必要に応じて、特定のユーザーと特定のネットワークの場所だけに、レガシ認証に基づいたアプリの使用を許可します。

条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 つまり、条件付きアクセスは、サービス拒否 (DoS) 攻撃などのシナリオの防御の最前線を意図したものではありませんが、これらのイベントのシグナル (サインインのリスク レベル、要求の場所など) を利用してアクセスを判別できます。

## <a name="implementation"></a>実装

このセクションでは、レガシ認証をブロックするために条件付きアクセス ポリシーを構成する方法について説明します。 

### <a name="messaging-protocols-that-support-legacy-authentication"></a>レガシ認証をサポートするメッセージング プロトコル

次のメッセージング プロトコルは、レガシ認証をサポートしています。

- 認証済み SMTP - 認証された電子メール メッセージの送信に使用されます。
- 自動検出 - Exchange Online でメールボックスを検索して接続するために Outlook および EAS のクライアントで使用されます。
- Exchange ActiveSync (EAS) - Exchange Online のメールボックスに接続するために使用されます。
- Exchange Online PowerShell - リモート PowerShell を使用して Exchange Online に接続するために使用されます。 Exchange Online PowerShell の基本認証をブロックする場合は、Exchange Online PowerShell モジュールを使用して接続する必要があります。 手順については、「多要素認証[をConnect PowerShell Exchange Onlineする方法」を参照してください](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
- Exchange Web サービス (EWS) - Outlook、Outlook for Mac、およびサードパーティ製アプリによって使用されるプログラミング インターフェイスです。
- IMAP4 - IMAP 電子メール クライアントで使用されます。
- MAPI over HTTP (MAPI/HTTP) - Outlook 2010 SP2 以降で使用される、プライマリー メールボックス アクセス プロトコルです。
- オフライン アドレス帳 (OAB) - Outlook によってダウンロードおよび使用されるアドレス一覧コレクションのコピーです。
- Outlook Anywhere (RPC over HTTP) - Outlook のすべての現在のバージョンでサポートされているレガシ メールボックス アクセス プロトコルです。
- POP3 - POP 電子メール クライアントで使用されます。
- レポート Web サービス - Exchange Online でレポート データを取得するために使用されます。
- Universal Outlook - Windows 10 用のメール/カレンダー アプリで使用されています。
- その他のクライアント - レガシ認証を利用していると識別された他のプロトコル。

これらの認証プロトコルとサービスの詳細については、[Azure Active Directory ポータルのサインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)に関するページを参照してください。

### <a name="identify-legacy-authentication-use"></a>レガシ認証の使用を識別する

ディレクトリでレガシ認証をブロックするには、その前にまず、レガシ認証を使用するアプリをユーザーが保有しているかどうかや、それがディレクトリ全体にどのような影響を与えるかを把握する必要があります。 Azure AD のサインイン ログを使用すると、レガシ認証を使用しているかどうかを把握できます。

1. **[Azure portal]**  >  **[Azure Active Directory]**  >  **[サインイン]** に移動します。
1. [Client App] (クライアント アプリ) 列が表示されていない場合は、 **[列]**  >  **[Client App] (クライアント アプリ)** をクリックしてその列を追加します。
1. **[フィルターの追加]** 、 **[クライアント アプリ]** の順に進み、すべてのレガシ認証プロトコルを選択します。 フィルター処理のダイアログ ボックスの外側を選択して選択を適用し、ダイアログ ボックスを閉じます。
1. [新しいサインイン アクティビティ レポート - プレビュー](../reports-monitoring/concept-all-sign-ins.md)をアクティブにしている場合は、 **[ユーザーのサインイン (非対話型)]** タブでも上記の手順を繰り返します。

フィルター処理によって、レガシ認証プロトコルによって行われたサインイン試行のみが表示されます。 各サインイン試行をクリックすると詳細が表示されます。 **[Basic Info] (基本情報)** タブの下の **[Client App] (クライアント アプリ)** フィールドには、どのレガシ認証プロトコルが使用されたかが表示されます。

これらのログは、どのユーザーがまだレガシ認証に依存しているか、およびどのアプリケーションがレガシ プロトコルを使用して認証要求を行っているかを示しています。 これらのログに表示されず、レガシ認証を使用していないことが確認されたユーザーに対してのみ、条件付きアクセス ポリシーを実装します。

## <a name="block-legacy-authentication"></a>レガシ認証をブロックする 

条件付きアクセス ポリシーを使用してレガシ認証をブロックするには、2 つの方法があります。

- [レガシ認証を直接ブロックする](#directly-blocking-legacy-authentication)
- [レガシ認証を間接的にブロックする](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>レガシ認証を直接ブロックする

組織全体でレガシ認証をブロックする最も簡単な方法は、レガシ認証クライアントに特化して適用され、アクセスをブロックする条件付きアクセス ポリシーを構成することです。 ユーザーとアプリケーションをポリシーに割り当てる際には、まだレガシ認証を使用してサインインする必要があるユーザーとサービス アカウントを必ず除外してください。 このポリシーを適用するクラウド アプリを選択する場合は、すべてのクラウド アプリ、Office 365 (推奨) などの対象アプリ、または少なくとも Office 365 Exchange Online を選択します。 クライアント アプリの条件を構成するには、 **[Exchange ActiveSync クライアント]** と **[他のクライアント]** を選択します。 これらのクライアント アプリのアクセスをブロックするには、アクセスをブロックするようにアクセスの制御を構成します。

![レガシ認証をブロックするように構成されたクライアント アプリの条件](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>レガシ認証を間接的にブロックする

組織において、組織全体でレガシ認証をブロックする準備ができていない場合でも、レガシ認証を使用するサインインで、多要素認証や、準拠している、または Hybrid Azure AD Join を使用したデバイスを要求するなどの許可コントロールを必要とするポリシーがバイパスされないようにする必要があります。 認証時、レガシ認証クライアントでは、Azure AD への MFA、デバイスのコンプライアンス、または参加状態の情報の送信はサポートされません。 したがって、すべてのクライアント アプリケーションに許可コントロールを含むポリシーを適用して、許可コントロールを満たすことができないレガシ認証ベースのサインインがブロックされるようにします。 2020 年 8 月にクライアント アプリの条件が一般提供されると、新しく作成された条件付きアクセス ポリシーは、既定ですべてのクライアント アプリに適用されます。

![クライアント アプリの条件での既定の構成](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>知っておくべきこと

条件付きアクセス ポリシーが有効になるまでに、最大 24 時間かかる場合があります。

**その他のクライアント** を使用しているアクセスをブロックすると、基本認証を使用している Exchange Online PowerShell および Dynamics 365 もブロックされます。

**その他のクライアント** を対象とするポリシーを構成すると、SPConnect などの特定のクライアントから組織全体がブロックされます。 このブロックは、旧バージョンのクライアントが想定していない方法で認証されるために発生します。 この問題は、古い Office クライアントなどの重要な Office アプリケーションには適用されません。

**他のクライアント** 条件には、利用可能なすべての制御の許可を選択できます。ただし、エンドユーザーのエクスペリエンスは常に同じ、つまり、アクセスがブロックされます。

### <a name="sharepoint-online"></a>SharePoint Online

ユーザーが従来の認証で SharePoint Online にアクセスすることを禁止するには、組織は `Set-SPOTenant` PowerShell コマンドを使用し、`-LegacyAuthProtocolsEnabled` パラメーターを `$false` に設定して SharePoint の従来の認証を無効にする必要があります。 このパラメーターの詳しい設定方法は、[Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant) に関する SharePoint PowerShell リファレンス ドキュメントにあります。

## <a name="next-steps"></a>次のステップ

- [条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-insights-reporting.md)
- 条件付きアクセス ポリシー構成についてまだよくご存知でない場合は、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](../authentication/tutorial-enable-azure-mfa.md)」で例を参照してください。
- 先進認証のサポートの詳細については、[Office クライアント アプリで先進認証のしくみ](/office365/enterprise/modern-auth-for-office-2013-and-2016)に関するページを参照してください。 
- [Microsoft 365 を使用して電子メールを送信するように多機能機器またはアプリケーションを設定する方法](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)
