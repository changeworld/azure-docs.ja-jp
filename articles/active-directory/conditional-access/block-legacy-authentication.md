---
title: レガシ認証をブロックする - Azure Active Directory
description: Azure AD 条件付きアクセスを使用してレガシ認証をブロックすることでセキュリティ体制を強化する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e657114f4e51775fb73267322a48e362f57b1c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641693"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>方法:条件付きアクセスを使用して Azure AD へのレガシ認証をブロックする   

指定したユーザーがお使いのクラウド アプリに簡単にアクセスできるように、Azure Active Directory (Azure AD) ではレガシ認証を含め、幅広い認証プロトコルをサポートしています。 ただし、従来のプロトコルでは、多要素認証 (MFA) をサポートしていません。 MFA は多くの環境で、なりすましに対処するための一般的な要件になっています。 

Microsoft の ID セキュリティの担当部長である Alex Weinert は、2020 年 3 月 12 日のブログ投稿の「[組織内のレガシ認証をブロックする新しいツール](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#)」で、組織がレガシ認証をブロックする理由と、このタスクを実現するために Microsoft が提供する追加のツールについて強調しています。

> MFA を有効にするには、レガシ認証をブロックする必要もあります。 これは、POP、SMTP、IMAP、MAPI などの従来の認証プロトコルでは、MFA を強制することができないため、敵対者による組織の攻撃において優先するエントリ ポイントにするためです...
> 
>...Azure Active Directory (Azure AD) トラフィックの分析によるレガシ認証の数は著しいです。
> 
> - 99% を超えるパスワード スプレー攻撃がレガシ認証プロトコルを使用します
> - 97% を超える資格情報を含めた攻撃がレガシ認証を使用します
> - レガシ認証を無効にしている組織の Azure AD アカウントは、レガシ認証が有効になっている場合よりも侵害が 67% 低下します
>

お使いの環境で、テナントの保護を向上させるためにレガシ認証をブロックする準備ができている場合は、条件付きアクセスによってこの目標を達成できます。 この記事では、テナントのレガシ認証をブロックする条件付きアクセス ポリシーを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、次の内容を熟知していることを前提としています。 

- Azure AD の条件付きアクセスの[基本的な概念](overview.md) 
- Azure portal における条件付きアクセス ポリシーの構成に関する[ベスト プラクティス](best-practices.md)

## <a name="scenario-description"></a>シナリオの説明

Azure AD では、レガシ認証を含め、最も広く使用されている認証および承認のプロトコルを複数サポートしています。 レガシ認証とは、基本認証を使用するプロトコルのことです。 通常、これらのプロトコルでは、第 2 要素の認証はどれも適用できません。 レガシ認証に基づくアプリの例を次に示します。

- 従来の Microsoft Office アプリ
- POP、IMAP、SMTP などの電子メール プロトコルを使用するアプリ

最近は、単一要素認証 (たとえば、ユーザー名とパスワード) では不十分です。 パスワードは、簡単に推測できるうえに、適切なパスワードの選定は難しいため、不十分です。 また、パスワードは、フィッシングやパスワード スプレーなどの幅広い攻撃に対しては脆弱です。 パスワードの脅威から保護できる最も簡単な方法の 1 つは、MFA を実装することです。 MFA を使用すれば、攻撃者がユーザーのパスワードを入手したとしても、認証に成功してデータにアクセスするには、パスワードだけでは不十分です。

レガシ認証を使用しているアプリからはテナントのリソースにアクセスできないようにするには、どうしたらいいでしょうか。 条件付きアクセス ポリシーを使用して、それらのアプリをブロックすることをお勧めします。 必要に応じて、特定のユーザーと特定のネットワークの場所だけに、レガシ認証に基づいたアプリの使用を許可します。

条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 つまり、条件付きアクセスは、サービス拒否 (DoS) 攻撃などのシナリオの防御の最前線を意図したものではありませんが、これらのイベントのシグナル (サインインのリスク レベル、要求の場所など) を利用してアクセスを判別できます。

## <a name="implementation"></a>実装

このセクションでは、レガシ認証をブロックするために条件付きアクセス ポリシーを構成する方法について説明します。 

### <a name="legacy-authentication-protocols"></a>レガシ認証プロトコル

次のオプションは、レガシ認証プロトコルと見なされます。

- 認証済み SMTP - 電子メール メッセージを送信するために POP および IMAP クライアントで使用されます。
- 自動検出 - Exchange Online でメールボックスを検索して接続するために Outlook および EAS のクライアントで使用されます。
- Exchange Online PowerShell - リモート PowerShell を使用して Exchange Online に接続するために使用されます。 Exchange Online PowerShell の基本認証をブロックする場合は、Exchange Online PowerShell モジュールを使用して接続する必要があります。 手順については、「[多要素認証を使用して Exchange Online PowerShell に接続する](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)」を参照してください。
- Exchange Web サービス (EWS) - Outlook、Outlook for Mac、およびサードパーティ製アプリによって使用されるプログラミング インターフェイスです。
- IMAP4 - IMAP 電子メール クライアントで使用されます。
- MAPI over HTTP (MAPI/HTTP) - Outlook 2010 以降で使用されます。
- オフライン アドレス帳 (OAB) - Outlook によってダウンロードおよび使用されるアドレス一覧コレクションのコピーです。
- Outlook Anywhere (RPC over HTTP) - Outlook 2016 以前で使用されます。
- Outlook サービス - Windows 10 用のメール/カレンダー アプリで使用されます。
- POP3 - POP 電子メール クライアントで使用されます。
- レポート Web サービス - Exchange Online でレポート データを取得するために使用されます。
- その他のクライアント - レガシ認証を利用していると識別された他のプロトコル。

これらの認証プロトコルとサービスの詳細については、[Azure Active Directory ポータルのサインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)に関するページを参照してください。

### <a name="identify-legacy-authentication-use"></a>レガシ認証の使用を識別する

ディレクトリでレガシ認証をブロックするには、その前にまず、レガシ認証を使用するアプリをユーザーが保有しているかどうかや、それがディレクトリ全体にどのような影響を与えるかを把握する必要があります。 Azure AD のサインイン ログを使用すると、レガシ認証を使用しているかどうかを把握できます。

1. **[Azure portal]**  >  **[Azure Active Directory]**  >  **[サインイン]** に移動します。
1. [Client App] (クライアント アプリ) 列が表示されていない場合は、 **[列]**  >  **[Client App] (クライアント アプリ)** をクリックしてその列を追加します。
1. **[フィルターの追加]**  >  **[クライアント アプリ]** > と進み、すべてのレガシ認証プロトコルを選択し、 **[適用]** をクリックします。

フィルター処理によって、レガシ認証プロトコルによって行われたサインイン試行のみが表示されます。 個々のサインイン試行をクリックすると、追加の詳細が表示されます。 **[Basic Info] (基本情報)** タブの下の **[Client App] (クライアント アプリ)** フィールドには、どのレガシ認証プロトコルが使用されたかが表示されます。

これらのログは、どのユーザーがまだレガシ認証に依存しているか、およびどのアプリケーションがレガシ プロトコルを使用して認証要求を行っているかを示しています。 これらのログに表示されず、レガシ認証を使用していないことが確認されたユーザーに対してのみ、条件付きアクセス ポリシーを実装します。

### <a name="block-legacy-authentication"></a>レガシ認証をブロックする 

条件付きアクセス ポリシーでは、リソースへのアクセスに使用するクライアント アプリに関連付ける条件を設定できます。 クライアント アプリの条件を使用すると、 **[モバイル アプリとデスクトップ クライアント]** で **[Exchange ActiveSync クライアント]** と **[その他のクライアント]** を選択して、レガシ認証を使用しているアプリに対象範囲を絞り込むことができます。

![その他のクライアント](./media/block-legacy-authentication/01.png)

これらのアプリへのアクセスをブロックするには、 **[アクセスのブロック]** を選択する必要があります。

![アクセスのブロック](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>ユーザーとクラウド アプリを選択する

組織へのレガシ認証をブロックする場合、恐らくは、以下を選択してこれを行うことを検討するでしょう。

- すべてのユーザー
- すべてのクラウド アプリ
- アクセスのブロック

![代入](./media/block-legacy-authentication/03.png)

この構成は、条件付きアクセス ポリシーの[ベスト プラクティス](best-practices.md)に違反するので、Azure には、このようなポリシーを作成しないための安全機能が用意されています。
 
![サポートされていないポリシー構成](./media/block-legacy-authentication/04.png)

"*すべてのユーザーとすべてのクラウド アプリのブロック*" では、組織全体をテナントへのサインオンからブロックする可能性があるため、安全機能が必要です。 ベスト プラクティスの最小要件を満たすには、少なくとも 1 人のユーザーを除外する必要があります。 ディレクトリ ロールを除外することもできます。

![サポートされていないポリシー構成](./media/block-legacy-authentication/05.png)

1 人のユーザーをポリシーから除外することで、この安全機能を利用できます。 理想的には、数個の [Azure AD の緊急アクセス管理アカウント](../users-groups-roles/directory-emergency-access.md)を定義し、それらのアカウントをポリシーから除外します。

ポリシーを有効にしてレガシ認証をブロックするときに[レポートのみのモード](concept-conditional-access-report-only.md)を使用すると、ポリシーの影響を監視する機会が組織に与えられます。

## <a name="policy-deployment"></a>ポリシーのデプロイ

ポリシーを運用環境に導入する前に、次の点に注意します。
 
- **サービス アカウント** -サービス アカウントとして使用されるユーザー アカウント、または会議室の電話などのデバイスによって使用されるユーザー アカウントを特定します。 これらのアカウントに必ず強固なパスワードを設定して、除外されるグループに追加します。
- **サインイン レポート** - サインイン レポートを確認して、**その他のクライアント**のトラフィックを検索します。 最上位の使用率を特定して、なぜそれが使用されているのかを調査します。 通常、トラフィックは、最新の認証を使用していない従来の Office クライアント、またはサードパーティの電子メール アプリによって生成されています。 これらのアプリからの使用がなくなるように、計画を立てます。あるいは、影響が少ない場合は、これらのアプリを今後は使用できないことをユーザーに通知します。
 
詳細については、「[新しいポリシーはどのように展開する必要がありますか。](best-practices.md#how-should-you-deploy-a-new-policy)」を参照してください。

## <a name="what-you-should-know"></a>知っておくべきこと

**その他のクライアント**を使用しているアクセスをブロックすると、基本認証を使用している Exchange Online PowerShell および Dynamics 365 もブロックされます。

**その他のクライアント**を対象とするポリシーを構成すると、SPConnect などの特定のクライアントから組織全体がブロックされます。 このブロックは、旧バージョンのクライアントが想定していない方法で認証されるために発生します。 この問題は、古い Office クライアントなどの重要な Office アプリケーションには適用されません。

ポリシーが有効になるまで、最大で 24 時間かかる可能性があります。

**他のクライアント**条件には、利用可能なすべての制御の許可を選択できます。ただし、エンドユーザーのエクスペリエンスは常に同じ、つまり、アクセスがブロックされます。

**他のクライアント**条件を使用してレガシ認証をブロックする場合は、デバイスのプラットフォームと場所の条件も設定できます。 たとえば、モバイル デバイスに対する従来の認証をブロックすることだけが必要な場合は、以下を選択して**デバイス プラットフォーム**の条件を設定します。

- Android
- iOS
- Windows Phone

![サポートされていないポリシー構成](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>次のステップ

- [条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-report-only.md)
- 条件付きアクセス ポリシー構成についてまだよくご存知でない場合は、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」で例を参照してください。
- 先進認証のサポートの詳細については、「[How modern authentication works for Office 2013 and Office 2016 client apps](/office365/enterprise/modern-auth-for-office-2013-and-2016)」 (Office 2013 クライアント アプリと Office 2016 クライアント アプリでの先進認証のしくみ) を参照してください 
- [Office 365 および Microsoft 365 を使用して電子メールを送信するように多機能機器またはアプリケーションを設定する方法](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
