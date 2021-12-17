---
title: Azure Active Directory のサインイン ログ | Microsoft Docs
description: Azure Active Directory のサインイン ログの概要。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/25/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d783fcd44fac0c22b83023e1e50b24c209fed8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577189"
---
# <a name="sign-in-logs-in-azure-active-directory"></a>Azure Active Directory のサインイン ログ

IT 管理者は、IT 環境がどのように動作しているかを知る必要があります。 システムの正常性に関する情報を使用すると、潜在的な問題に対処する必要があるかどうか、およびその方法を評価できます。 

この目標を達成するために、Azure Active Directory ポータルでは、次の 3 つのアクティビティ ログにアクセスできます。

- **[サインイン](concept-sign-ins.md)** - サインインとユーザーのリソース使用状況に関する情報。
- **[監査](concept-audit-logs.md)** - ユーザーやグループの管理、テナントのリソースに適用された更新など、テナントに適用された変更に関する情報。
- **[プロビジョニング](concept-provisioning-logs.md)** - ServiceNow でのグループの作成や、Workday からインポートされたユーザーなど、プロビジョニング サービスによって実行されるアクティビティ。

この記事では、サインイン レポートの概要について説明します。


## <a name="what-can-you-do-with-it"></a>できること

サインイン ログを見れば、例えば次のことが分かります。

- ユーザーのサインインにどのようなパターンがあるか。

- 1 週間で何人のユーザーがサインインを行ったか。

- これらのサインインはどのような状態か。


## <a name="who-can-access-it"></a>だれがアクセスできるのか。

