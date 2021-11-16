---
title: ユーザー アカウントに対する Azure Active Directory のセキュリティ運用
description: ベースラインを確立するためのガイダンスと、ユーザー アカウントの潜在的なセキュリティ問題を監視し、アラートを生成する方法。
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e386add0cab29ed0570f3b0b72d6e34cbb363849
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292563"
---
# <a name="azure-active-directory-security-operations-for-user-accounts"></a>ユーザー アカウントに対する Azure Active Directory のセキュリティ運用

ユーザー ID は、組織とデータを保護する上で最も重要な側面の 1 つです。 この記事では、アカウントの作成、削除と、アカウントの使用状況を監視するためのガイダンスを提供します。 最初に、通常とは異なるアカウントの作成と削除を監視する方法について説明します。 次に、アカウントの異常な使用を監視する方法について説明します。 

[Azure Active Directory (Azure AD) のセキュリティ運用の概要](security-operations-introduction.md)に関するページをまだ読んでいない場合は、読んでから先に進むことをお勧めします。

この記事では、一般的なユーザー アカウントについて説明します。 特権アカウントについては、セキュリティ運用の特権アカウントに関するページを参照してください。

## <a name="define-a-baseline"></a>ベースラインを定義する

異常な動作を発見するには、通常の想定される動作をまず定義する必要があります。 組織にとって想定される動作を定義することで、想定しない動作が発生したときに判断することができます。 また、定義することで、監視とアラートを行う際の擬陽性のノイズ レベルを減らすことができます。 

想定するものを定義したら、ベースライン監視を実行し、想定内容を検証します。 その情報を使用して、定義した許容範囲から外れたものがないか、ログを監視することができます。 

通常のプロセス以外で作成されたアカウントのデータ ソースとして、Azure AD 監査ログ、Azure AD サインイン ログ、ディレクトリ属性を使用します。 組織にとって通常とは何かについて考え、定義する際に役立つ提案を次に示します。

* **ユーザーのアカウント作成** - 以下を評価します。

   * ユーザー アカウントの作成と管理に使用するツールとプロセスの戦略と原則。 たとえば、ユーザー アカウントの属性に適用される標準の属性、形式はあるでしょうか。 

  * アカウントの作成に承認されているソース。 たとえば、Active Directory (AD)、Azure Active Directory、または Workday のような人事システムによるものです。

  * 承認されたソース以外でアカウントが作成された場合のアラート戦略。 ご自分の組織が共同作業している組織の管理された一覧はありますか。

  * ゲスト アカウントのプロビジョニングと、エンタイトルメント管理やその他の通常のプロセス以外で作成されたアカウントに対するアラート パラメーター。

  * 承認されたユーザー管理者ではないアカウントによって作成、変更、または無効化されたアカウントに対する戦略とアラートのパラメーター。

  * 従業員 ID などの標準の属性がない、または組織の名前付け規則に従っていないアカウントの監視とアラート戦略。

  * アカウントの削除と保持のための戦略、原則、プロセス。

* **オンプレミスのユーザー アカウント** - Azure AD Connect と同期されているアカウントについて、以下を評価します。

  * 同期のスコープ内にあるフォレスト、ドメイン、組織単位 (OU)。 これらの設定を変更できる承認された管理者は誰ですか。また、スコープはどのくらいの頻度で確認されますか。

  * 同期されるアカウントの種類。 たとえば、ユーザー アカウントやサービス アカウントなどです。 

  * オンプレミスの特権アカウントを作成するプロセスと、この種類のアカウントの同期を制御する方法。

  * オンプレミスのユーザー アカウントを作成するプロセスと、この種類のアカウントの同期を管理する方法。

オンプレミスのアカウントのセキュリティと監視の詳細については、「[オンプレミスの攻撃から Microsoft 365 を保護する](protect-m365-from-on-premises-attacks.md)」を参照してください。

* **クラウド ユーザー アカウント** - 以下を評価します。

  * Azure AD でクラウド アカウントを直接プロビジョニングし、管理するプロセス。

  * Azure AD のクラウド アカウントとしてプロビジョニングされるユーザーの種類を決定するプロセス。 たとえば、特権アカウントのみを許可するか、ユーザー アカウントも許可するか、などです。

  * クラウド ユーザー アカウントの作成と管理を行うことを想定する、信頼できる個人とプロセスの一覧を作成し、保守するプロセス。

  * 承認されていないクラウドベースのアカウントに対するアラート戦略を作成し、保守するプロセス。 

