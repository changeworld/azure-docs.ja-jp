---
title: Azure Active Directory ポータルのサインイン アクティビティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのサインイン アクティビティ レポートの概要
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/09/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 256194d8b0b5e6b08210e9338d945774603ac328
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429805"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのサインイン アクティビティ レポート

Azure Active Directory (Azure AD) のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** – マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報。
    - **監査ログ** - [監査ログ](concept-audit-logs.md)は、ユーザーとグループの管理や、マネージド アプリケーションとディレクトリのアクティビティに関するシステム アクティビティ情報を提供します。
- **セキュリティ** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](concept-risky-sign-ins.md)は、ユーザー アカウントの正当な所有者ではないユーザーによるサインイン試行の指標です。
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](concept-user-at-risk.md)は、侵害された可能性があるユーザー アカウントの指標です。

この記事では、サインイン レポートの概要について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。

* セキュリティ管理者、セキュリティ閲覧者、グローバル閲覧者、およびレポート閲覧者ロールのユーザー
* グローバル管理者
* 任意のユーザー (非管理者) が自分のサインインにアクセス可能 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>サインイン アクティビティにアクセスするために必要な Azure AD ライセンスを教えてください。

* すべてのサインイン アクティビティ レポートを閲覧するためには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。 アップグレード前のアクティビティ データがない状態でプレミアム ライセンスにアップグレードした後、データがレポートに表示されるまでには数日間かかります。

## <a name="sign-ins-report"></a>サインイン レポート

ユーザーのサインイン レポートは、次の質問に対する回答を提示します。

* ユーザーのサインインにどのようなパターンがあるか。
* 1 週間で何人のユーザーがサインインを行ったか。
* これらのサインインはどのような状態か。