このリンクを使用すれば、いつでも自身のサインイン履歴にアクセスできます: [https://mysignins.microsoft.com](https://mysignins.microsoft.com)

サインイン ログにアクセスするには、次の条件が必要です。

- グローバル管理者

- 次のいずれかのロールのユーザー
    - セキュリティ管理者

    - セキュリティ閲覧者

    - グローバル閲覧者

    - レポート閲覧者



## <a name="what-azure-ad-license-do-you-need"></a>必要な Azure AD ライセンス

サインイン アクティビティ レポートは、[Azure AD のすべてのエディション](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)で使用できます。 Azure Active Directory P1 または P2 ライセンスがある場合は、Microsoft Graph API からアクティビティ レポートにアクセスすることもできます。


## <a name="where-can-you-find-it-in-the-azure-portal"></a>Azure portal での閲覧方法

Azure portal でログにアクセスする方法はいくつかあります。 たとえば、[Azure Active Directory] メニューの **[監視]** セクションでログを開けます。  

![サインイン ログを開く](./media/concept-sign-ins/sign-ins-logs-menu.png)

また、次のリンクからサインイン ログに直接アクセスできます: [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)


## <a name="what-is-the-default-view"></a>既定の表示項目

サインイン ログには、次のものを示す既定のリスト ビューがあります。

- サインインの日付
- 関連するユーザー
- ユーザーがサインインしたアプリケーション
- サインインの状態
- リスク検出の状態
- 多要素認証 (MFA) の要件の状態

![スクリーンショットには、Office 365 SharePoint Online のサインインが示されています。](./media/concept-sign-ins/sign-in-activity.png "サインイン アクティビティ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![スクリーンショットには、[サインイン] ページの [列] オプションが示されています。](./media/concept-sign-ins/19.png "サインイン アクティビティ")

**[列]** ダイアログでは、選択可能な属性にアクセスできます。 サインイン レポートでは、特定のサインイン要求に対して複数の値を持つフィールドを列として使用することはできません。 これは、認証の詳細、条件付きアクセス データ、ネットワークの場所などが該当します。   

![スクリーンショットには、属性を選択できる [列] ダイアログ ボックスが示されています。](./media/concept-sign-ins/columns.png "サインイン アクティビティ")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![スクリーンショットには、詳細情報ビューが示されています。](./media/concept-sign-ins/basic-sign-in.png "サインイン アクティビティ")



## <a name="sign-in-error-code"></a>サインイン エラー コード

サインインに失敗した場合は、関連するログ項目の **[基本情報]** セクションで、理由に関する詳細情報を取得できます。 

![サインイン エラー コード](./media/concept-all-sign-ins/error-code.png)
 
ログ項目にはエラーの理由が表示されますが、[サインイン エラー ルックアップ ツール](https://login.microsoftonline.com/error)を使用して、さらに多くの情報が得られる場合があります。 たとえば、使用可能な場合は、このツールにより修復手順が通知されます。  

![エラー コード ルックアップ ツール](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>サインイン アクティビティのフィルター処理


ログのデータをフィルター処理して、必要な範囲まで絞り込めます。

![スクリーンショットには、[フィルターの追加] オプションが示されています。](./media/concept-sign-ins/04.png "サインイン アクティビティ")

**要求 ID** - 確認したい要求の ID です。

**ユーザー** - 確認したいユーザーの名前またはそのユーザー プリンシパル名 (UPN) です。

**アプリケーション** - ターゲット アプリケーションの名前です。
 
**状態** - 確認したいサインインの状態です。

- Success

- 障害

- 中断


**IP アドレス** - テナントへの接続に使用されたデバイスの IP アドレスです。

**場所** - 接続が開始された場所です。

- City

- 都道府県

- 国/リージョン


**リソース** - サインインに使用されたサービスの名前です。


**リソース ID** - サインインに使用されたサービスの ID です。


**クライアント アプリ** - テナントへの接続に使用されたクライアント アプリの種類です。

![クライアント アプリ フィルター](./media/concept-sign-ins/client-app-filter.png)


> [!NOTE]
> プライバシーに関するコミットメントにより、テナント間のシナリオの場合、Azure AD によってこのフィールドがホーム テナントに設定されることはありません。


|名前|先進認証|説明|
|---|:-:|---|
|認証済み SMTP| |電子メール メッセージを送信するために POP および IMAP のクライアントで使用されます。|
|自動検出| |Exchange Online でメールボックスを検索して接続するために Outlook および EAS のクライアントで使用されます。|
|Exchange ActiveSync| |このフィルターは、EAS プロトコルが試行されたすべてのサインイン試行を表示します。|
|Browser|![青のチェックマーク。](./media/concept-sign-ins/check.png)|Web ブラウザーを使用したユーザーのすべてのサインイン試行を表示します|
|Exchange ActiveSync| | Exchange ActiveSync を使用して Exchange Online に接続するクライアント アプリでのユーザーのすべてのサインイン試行を表示します|
|Exchange Online PowerShell| |リモート PowerShell を使用して Exchange Online に接続するために使用されます。 Exchange Online PowerShell の基本認証をブロックする場合は、Exchange Online PowerShell モジュールを使用して接続する必要があります。 手順については、「[多要素認証を使用して Exchange Online PowerShell に接続する](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)」を参照してください。|
|Exchange Web サービス| |Outlook、Outlook for Mac、およびサードパーティ製アプリによって使用されるプログラミング インターフェイスです。|
|IMAP4| |IMAP を使用して電子メールを取得する従来のメール クライアント。|
|MAPI over HTTP| |Outlook 2010 以降で使用されます。|
|モバイル アプリとデスクトップ クライアント|![青のチェックマーク。](./media/concept-sign-ins/check.png)|モバイル アプリとデスクトップ クライアントを使用したユーザーのすべてのサインイン試行を表示します。|
|オフライン アドレス帳| |Outlook によってダウンロードおよび使用されるアドレス一覧コレクションのコピーです。|
|Outlook Anywhere (RPC over HTTP)| |Outlook 2016 以降で使用されます。|
|Outlook サービス| |Windows 10 用のメール/カレンダー アプリで使用されます。|
|POP3| |POP3 を使用して電子メールを取得する従来のメール クライアント。|
|レポート Web サービス| |Exchange Online でレポート データを取得するために使用されます。|
|その他のクライアント| |クライアント アプリが含まれていない、または不明であるユーザーのサインインの試行をすべて表示します。|







**オペレーティング システム** - テナントへのサインオンに使用されたデバイスで実行されているオペレーティング システムです。 


**デバイス ブラウザー** - ブラウザーから接続が開始された場合、このフィールドを使用するとブラウザー名でフィルター処理できます。


**関連付け ID** - アクティビティの関連付け ID です。




**条件付きアクセス** - 適用されている条件付きアクセス規則の状態です

- **適用されていません**:サインイン中にポリシーがユーザーとアプリケーションに適用されていません。

- **成功**:サインイン中に 1 つ以上の条件付きアクセス ポリシーがユーザーとアプリケーションに適用されました (ただし、必ずしも他の条件が適用されたとは限りません)。 

- **失敗**:サインインによって少なくとも 1 つの条件付きアクセス ポリシーのユーザーとアプリケーションの条件が満たされたうえで、制御の許可が満たされていないか、またはアクセスをブロックするように設定されています。









## <a name="download-sign-in-activities"></a>サインイン アクティビティのダウンロード

**[ダウンロード]** オプションをクリックして、最新の 250,000 件のレコードを含む CSV または JSON ファイルを作成します。 Azure portal の外部で操作する場合は、まず[サインイン データをダウンロード](./howto-download-logs.md)します。  

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

![スクリーンショットには、1 か月のサインインのグラフが表示されています。](./media/concept-sign-ins/06.png "サインイン アクティビティ")

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
- 場所
- IP アドレス
- Date
- MFA が必要
- サインインの状態

> [!NOTE]
> IP アドレスは、IP アドレスとそのアドレスを持つコンピューターの物理的な配置場所との間に明確な関連性がないような方法で発行されます。 IP アドレスのマッピングは、多くの場合、クライアント デバイスの実際の使用場所から遠く離れたところにある中央プールから、モバイル プロバイダーや VPN が IP アドレスを発行しているという事実によって、複雑になります。 現時点では、トレース、レジストリ データ、逆引きなどの情報に基づいて IP アドレスを物理的な場所に変換するのがいいでしょう。

**[ユーザー]** ページの **[アクティビティ]** セクションの **[サインイン]** をクリックすると、すべてのユーザー サインインの完全な概要が表示されます。

![スクリーンショットには、[サインイン] を選択できる [アクティビティ] セクションが示されています。](./media/concept-sign-ins/08.png "サインイン アクティビティ")

## <a name="authentication-details"></a>認証の詳細

サインイン レポート内にある **[認証の詳細]** タブには、認証を試行するごとに次の情報が表示されます。

- 適用される認証ポリシーの一覧 (条件付きアクセス、ユーザーごとの MFA、セキュリティの既定値など)
- 適用されるセッション有効期間ポリシーの一覧 (サインインの頻度、MFA の保存、構成可能なトークンの有効期間など)
- サインインに使用される認証方法のシーケンス
- 認証試行が成功したかどうか
- 認証試行が成功または失敗した理由の詳細

こうした情報によって、管理者はユーザーのサインインの各ステップのトラブルシューティングを行い、次の情報を追跡できます。

- 多要素認証によって保護されるサインインの回数 
- セッション有効期間ポリシーに基づく認証プロンプトの理由
- 各認証方法の使用状況と成功率 
- パスワードレス認証方法の使用 (パスワードレス電話サインイン、FIDO2、Windows Hello for Business など) 
- トークン要求によって認証要件が満たされる頻度 (ユーザーが対話形式でパスワードの入力や SMS OTP の入力を求められない場合など)

サインイン レポートを表示している状態で、 **[認証の詳細]** タブを選択 します。 

![[認証の詳細] タブのスクリーンショット](media/concept-sign-ins/auth-details-tab.png)

>[!NOTE]
>**OATH 検証コード** は、OATH ハードウェア トークンとソフトウェア トークン (Microsoft Authenticator アプリの認証など) の両方で認証方法として記録されます。

>[!IMPORTANT]
>**[認証の詳細]** タブでは、ログ情報が完全に集計されるまでの最初のうちは、不完全または不正確なデータが表示されることがあります。 たとえば、次のような場合が確認されています。 
>- サインイン イベントが最初にログに記録された場合、 **[トークンの要求によって満たされました]** というメッセージが正しく表示されません。 
>- **[プライマリ認証]** の行が最初はログに記録されません。 


## <a name="usage-of-managed-applications"></a>マネージド アプリケーションの使用状況

アプリケーションを中心にしてサインイン データを表示すると、次のような疑問に答えることができます。

* アプリケーションをだれが使用しているか。
* 組織内の上位 3 つのアプリケーションはどれか。
* 最新のアプリケーションはどのように実行されるか。

このデータへのエントリ ポイントとなるのは、組織内の上位 3 つのアプリケーションです。 データは **[エンタープライズ アプリケーション]** の下の **[概要]** セクションにある、過去 30 日間のレポートに含まれています。

![スクリーンショットには、[概要] を選択できる場所が示されています。](./media/concept-sign-ins/10.png "サインイン アクティビティ")

アプリ使用状況グラフは、特定の期間の上位 3 つのアプリケーションのサインインを週単位で集計します。 期間の既定値は 30 日です。

![スクリーンショットには、1 か月間のアプリの使用状況が示されています。](./media/concept-sign-ins/graph-chart.png "サインイン アクティビティ")

必要に応じて、特定のアプリケーションにフォーカスを設定できます。

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "レポーティング")

アプリ使用状況グラフ内の日付をクリックすると、サインイン アクティビティの詳細な一覧が表示されます。

**[サインイン]** オプションを使用すると、アプリケーションへのすべてのサインイン イベントの完全な概要を表示できます。

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 のアクティビティ ログ

Microsoft 365 のアクティビティ ログは、[Microsoft 365 管理センター](/office365/admin/admin-overview/about-the-admin-center)から確認できます。 Microsoft 365 アクティビティと Azure AD アクティビティのログで多くのディレクトリ リソースが共有される点について考えてみましょう。 Microsoft 365 のアクティビティ ログがすべて表示されるのは、Microsoft 365 管理センターだけです。 

また、[Office 365 Management API](/office/office-365-management-api/office-365-management-apis-overview) を使用すると、Microsoft 365 のアクティビティ ログにプログラムでアクセスすることもできます。

## <a name="next-steps"></a>次のステップ

* [Azure AD のデータ保有ポリシー](reference-reports-data-retention.md)
* [Azure AD のレポート待機時間](reference-reports-latencies.md)
* [サインイン レポート中の Microsoft ファースト パーティー アプリケーション](/troubleshoot/azure/active-directory/verify-first-party-apps-sign-in#application-ids-for-commonly-used-microsoft-applications)