## <a name="where-to-look"></a>確認先

調査と監視に使用するログ ファイルは次のとおりです。 

* [Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md)

* [サインイン ログ](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 監査ログ](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault ログ](../../key-vault/general/logging.md?tabs=Vault)

* [危険なユーザー ログ](../identity-protection/howto-identity-protection-investigate-risk.md)

* [UserRiskEvents ログ](../identity-protection/howto-identity-protection-investigate-risk.md)

Azure portal から、Azure AD 監査ログを表示したり、コンマ区切り値 (CSV) または JavaScript Object Notation (JSON) ファイルとしてダウンロードしたりできます。 Azure portal には、Azure AD ログを他のツールと統合する方法がいくつか用意されており、監視とアラートの自動化を強化することができます。

* **[Microsoft Sentinel](../../sentinel/overview.md)** – セキュリティ情報イベント管理 (SIEM) 機能により、エンタープライズ レベルでインテリジェントなセキュリティ分析ができるようにします。 

* **[Azure Monitor](../../azure-monitor/overview.md)** - さまざまな条件に基づいて監視とアラートを自動化します。 ブックを作成または使用して、異なるソースのデータを結合できます。

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) と SIEM の統合**- [Azure Event Hubs 統合を介して、Splunk、ArcSight、QRadar、Sumo Logic などの他の SIEM と Azure AD ログを統合できます](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。

* **[Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security)** – アプリの検出と管理、すべてのアプリとリソースの制御、およびクラウド アプリのコンプライアンスの確認を行うことができます。 

監視とアラートの対象の多くは、条件付きアクセス ポリシーの影響です。 [条件付きアクセスに関する分析情報とレポートのブック](../conditional-access/howto-conditional-access-insights-reporting.md)を使用すると、サインイン時の 1 つ以上の条件付きアクセス ポリシーの影響と、デバイスの状態などのポリシーの結果を確認できます。 このブックを使用すると、影響の概要を表示し、特定の期間における影響を特定できます。 ブックを使用して、特定のユーザーのサインインを調査することもできます。 

 この記事の残りの部分で、監視とアラートをお勧めする対象について説明し、脅威の種類別にまとめています。 特定の事前構築済みソリューションがある場合は、表の後にリンクを示すか、サンプルを提供しています。 それ以外の場合は、前述のツールを使用してアラートを作成できます。 

## <a name="account-creation"></a>アカウントの作成

異常なアカウント作成は、セキュリティ上の問題を示している可能性があります。 短命なアカウント、名前付け標準に従っていないアカウント、通常のプロセス以外で作成されたアカウントなどを調査する必要があります。

### <a name="short-lived-accounts"></a>短命なアカウント

通常の ID 管理プロセス以外で行われたアカウントの作成と削除は、Azure AD で監視する必要があります。 短命なアカウントとは、短期間に作成され、削除されたアカウントです。 このようなアカウントの作成と短期の削除は、不正なアクターがアカウントを作成し、使用後にアカウントを削除することで、検出を回避しようとしていることを示す可能性があります。

短命なアカウントのパターンは、承認されていない個人またはプロセスが、確立されたプロセスとポリシーから外れたアカウントの作成や削除の権限を持っていることを示す可能性があります。 このような動作により、目に見えるマーカーがディレクトリから削除されます。 

アカウントの作成と削除に関するデータの軌跡を迅速に発見できなければ、インシデントの調査に必要な情報が存在しなくなる可能性があります。 たとえば、アカウントが削除された後、ごみ箱から消去される場合があります。 監査ログは 30 日間保持されます。 ただし、ログを Azure Monitor またはセキュリティ情報イベント管理 (SIEM) ソリューションにエクスポートすることで、より長期的に保持することができます。 

| [What to monitor] (監視対象)                                                  | リスク レベル | Where               | フィルターまたはサブフィルター                                                                                                                                         | メモ                                                                                                                               |
|------------------------------------------------------------------|------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| 近い期間内のアカウントの作成と削除のイベント。  | 高       | Azure AD 監査ログ | アクティビティ: ユーザーの追加<br>状態 = 成功<br>および<br>アクティビティ: ユーザーの削除<br>状態 = 成功<br>                                                          | ユーザー プリンシパル名 (UPN) イベントを検索します。 24 時間以内に作成され、削除されたアカウントを探します。                          |
| 承認されていないユーザーまたはプロセスによって作成され、削除されたアカウント。 | Medium     | Azure AD 監査ログ | 開始者 (アクター) - ユーザー プリンシパル名<br>および<br>アクティビティ: ユーザーの追加<br>状態 = 成功<br>および - または<br>アクティビティ: ユーザーの削除<br>状態 = 成功      | アクターが承認されていないユーザーの場合、アラートを送信するように構成します。                                                                    |
| 承認されていないソースからのアカウント。                              | Medium     | Azure AD 監査ログ | アクティビティ: ユーザーの追加<br>状態 = 成功<br>1 つ以上のターゲット = ユーザー プリンシパル名                                                                                 | 承認されたドメインからのエントリではない場合、または既知のブロックされたドメインのものである場合、アラートを送信するように構成します。                               |
| 特権ロールに割り当てられたアカウント。                          | 高       | Azure AD 監査ログ | アクティビティ: ユーザーの追加<br>状態 = 成功<br>および<br>アクティビティ: ユーザーの削除<br>状態 = 成功<br>および<br>アクティビティ: ロールへのメンバーの追加<br>状態 = 成功 | アカウントが Azure AD ロール、Azure ロール、または特権グループ メンバーシップに割り当てられている場合、調査についてアラートを生成し、優先します。 |

特権アカウントと非特権アカウントの両方を監視し、アラートを生成する必要があります。 ただし、特権アカウントは管理者アクセス許可を持っているため、監視、アラート、対応のプロセスにおいて優先順位を高くする必要があります。 

### <a name="accounts-not-following-naming-policies"></a>名前付けポリシーに従っていないアカウント

名前付けポリシーに従っていないユーザー アカウントは、組織のポリシーに基づかずに作成された可能性があります。 

ベスト プラクティスは、ユーザー オブジェクトに名前付けポリシーを設定することです。 名前付けポリシーを設定することで、管理が容易になり、一貫性を保つことができます。 このポリシーがあると、承認されたプロセス以外でユーザーが作成された場合に検出するのにも役立ちます。 不正なアクターは、名前付け標準を認識していない可能性があり、組織のプロセス以外でプロビジョニングされたアカウントを検出しやすくする可能性があります。

組織には、ユーザー アカウントや特権アカウントの作成に使用される特定の形式や属性が存在する傾向があります。 次に例を示します。

* 管理者アカウントの UPN = ADM_firstname.lastname@tenant.onmicrosoft.com

* ユーザー アカウントの UPN = Firstname.Lastname@contoso.com

また、ユーザー アカウントには、実際のユーザーを識別するための属性が設定されていることがよくあります。 たとえば、EMPID = XXXNNN などです。 組織にとって通常とは何かについて考え、定義するのに役立つ提案と、アカウントが組織の名前付け規則に従っていないログ エントリのベースラインを定義する場合に考慮すべきことを次に示します。

* 名前付け規則に従っていないアカウント。 たとえば、`nnnnnnn@contoso.com` と `firstname.lastname@contoso.com` などです。 

* 標準の属性が設定されていない、または正しい形式ではないアカウント。 たとえば、有効な従業員 ID がない場合などです。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - | - | - | - |
| 想定される属性が定義されていないユーザー アカウント。| 低| Azure AD 監査ログ| アクティビティ: ユーザーの追加<br>状態 = 成功| 標準の属性が null 値または誤った形式のアカウントを探します。 たとえば、EmployeeID |
| 誤った名前付け形式で作成されたユーザー アカウント。| 低| Azure AD 監査ログ| アクティビティ: ユーザーの追加<br>状態 = 成功| 名前付けポリシーに従わない UPN のアカウントを探します。 |
| 名前付けポリシーに従わない特権アカウント。| 高| Azure サブスクリプション| [Azure portal を使用して Azure でのロールの割り当てを一覧表示する - Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| サブスクリプションのロール割り当てを一覧表示し、サインイン名が組織の形式と一致していない場合にアラートを生成します。 たとえば、プレフィックスとしての ADM_ です。 |
| 名前付けポリシーに従わない特権アカウント。| 高| Azure AD ディレクトリ| [Azure AD ロールの割り当てを一覧表示する](../roles/view-assignments.md)| UPN が組織の形式と一致しない Azure AD ロール アラートのロール割り当てを一覧表示します。 たとえば、プレフィックスとしての ADM_ です。 |



解析の詳細については、以下を参照してください。

* Azure AD 監査ログ - [Azure Monitor ログのテキスト データの解析](../../azure-monitor/logs/parse-text.md)

* Azure サブスクリプション - [Azure PowerShell を使用して Azure でのロールの割り当てを一覧表示する](../../role-based-access-control/role-assignments-list-powershell.md)

* Azure Active Directory - [Azure AD ロールの割り当てを一覧表示する](../roles/view-assignments.md) 

### <a name="accounts-created-outside-normal-processes"></a>通常のプロセス以外で作成されたアカウント

ユーザーと特権アカウントを作成する標準のプロセスを用意することは、ID のライフサイクルを安全に制御するために重要です。 確立されたプロセス以外でユーザーのプロビジョニングとプロビジョニング解除が行われると、それがセキュリティ リスクになる可能性があります。 また、確立されたプロセス以外での運用によって、ID 管理上の問題が発生する可能性があります。 次のようなリスクが考えられます。

* ユーザー アカウントと特権アカウントが、組織のポリシーに従って管理されない可能性があります。 その結果、正しく管理されていないアカウントに対する攻撃対象領域が広がる可能性があります。

* 不正なアクターが悪意のある目的でアカウントを作成しても、検出することが難しくなります。 確立された手順以外で有効なアカウントが作成されることで、悪意のある目的でアカウントが作成されたり、アクセス許可が変更されたりしても、検出することが難しくなります。 

ユーザー アカウントと特権アカウントは、必ず組織のポリシーに従って作成することをお勧めします。 たとえば、正しい名前付け標準、組織の情報、適切な ID ガバナンスのスコープ内でアカウントを作成する必要があります。 ID を作成、管理、および削除する権限を誰が持っているかについて、組織は厳格に管理する必要があります。 このようなアカウントを作成するロールは厳重に管理し、これらのアクセス許可を承認および取得する確立されたワークフローに従った後にのみ、その権限を使用できるようにする必要があります。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - | - | - | - |
| 承認されていないユーザーまたはプロセスによって作成または削除されたユーザー アカウント。| Medium| Azure AD 監査ログ| アクティビティ: ユーザーの追加<br>状態 = 成功<br>および - または<br>アクティビティ: ユーザーの削除<br>状態 = 成功<br>および<br>開始者 (アクター) - ユーザー プリンシパル名| 承認されていないユーザーまたはプロセスによって作成されたアカウントについてアラートを生成します。 高い特権を使用して作成されたアカウントを優先します。 |
| 承認されていないソースから作成または削除されたユーザー アカウント。| Medium| Azure AD 監査ログ| アクティビティ: ユーザーの追加<br>状態 = 成功<br>または<br>アクティビティ: ユーザーの削除<br>状態 = 成功<br>および<br>1 つ以上のターゲット = ユーザー プリンシパル名| 承認されていないドメインまたは既知のブロックされたドメインである場合にアラートを生成します。 |


## <a name="unusual-sign-ins"></a>通常とは異なるサインイン

ユーザー認証に失敗するのは普通のことです。 ただし、失敗のパターンやブロックが見られる場合、ユーザーの ID に何かが起こっている兆候である可能性があります。 たとえば、パスワード スプレーやブルート フォース攻撃の場合、またはユーザー アカウントが侵害された場合などです。 パターンが出現したときに監視し、アラートを生成することが重要です。 これにより、ユーザーと組織のデータを確実に保護することができます。 

成功すると、すべてがうまくいっているように見えます。 ただし、それは不正なアクターがサービスへのアクセスに成功したことを意味する可能性があります。 ログインの成功を監視することで、アクセス権を取得しているユーザー アカウントであっても、アクセス権を持つべきユーザー アカウントではない場合を検出することができます。 ユーザー認証の成功は、Azure AD サインイン ログの通常のエントリです。 パターンが出現したときに検出するために、監視とアラートを行うことをお勧めします。 これにより、ユーザー アカウントと組織のデータを確実に保護することができます。 


ログの監視とアラートの戦略を設計および運用する際には、Azure portal で使用できるツールを検討してください。 Identity Protection を使用すると、ID ベースのリスク検出、保護、修復を自動化することができます。 Identity Protection には、リスクの検出とユーザーとサインインのリスク スコアの割り当てにインテリジェンス主導型機械学習とヒューリスティック システムが使用されています。お客様は、リスク レベルに基づいて、アクセスを許可または拒否するタイミングや、ユーザーがリスクから安全に自己修復できるようにするためのポリシーを構成することができます。 次に示す Identity Protection のリスク検出を使用すると、今日のリスク レベルがわかります。

| [What to monitor] (監視対象) | リスク レベル | Where | フィルターまたはサブフィルター | メモ |
| - | - | - | - | - |
| 漏えいした資格情報のユーザー リスク検出| 高| Azure AD リスク検出ログ| UX: 漏えいした資格情報 <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| Azure AD 脅威インテリジェンスのユーザー リスク検出| 高| Azure AD リスク検出ログ| UX: Azure AD 脅威インテリジェンス <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 匿名 IP アドレスのサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 匿名 IP アドレス <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 通常とは異なる移動のサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 通常とは異なる移動 <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 異常なトークン| 場合により異なる| Azure AD リスク検出ログ| UX: 異常なトークン <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| マルウェアにリンクした IP アドレスのサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: マルウェアにリンクした IP アドレス <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 疑わしいブラウザーのサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 疑わしいブラウザー <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 見慣れないサインイン プロパティのサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 見慣れないサインイン プロパティ <br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 悪意のある IP アドレスのサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 悪意のある IP アドレス<br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 受信トレイに対する疑わしい操作ルールのサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 受信トレイに対する疑わしい操作ルール<br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| パスワード スプレーのサインイン リスク検出| 高| Azure AD リスク検出ログ| UX: パスワード スプレー<br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| あり得ない移動のサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: あり得ない移動<br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 初めての国のサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 初めての国<br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 匿名 IP アドレスからのアクティビティのサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 匿名 IP アドレスからのアクティビティ<br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| 受信トレイからの疑わしい転送のサインイン リスク検出| 場合により異なる| Azure AD リスク検出ログ| UX: 受信トレイからの疑わしい転送<br><br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |
| Azure AD 脅威インテリジェンスのサインイン リスク検出| 高| Azure AD リスク検出ログ| UX: Azure AD 脅威インテリジェンス<br>API: 「[riskDetection リソースの種類 - Microsoft Graph ベータ](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)」を参照してください| 「[リスクとは - Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md)」を参照してください |

詳細については、「[Identity Protection とは](../identity-protection/overview-identity-protection.md)」を参照してください。 


### <a name="what-to-look-for"></a>注意点

Azure AD サインイン ログ内のデータに対して監視を構成し、アラートが生成され、組織のセキュリティ ポリシーに準拠していることを確認します。 この例を次にいくつか示します。

* **認証の失敗**: 人は誰でも、パスワードをときどき間違えるものです。 ただし、認証に何度も失敗するということは、不正なアクターがアクセスを取得しようとしていることを示す可能性があります。 攻撃の強さはさまざまですが、1 時間に数回の試行から、はるかに高い頻度のものまであります。 たとえば、パスワード スプレーの場合、通常は多数のアカウントに対して簡単なパスワードが試行されるのに対し、ブルート フォースの場合はターゲットのアカウントに対して多数のパスワードが試行されます。 

* **認証の割り込み**: Azure AD の中断は、認証を満たす追加のプロセスが挿入されたことを表します (CA ポリシーのコントロールを適用する場合など)。 これは通常のイベントであり、アプリケーションが正しく構成されていない場合に発生する可能性があります。 ただし、あるユーザー アカウントに対して多数の割り込みが発生した場合、そのアカウントに何かが発生していることを示す可能性があります。 

  * たとえば、サインイン ログでユーザーをフィルター処理し、サインインの状態 = 割り込みと条件付きアクセス = 失敗が大量に確認されたとします。 さらに掘り下げると、認証の詳細に、パスワードは正しいが、強力な認証が必要であると示されることがあります。 これは、ユーザーが多要素認証 (MFA) を完了していないことを意味しており、ユーザーのパスワードが漏えいしていて、不正なアクターが MFA を完了できないことを示している可能性があります。

* **スマート ロックアウト**: Azure AD には、認証プロセスになじみのある場所となじみのない場所の概念を導入するスマート ロックアウト サービスが用意されています。 なじみのある場所にアクセスしたユーザー アカウントは認証に成功しても、同じ場所になじみのない不正なアクターは数回の試行後にブロックされます。 ロックアウトされたアカウントを探し、さらに調査します。 

* **IP の変更**: 異なる IP アドレスから送信されるユーザーが確認されるのは通常のことです。 ただし、ゼロ トラスト状態では、決して信用せず、常に検証します。 大量の IP アドレスとサインインの失敗が確認される場合、侵入の兆候である可能性があります。 複数の IP アドレスから発生する多数の認証失敗のパターンを探します。 仮想プライベート ネットワーク (VPN) 接続は擬陽性を引き起こす可能性があることに注意してください。 課題に関係なく、IP アドレスの変更を監視し、可能であれば Azure AD Identity Protection を使用して、これらのリスクを自動的に検出して軽減することをお勧めします。

* **場所**: 一般的に、ユーザー アカウントは地理的に同じ場所にあることを想定します。 また、従業員やビジネス関係者がいる場所からのサインインも想定します。 ユーザー アカウントが海外の異なる場所から、そこまで移動するのにかかる時間よりも短い時間で送信された場合、そのユーザー アカウントは悪用されていることを示す可能性があります。 VPN は擬陽性を引き起こす可能性があるため、地理的に離れた場所からサインインするユーザー アカウントを監視し、可能であれば Azure AD Identity Protection を使用して、これらのリスクを自動的に検出して軽減することをお勧めします。

このリスク領域については、標準のユーザー アカウントと特権アカウントの両方を監視し、特権アカウントの調査を優先することをお勧めします。 特権アカウントは、どの Azure AD テナントでも最も重要なアカウントです。 特権アカウントに固有のガイダンスについては、セキュリティ運用の特権アカウントに関するページを参照してください。 

### <a name="how-to-detect"></a>検出する方法

Azure Identity Protection と Azure AD のサインイン ログを使用して、通常とは異なるサインインの特徴によって示される脅威を検出できます。 Identity Protection については、「[Identity Protection とは](../identity-protection/overview-identity-protection.md)」を参照してください。 監視とアラートの目的で、Azure Monitor または SIEM にデータをレプリケートすることもできます。 環境の通常を定義し、ベースラインを設定するには、次のことを判断します。

* ユーザー ベースで通常と見なされるパラメーター。

* ユーザーがサービス デスクに電話するか、セルフサービス パスワード リセットを実行するまでの、ある期間におけるパスワードの平均試行回数。

* アラートを生成するまでに許容する失敗試行の回数と、それがユーザー アカウントと特権アカウントで異なるかどうか。

* アラートを生成するまでに許容する MFA 試行の回数と、それがユーザー アカウントと特権アカウントで異なるかどうか。

* レガシ認証が有効であり、使用を中止するためのロードマップがあるかどうか。 

* 既知のエグレス IP アドレスは組織のものである。

* ユーザーが操作している国。

* ネットワーク上の場所または国内に固定されているユーザーのグループがあるかどうか。

* 組織に固有の、異常なサインインに関するその他のメトリックを特定します。 たとえば、組織が操業していない曜日、時間帯、年などです。

環境内のアカウントの種類にとって通常とは何かの範囲を絞り込んだら、次の点を考慮し、どのシナリオを監視してアラートを生成するかを決定し、アラートを微調整します。

* Identity Protection が構成されている場合に監視とアラートを行う必要がありますか。

* 特権アカウントに適用されるより厳しい条件があり、それを使用して監視とアラートを行うことができますか。 たとえば、信頼できる IP アドレスからのみ特権アカウントを使用することを必須にするなどです。

* 設定したベースラインは積極的すぎませんか。 アラート数が多すぎると、アラートが無視されたり、見逃されたりする可能性があります。

Identity Protection を構成すると、セキュリティ ベースライン ポリシーをサポートする保護が確実に実行されるようになります。 たとえば、リスク = 高の場合にユーザーをブロックするなどです。 このリスク レベルは、ユーザー アカウントが侵害されていることを高い確度で示しています。 サインイン リスク ポリシーとユーザー リスク ポリシーの設定については、「[Identity Protection ポリシー](../identity-protection/concept-identity-protection-policies.md)」を参照してください。 条件付きアクセスの設定の詳細については、「[条件付きアクセス: サインイン リスクベースの条件付きアクセス](../conditional-access/howto-conditional-access-policy-risk.md)」を参照してください。

エントリの影響と重大度に基づいて重要度の高い順に一覧表示したものを次に示します。

### <a name="monitoring-for-failed-unusual-sign-ins"></a>通常とは異なる失敗したサインインの監視

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 失敗したサインインの試行。| 中 - 孤立したインシデントの場合<br>高 - 多数のアカウントで同じパターンまたは VIP が発生している場合。| Azure AD サインイン ログ| 状態 = 失敗<br>および<br>サインイン エラー コード 50126 - <br>無効なユーザー名またはパスワードにより、資格情報の検証でエラーが発生しました。| ベースラインしきい値を定義してから、組織の行動に合わせて監視および調整し、誤ったアラートが生成されないようにします。 |
| スマート ロックアウト イベント。| 中 - 孤立したインシデントの場合<br>高 - 多数のアカウントで同じパターンまたは VIP が発生している場合。| Azure AD サインイン ログ| 状態 = 失敗<br>および<br>サインイン エラー コード = 50053 - IdsLocked| ベースラインしきい値を定義してから、組織の行動に合わせて監視および調整し、誤ったアラートが生成されないようにします。 |
| 割り込み| 中 - 孤立したインシデントの場合<br>高 - 多数のアカウントで同じパターンまたは VIP が発生している場合。| Azure AD サインイン ログ| 500121、強力な認証の要求時に、認証に失敗しました。 <br>または<br>50097、デバイス認証が必要です、または 50074、強力な認証が必要です。 <br>または<br>50155、DeviceAuthenticationFailed<br>または<br>50158、ExternalSecurityChallenge - 外部セキュリティ チャレンジが満たされませんでした<br>または<br>53003 と失敗の理由 = CA によるブロック| 割り込みについて監視し、アラートを生成します。<br>ベースラインしきい値を定義してから、組織の行動に合わせて監視および調整し、誤ったアラートが生成されないようにします。 |


エントリの影響と重大度に基づいて重要度の高い順に一覧表示したものを次に示します。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 多要素認証 (MFA) の不正アラート。| 高| Azure AD サインイン ログ| 状態 = 失敗<br>および<br>詳細 = MFA 拒否<br>| エントリについて監視し、アラートを生成します。 |
| 操業していない国からの認証の失敗。| Medium| Azure AD サインイン ログ| 場所 = \<unapproved location\>| すべてのエントリについて監視し、アラートを生成します。 |
| レガシ プロトコルまたは使用されていないプロトコルの失敗した認証。| Medium| Azure AD サインイン ログ| 状態 = 失敗<br>および<br>クライアント アプリ = その他のクライアント、POP、IMAP、MAPI、SMTP、ActiveSync| すべてのエントリについて監視し、アラートを生成します。 |
| CA によってブロックされた失敗。| Medium| Azure AD サインイン ログ| エラー コード = 53003 <br>および<br>失敗の理由 = CA によるブロック| すべてのエントリについて監視し、アラートを生成します。 |
| 任意の種類の認証失敗の増加。| Medium| Azure AD サインイン ログ| 全体的な失敗の増加をキャプチャします。 つまり、今日の失敗の合計が、前週の同じ曜日に比べて 10% を超えて増えている場合などです。| しきい値を設定していない場合は、失敗が 10% 以上増えた場合に監視してアラートを生成します。 |
| 国の通常の業務を行っていない時間帯と曜日に発生した認証。| 低| Azure AD サインイン ログ| 通常の業務が行われていない曜日または時間帯に発生した対話型の認証をキャプチャします。 <br>状態 = 成功<br>および<br>場所 = \<location\><br>および<br>日時 = \<not normal working hours\>| すべてのエントリについて監視し、アラートを生成します。 |
| アカウントがサインインで無効またはブロックされる| 低| Azure AD サインイン ログ| 状態 = 失敗<br>および<br>エラー コード = 50057、ユーザー アカウントは無効になっています。| これは、誰かが組織を退職した後にアカウントにアクセスしようとしていることを示している可能性があります。 このアカウントはブロックされていますが、このアクティビティについて記録し、アラートを生成することが重要です。 |


### <a name="monitoring-for-successful-unusual-sign-ins"></a>通常とは異なるサインインの監視

 | [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - |- |- |- |- |
| 想定されるコントロールの範囲外の特権アカウントの認証。| 高| Azure AD サインイン ログ| 状態 = 成功<br>および<br>UserPricipalName = \<Admin account\><br>および<br>場所 = \<unapproved location\><br>および<br>IP アドレス = \<unapproved IP\><br>デバイス情報 = \<unapproved Browser, Operating System\><br>| 想定されるコントロールの範囲外で特権アカウントの認証が成功した場合に監視し、アラートを生成します。 3 つの一般的なコントロールがあります。 |
| 単一要素認証のみが必要な場合。| 低| Azure AD サインイン ログ| 状態 = 成功<br>認証要件 = 単一要素認証| これを定期的に監視し、これが想定される動作であることを確認します。 |
| MFA に登録されていない特権アカウントを検出します。| 高| Azure Graph API| 管理者アカウントの IsMFARegistered eq false のクエリ。 <br>[credentialUserRegistrationDetails の一覧表示 - Microsoft Graph ベータ版 | Microsoft Docs](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http)| 監査と調査を行って、意図的なのか、見落としなのかを判断します。 |
| 組織が操業していない国からの認証の成功。| Medium| Azure AD サインイン ログ| 状態 = 成功<br>場所 = \<unapproved country\>| 指定された市区町村名と等しくないエントリを監視し、アラートを生成します。 |
| 認証の成功、CA によってブロックされたセッション。| Medium| Azure AD サインイン ログ| 状態 = 成功<br>および<br>エラー コード = 53003 - 失敗の理由、CA によるブロック| 認証に成功しても、CA によってセッションがブロックされた場合を監視し、調査します。 |
| レガシ認証を無効にした後の認証の成功。| Medium| Azure AD サインイン ログ| 状態 = 成功 <br>および<br>クライアント アプリ = その他のクライアント、POP、IMAP、MAPI、SMTP、ActiveSync| 組織でレガシ認証を無効にした場合は、レガシ認証が成功したときを監視し、アラートを生成します。 |


定期的に、単一要素認証のみが必要な中事業影響度 (MBI) と高事業影響度 (HBI) のアプリケーションに対する認証を確認することをお勧めします。 それぞれについて、単一要素認証が想定されていたかどうかを判断する必要があります。 さらに、認証の成功の増加、または場所に基づいて予期しない時刻のときに確認します。 

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - |- |- |- |
| 単一要素認証を使用した MBI および HBI アプリケーションに対する認証。| 低| Azure AD サインイン ログ| 状態 = 成功<br>および<br>アプリケーション ID = \<HBI app\> <br>および<br>認証要件 = 単一要素認証| この構成が意図的なものであることを確認し、検証します。 |
| 国が通常の業務を行っていない曜日、時間帯、年の認証。| 低| Azure AD サインイン ログ| 通常の業務が行われていない曜日または時間帯に発生した対話型の認証をキャプチャします。 <br>状態 = 成功<br>場所 = \<location\><br>日付と時刻 = \<not normal working hours\>| 国が通常の業務を行っていない曜日、時間帯、年の認証について監視し、アラートを生成します。 |
| 成功したサインインの測定可能な増加| 低| Azure AD サインイン ログ| 全体的な認証の成功数の増加をキャプチャします。 つまり、今日の成功の合計が、前週の同じ曜日に比べて 10% を超えて増えている場合などです。| しきい値を設定していない場合は、認証の成功が 10% 以上増えた場合に監視し、アラートを生成します。 |

## <a name="next-steps"></a>次のステップ
これらのセキュリティ運用ガイドの記事を参照してください。

[Azure AD セキュリティ運用の概要](security-operations-introduction.md)

[ユーザー アカウントのためのセキュリティ運用](security-operations-user-accounts.md)

[特権アカウントのためのセキュリティ運用](security-operations-privileged-accounts.md)

[Privileged Identity Management のためのセキュリティ運用](security-operations-privileged-identity-management.md)

[アプリケーションのためのセキュリティ運用](security-operations-applications.md)

[デバイスのためのセキュリティ運用](security-operations-devices.md)
 
[インフラストラクチャのためのセキュリティ運用](security-operations-infrastructure.md)