[[Azure portal]](https://portal.azure.com) メニューで **[Azure Active Directory]** を選択するか、任意のページから **[Azure Active Directory]** を検索して選択します。

![[Azure Active Directory] を選択する](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

**[監視]** で **[サインイン]** を選択して、[サインイン レポート](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)を開きます。

![サインイン アクティビティ](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "サインイン アクティビティ")

一部のサインイン レコードがポータルに表示されるまでに、最大 2 時間かかることがあります。

> [!IMPORTANT]
> サインイン レポートには、**対話型**のサインイン、つまりユーザーがユーザー名とパスワードを使用して手動で行うサインインのみが表示されます。 サービス間の認証のような対話型ではないサインインは、サインイン レポートに表示されません。 

サインイン ログには、次のものを示す既定のリスト ビューがあります。

- サインインの日付
- 関連するユーザー
- ユーザーがサインインしたアプリケーション
- サインインの状態
- リスク検出の状態
- 多要素認証 (MFA) の要件の状態

![サインイン アクティビティ](./media/concept-sign-ins/sign-in-activity.png "サインイン アクティビティ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![サインイン アクティビティ](./media/concept-sign-ins/19.png "サインイン アクティビティ")

**[列]** ダイアログでは、選択可能な属性にアクセスできます。 サインイン レポートでは、特定のサインイン要求に対して複数の値を持つフィールドを列として使用することはできません。 これは、認証の詳細、条件付きアクセス データ、ネットワークの場所などが該当します。   

![サインイン アクティビティ](./media/concept-sign-ins/columns.png "サインイン アクティビティ")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![サインイン アクティビティ](./media/concept-sign-ins/basic-sign-in.png "サインイン アクティビティ")

> [!NOTE]
> すべてのサインイン レポートで、条件付きアクセス ポリシーのトラブルシューティングを実行できるようになりました。 サインイン レコードの **[条件付きアクセス]** タブをクリックして、条件付きアクセスの状態を確認し、サインインに適用されたポリシーの詳細と各ポリシーの結果を調べることができます。
> 詳細については、「[Frequently asked questions about CA information in all sign-ins](reports-faq.md#conditional-access)」 (すべてのサインインの CA 情報に関してよく寄せられる質問) を参照してください。



## <a name="filter-sign-in-activities"></a>サインイン アクティビティのフィルター処理

まず、報告されたデータを、自分に適したレベルまで絞り込みます。 次に、既定のフィルターとして [日付] フィールドを使用したサインイン データをフィルター処理します。 Azure AD では、さまざまな追加のフィルターを設定できます。

![サインイン アクティビティ](./media/concept-sign-ins/04.png "サインイン アクティビティ")

**[ユーザー]** フィルターでは、確認したいユーザーの名前またはそのユーザー プリンシパル名 (UPN) を指定できます。

**[アプリケーション]** フィルターでは、確認したいアプリケーションの名前を指定できます。

**[サインイン状態]** フィルターでは、次のいずれかを選択できます。

- All
- Success
- 障害

**条件付きアクセス** フィルターでは、次に示すサインインの CA ポリシーの状態を選択できます。

- All
- 未適用
- Success
- 障害

**[日付]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム期間

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

サインイン ビューにフィールドを追加すると、これらのフィールドがフィルターの一覧に自動的に追加されます。 たとえば、 **[クライアント アプリ]** フィールドを一覧に追加した場合、次のフィルターを設定できるもう 1 つのフィルター オプションが表示されます。  
![サインイン アクティビティ](./media/concept-sign-ins/12.png "サインイン アクティビティ")

- **ブラウザー**  
    このフィルターは、ブラウザー フローを使用してサインインが試行されたすべてのイベントを表示します。
- **Exchange ActiveSync (サポート対象)**  
    このフィルターは、iOS、Android、Windows Phone など、サポートされているプラットフォームから Exchange ActiveSync (EAS) プロトコルが試行されているすべてのサインイン試行を表示します。
- **Exchange ActiveSync (サポート対象外)**  
    このフィルターは、Linux ディストリビューションのようなサポートされないプラットフォームから EAS プロトコルが試行されているすべてのサインイン試行を表示します。
- **モバイル アプリとデスクトップ クライアント**。このフィルターは、ブラウザー フローを使用していなかったすべてのサインイン試行を表示します。 たとえば、任意のプロトコルを使用する任意のプラットフォームからのモバイル アプリや、Windows または MacOS 上の Office のようなデスクトップ クライアント アプリなどです。
  
- **その他のクライアント**
    - **IMAP**  
        IMAP を使用して電子メールを取得する従来のメール クライアント。
    - **MAPI**  
        Office 2013。ADAL が有効になっており、MAPI が使用されています。
    - **以前のバージョンの Office クライアント**  
        ADAL が有効になっておらず、MAPI が使用されている既定の構成の Office 2013。あるいは、ADAL が無効になっている Office 2016。
    - **POP**  
        POP3 を使用して電子メールを取得する従来のメール クライアント。
    - **SMTP**  
        SMTP を使用して電子メールを送信する従来のメール クライアント。

## <a name="download-sign-in-activities"></a>サインイン アクティビティのダウンロード

**[ダウンロード]** オプションをクリックして、最新の 250,000 件のレコードを含む CSV または JSON ファイルを作成します。 Azure portal の外部で操作する場合は、まず[サインイン データをダウンロード](quickstart-download-sign-in-report.md)します。  

![ダウンロード](./media/concept-sign-ins/71.png "ダウンロード")

> [!IMPORTANT]
> ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。  


## <a name="sign-ins-data-shortcuts"></a>サインイン データのショートカット

Azure AD と Azure portal には両方とも、サインイン データへの追加のエントリ ポイントが用意されています。

- ID のセキュリティ保護の概要
- ユーザー
- グループ
- エンタープライズ アプリケーション

### <a name="users-sign-ins-data-in-identity-security-protection"></a>ID セキュリティ保護のユーザーのサインイン データ

**[ID のセキュリティ保護]** の概要ページのユーザー サインイン グラフは、サインインの週単位の集計を示します。期間の既定値は 30 日です。

![サインイン アクティビティ](./media/concept-sign-ins/06.png "サインイン アクティビティ")

サインイン グラフ内の日付をクリックすると、その日のサインイン アクティビティの概要が表示されます。

サインイン アクティビティ リストの各行には、次の情報が表示されます。

* サインインしたのはだれか。
* サインインの対象となったのはどのアプリケーションか。
* サインインの状態はどのような状態か。
* サインインの MFA 状態はどのような状態か。

項目をクリックすると、サインイン操作の詳細が表示されます。

- User ID
- User
- ユーザー名
- アプリケーション ID
- Application
- Client
- Location
- IP アドレス
- Date
- MFA が必要
- サインインの状態

> [!NOTE]
> IP アドレスは、IP アドレスとそのアドレスを持つコンピューターの物理的な配置場所との間に明確な関連性がないような方法で発行されます。 IP アドレスのマッピングは、多くの場合、クライアント デバイスの実際の使用場所から遠く離れたところにある中央プールから、モバイル プロバイダーや VPN が IP アドレスを発行しているという事実によって、複雑になります。 現在の Azure AD レポートでは、IP アドレスの物理的な場所の変換は、トレース、レジストリ データ、逆引き参照、およびその他の情報に基づくベスト エフォートで対応されています。

**[ユーザー]** ページの **[アクティビティ]** セクションの **[サインイン]** をクリックすると、すべてのユーザー サインインの完全な概要が表示されます。

![サインイン アクティビティ](./media/concept-sign-ins/08.png "サインイン アクティビティ")

## <a name="usage-of-managed-applications"></a>マネージド アプリケーションの使用状況

アプリケーションを中心にしてサインイン データを表示すると、次のような疑問に答えることができます。

* アプリケーションをだれが使用しているか。
* 組織内の上位 3 つのアプリケーションはどれか。
* 最新のアプリケーションはどのように実行されるか。

このデータへのエントリ ポイントとなるのは、組織内の上位 3 つのアプリケーションです。 データは **[エンタープライズ アプリケーション]** の下の **[概要]** セクションにある、過去 30 日間のレポートに含まれています。

![サインイン アクティビティ](./media/concept-sign-ins/10.png "サインイン アクティビティ")

アプリ使用状況グラフは、特定の期間の上位 3 つのアプリケーションのサインインを週単位で集計します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/concept-sign-ins/graph-chart.png "サインイン アクティビティ")

必要に応じて、特定のアプリケーションにフォーカスを設定できます。

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "レポーティング")

アプリ使用状況グラフ内の日付をクリックすると、サインイン アクティビティの詳細な一覧が表示されます。

**[サインイン]** オプションを使用すると、アプリケーションへのすべてのサインイン イベントの完全な概要を表示できます。

## <a name="office-365-activity-logs"></a>Office 365 のアクティビティ ログ

Office 365 のアクティビティ ログは、[Microsoft 365 管理センター](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)から確認できます。 Office 365 アクティビティ ログと Azure AD アクティビティ ログでは、多くのディレクトリ リソースが共有される点について考えてみましょう。 Office 365 のアクティビティ ログを完全に表示できるのは、Microsoft 365 管理センターだけです。 

[Office 365 Management API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) を使用すると、Office 365 のアクティビティ ログにプログラムでアクセスすることもできます。

## <a name="next-steps"></a>次のステップ

* [サインイン アクティビティ レポートのエラー コード](reference-sign-ins-error-codes.md)
* [Azure AD のデータ保有ポリシー](reference-reports-data-retention.md)
* [Azure AD のレポート待機時間](reference-reports-latencies.md)

