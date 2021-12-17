---
title: アプリケーション向けの Azure Active Directory セキュリティ運用
description: セキュリティ上の脅威を特定するために、アプリケーションを監視してアラートを生成する方法について説明します。
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
ms.openlocfilehash: a70d01ddd2387e30ef854acff1412fac2bd0bf65
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346479"
---
# <a name="azure-active-directory-security-operations-guide-for-applications"></a>アプリケーション向けの Azure Active Directory セキュリティ運用ガイド

アプリケーションは、セキュリティ侵害の攻撃対象となるため、監視する必要があります。 ユーザー アカウントほど頻繁に狙われるわけではありませんが、侵害される可能性があります。 アプリケーションは人間の介入なしで実行されることが多いため、攻撃を検出することがより難しい場合があります。

この記事では、アプリケーション イベントの監視とアラートに関するガイダンスを提供します。 次のことを確実に行えるように定期的に更新されます。

* 悪意のあるアプリケーションがデータに不正にアクセスできないようにする。

* 既存のアプリケーションが不正なアクターによって侵害されないようにする。

* 新しいアプリケーションのビルドと構成をより安全に行うことができるように分析情報を収集する。

Azure Active Directory (Azure AD) でのアプリケーションの動作に慣れていない場合は、[Azure AD のアプリとサービス プリンシパル](../develop/app-objects-and-service-principals.md)に関する記事をご覧ください。

> [!NOTE]
> [Azure Active Directory セキュリティ運用の概要](security-operations-introduction.md)をまだ確認されていない場合は、今すぐご確認ください。

## <a name="what-to-look-for"></a>注意点

アプリケーション ログでセキュリティ インシデントを監視する場合、以下を確認すると、通常のアクティビティと悪意のあるアクティビティを区別できます。 次のイベントはセキュリティ上の問題を示す可能性があるため、それぞれについて、この記事の残りの部分で説明します。

* 通常ビジネスのプロセスとスケジュールの外で発生した変更すべて。

* アプリケーション資格情報の変更

* アプリケーションのアクセス許可

   * Azure AD または Azure RBAC ロールに割り当てられたサービス プリンシパル。

   * 高い特権を持つアクセス許可が付与されているアプリケーション。

   * Azure Key Vault の変更。

   * アプリケーションに同意を付与するエンド ユーザー。

   * リスクのレベルに基づいてエンド ユーザーの同意を停止した。

* アプリケーション構成の変更

   * ユニバーサル リソース識別子 (URI) の変更または非標準。

   * アプリケーション所有者に対する変更。

   * ログアウト URL が変更された。

## <a name="where-to-look"></a>確認先

調査と監視に使用するログ ファイルは次のとおりです。

* [Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md)

* [サインイン ログ](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 監査ログ](/microsoft-365/compliance/auditing-solutions-overview)

* [Azure Key Vault のログ](../../key-vault/general/logging.md)

Azure AD 監査ログは、Azure portal で確認し、コンマ区切り値 (CSV) ファイルまたは JavaScript Object Notation (JSON) ファイルとしてダウンロードできます。 Azure portal には、Azure AD ログを他のツールと統合する方法がいくつか用意されており、監視とアラートの自動化を強化することができます。

* **[Microsoft Sentinel](../../sentinel/overview.md)** – セキュリティ情報イベント管理 (SIEM) 機能を備え、エンタープライズ レベルでインテリジェントにセキュリティを分析します。 

* **[Azure Monitor](../../azure-monitor/overview.md)** - さまざまな条件に基づいて監視とアラートを自動化します。 ブックを作成または使用して、異なるソースのデータを結合できます。

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) を SIEM と統合** - Event Hub 統合を介して、Splunk、ArcSight、QRadar、Sumo Logic など、[他の SIEM に Azure AD ログを統合できます](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。

* **[Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security)** – アプリを検出し、アプリを管理し、すべてのアプリとリソースを制御し、クラウド アプリのコンプライアンス状況を確認できます。

監視とアラートの対象の多くは、条件付きアクセス ポリシーの影響です。 [条件付きアクセスに関する分析情報とレポートのブック](../conditional-access/howto-conditional-access-insights-reporting.md)を使用すると、サインイン時の 1 つ以上の条件付きアクセス ポリシーの影響と、デバイスの状態などのポリシーの結果を確認できます。 このブックを使用すると、影響の概要を表示し、特定の期間における影響を特定できます。 ブックを使用して、特定のユーザーのサインインを調査することもできます。 

 この記事の残りの部分で、監視とアラートをお勧めする対象について説明し、脅威の種類別にまとめています。 特定の事前構築済みソリューションがある場合は、表の後にリンクを示すか、サンプルを提供しています。 それ以外の場合は、前述のツールを使用してアラートを作成できます。 

## <a name="application-credentials"></a>アプリケーション資格情報

多くのアプリケーションでは、Azure AD での認証に資格情報が使用されます。 予想されるプロセスの外部で追加された資格情報は、それらの資格情報を使用する悪意のあるアクターである可能性があります。 クライアント シークレットを使用するのではなく、信頼できる機関によって発行された X509 証明書またはマネージド ID を使用することを強くお勧めします。 しかしながら、クライアント シークレットを使用する必要がある場合は、適切な検疫プラクティスに従ってアプリケーションを安全に保ってください。 アプリケーションとサービス プリンシパルの更新は、監査ログに 2 つのエントリとして記録されます。 

* アプリケーションを監視して、資格情報の有効期限が長いアプリケーションを特定します。

* 長い期間の資格情報は、短い期間の資格情報に置き換えます。 資格情報がコード リポジトリにコミットされず、安全に格納されることを保証するための手順を行います。


| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| -|-|-|-|-|
| 既存のアプリケーションに資格情報を追加した| 高| Azure AD 監査ログ| Service-Core ディレクトリ、Category-ApplicationManagement <br>アクティビティ: アプリケーションの更新 - 証明書およびシークレット管理<br>および<br>アクティビティ: サービス プリンシパルの更新/アプリケーションの更新| 資格情報が次の場合にアラートを生成します。<li> 通常営業時間またはワークフローの外で追加された。<li> ご利用の環境では使われていない種類である。<li> サービス プリンシパルをサポートする SAML 以外のフローに追加された。 |
| 資格情報の有効期間がポリシーで許可されているよりも長い。| Medium| Microsoft Graph| アプリケーション キー資格情報の状態と終了日<br>および<br>アプリケーション パスワードの資格情報| MS Graph API を使用して、資格情報の開始および終了日を検索し、許可された有効期間よりも長い資格情報を評価できます。 この表の後の PowerShell スクリプトを参照してください。 |

 次の事前構築済みの監視とアラートを利用できます。

* Microsoft Sentinel – [新しいアプリまたはサービス プリンシパルの資格情報が追加されたときにアラートを生成する](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/NewAppOrServicePrincipalCredential.yaml) 

* Azure Monitor – [Solorigate リスクの評価に役立つ Azure AD ブック - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* Defender for Cloud Apps – [Defender for Cloud Apps 異常検出アラートの調査ガイド](/cloud-app-security/investigate-anomaly-alerts)

* PowerShell - [資格情報の有効期間を検索するサンプルの PowerShell スクリプト](https://github.com/madansr7/appCredAge)。

## <a name="application-permissions"></a>アプリケーションのアクセス許可

管理者アカウントと同様に、アプリケーションには特権ロールを割り当てることができます。 アプリには、グローバル管理者などの Azure AD ロール、またはサブスクリプション所有者などの Azure RBAC ロールを割り当てることができます。 ユーザーが存在しなくても、バックグラウンド サービスとして実行できるため、アプリケーションに高い特権を持つロールまたはアクセス許可が付与されたときは常に注意深く監視します。 

### <a name="service-principal-assigned-to-a-role"></a>ロールに割り当てられたサービス プリンシパル


| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| Azure RBAC ロールまたは Azure AD ロールに割り当てられたアプリ| 高から中| Azure AD 監査ログ| 種類: サービス プリンシパル<br>アクティビティ: "メンバーをロールに追加する" または "適格なメンバーをロールに追加する"<br>または<br>"スコープを持つメンバーをロールに追加する"。| グローバル管理者などの高い特権を持つロールの場合、リスクは高です。 低い特権ロールの場合、リスクは中です。 通常の変更管理または構成手順の外部で、アプリケーションが Azure ロールまたは Azure AD ロールに割り当てられたときは常にアラートを生成します。 |

### <a name="application-granted-highly-privileged-permissions"></a>高い特権を持つアクセス許可が付与されたアプリケーション

アプリケーションは、最小特権のプリンシパルにも従う必要があります。 アプリケーションのアクセス許可を調査して、本当に必要なアクセス許可を確保します。 既存のアプリケーションを識別し、特権アクセス許可を強調表示するのに役立つ[アプリ同意付与レポート](https://aka.ms/getazureadpermissions)を作成できます。

| [What to monitor] (監視対象)|リスク レベル|Where| フィルターまたはサブフィルター| メモ|
|-|-|-|-|-|
| アプリに " *.All" のアクセス許可 (Directory.ReadWrite.All) や広範囲のアクセス許可 (Mail.* ) など、高い特権を持つアクセス許可が付与される| 高 |Azure AD 監査ログ| "サービス プリンシパルへのアプリ ロールの割り当てを追加する"、 <br>このとき<br> ターゲットで、機密データを含む API を識別する (Microsoft Graph など) <br>および<br>AppRole.Value で、高い特権を持つアプリケーションのアクセス許可 (アプリ ロール) を識別する。| " *.All" (Directory.ReadWrite.All) や広範囲のアクセス許可 (Mail.* ) など、幅広いアクセス許可が付与されたアプリ |
| 管理者が、アプリケーションのアクセス許可 (アプリ ロール) または高い特権を持つ委任されたアクセス許可を付与する |高| Microsoft 365 ポータル| "サービス プリンシパルへのアプリ ロールの割り当てを追加する"、 <br>このとき<br>ターゲットで、機密データを含む API を識別する (Microsoft Graph など)<br>"委任されたアクセス許可の付与を追加する"、 <br>このとき<br>ターゲットで、機密データを含む API を識別する (Microsoft Graph など) <br>および<br>DelegatedPermissionGrant.Scope に、高い特権のアクセス許可が含まれる。| グローバル管理者、アプリケーション管理者、またはクラウド アプリケーション管理者がアプリケーションに同意したとき、アラートを生成します。 特に、通常のアクティビティや変更手順以外の同意を探します。 |
| アプリケーションに、Microsoft Graph、Exchange、SharePoint、または Azure AD に対するアクセス許可が付与される。 |高| Azure AD 監査ログ| "委任されたアクセス許可の付与を追加する" <br>または<br>"サービス プリンシパルへのアプリ ロールの割り当てを追加する"、 <br>このとき<br>ターゲットで、機密データを含む API を識別する (Microsoft Graph、Exchange Online など)| 前の行と同様にアラートを生成します。 |
| 他の API に対するアプリケーションのアクセス許可 (アプリ ロール) が付与される |Medium| Azure AD 監査ログ| "サービス プリンシパルへのアプリ ロールの割り当てを追加する"、 <br>このとき<br>ターゲットで、他の API を識別する。| 前の行と同様にアラートを生成します。 |
| すべてのユーザーの代理として、高い特権を持つ委任されたアクセス許可が付与される |高| Azure AD 監査ログ| "委任されたアクセス許可付与を追加する"。このとき、ターゲットで、機密データを含む API を識別する (Microsoft Graph など)、 <br> DelegatedPermissionGrant.Scope に、高い特権のアクセス許可が含まれる、 <br>および<br>DelegatedPermissionGrant.ConsentType が "AllPrincipals" である。| 前の行と同様にアラートを生成します。 |

アプリのアクセス許可の監視の詳細については、「[チュートリアル: 危険な OAuth アプリを調査して修復する](/cloud-app-security/investigate-risky-oauth)」を参照してください。

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault を使用して、テナントのシークレットを格納できます。 Key Vault の構成とアクティビティの変更には特に注意することをお勧めします。 

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| Key Vault にいつ、だれが、どのようにアクセスするか| Medium| [Azure Key Vault のログ](../../key-vault/general/logging.md?tabs=Vault)| リソースの種類: Key Vault| 調査項目 <li> 通常のプロセスおよび時間外の Key Vault へのアクセス。 <li> Key Vault ACL への変更すべて。 |

Azure Key Vault を設定した後、必ず[ログ記録を有効](../../key-vault/general/howto-logging.md?tabs=azure-cli)にして、[Key Vaults にいつ、どのようにアクセスされたか](../../key-vault/general/logging.md?tabs=Vault)を表示し、Key Vault で[アラートを構成](../../key-vault/general/alert.md)して、正常性に影響があった場合に、割り当てられたユーザーまたは配布リストに電子メール、通話、テキスト メッセージ、または[イベント グリッド](../../key-vault/general/event-grid-overview.md)通知を介して通知します。 さらに、Key Vault 分析情報を使用して[監視](../../key-vault/general/alert.md)を設定すると、Key Vault 要求、パフォーマンス、エラー、待機時間のスナップショットが提供されます。 [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) には、Azure Key Vault 用の[サンプル クエリ](../../azure-monitor/logs/queries.md)もいくつか用意されており、Key Vault を選択した後に [監視] で [ログ] を選択してアクセスできます。

### <a name="end-user-consent"></a>エンドユーザーの同意

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| アプリケーションに対するエンドユーザーの同意| 低| Azure AD 監査ログ| アクティビティ: アプリケーションへの同意/ConsentContext.IsAdminConsent = false| 以下のものを探します。 <li>高プロファイルまたは高い特権を持つアカウント。<li> アプリで、危険度の高いアクセス許可が要求されている<li>疑わしい名前を持つアプリ (ありふれている、スペルミスなど) |


アプリケーションに同意する行為自体は、悪意のあるものではありません。 ではありますが、疑わしいアプリケーションを探して、新しいエンドユーザーの同意付与を調査してください。 [ユーザーの同意操作を制限する](../../security/fundamentals/steps-secure-identity.md)ことができます。

同意操作の詳細については、以下のリソースを参照してください。

* [アプリケーションの同意の管理と Azure Active Directory の同意要求の評価](../manage-apps/manage-consent-requests.md)

* [不正同意付与を検出して修復する - Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)

* [インシデント対応プレイブック - アプリ同意付与の調査](/security/compass/incident-response-playbook-app-consent)

### <a name="end-user-stopped-due-to-risk-based-consent"></a>リスクベースの同意のためにエンド ユーザーが停止した 

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| リスクベースの同意のためにエンドユーザーの同意が停止した| Medium| Azure AD 監査ログ| Core ディレクトリ/ApplicationManagement/アプリケーションへの同意<br> エラー状態の理由 = Microsoft.online.Security.userConsent<br>BlockedForRiskyAppsExceptions| リスクが原因で同意が停止されたときは常に監視および分析します。 以下のものを探します。<li>高プロファイルまたは高い特権を持つアカウント。<li> アプリで、危険度の高いアクセス許可が要求されている<li>疑わしい名前を持つアプリ (ありふれている、スペルミスなど) |

## <a name="application-configuration-changes"></a>アプリケーション構成の変更

アプリケーションの構成に対する変更を監視します。 具体的には、構成の Uniform Resource Identifier (URI)、所有権、ログアウト URL の変更です。

### <a name="dangling-uri-and-redirect-uri-changes"></a>宙ぶらりんの URI とリダイレクト URI の変更 

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| ダングリング URI| 高| Azure AD ログとアプリケーションの登録| Service-Core ディレクトリ、Category-ApplicationManagement<br>アクティビティ: アプリケーションの更新<br>成功 – プロパティ名 AppAddress| 存在しなくなったドメイン名や明示的に所有していないドメイン名を指すなど、宙ぶらりんの URI を探します。 |
| リダイレクト URI 構成の変更| 高| Azure AD ログ| Service-Core ディレクトリ、Category-ApplicationManagement<br>アクティビティ: アプリケーションの更新<br>成功 – プロパティ名 AppAddress| HTTPS * を使用しない Uri、末尾または URL のドメインにワイルドカードを含む URIs、アプリケーションに固有ではない URIs、制御しないドメインを指す URIs などを検索します。 |

これらの変更が検出されたときは常にアラートを生成します。

### <a name="appid-uri-added-modified-or-removed"></a>AppID URI が追加、変更、または削除された


| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| AppID URI の変更| 高| Azure AD ログ| Service-Core ディレクトリ、Category-ApplicationManagement<br>アクティビティ: 更新<br>Application<br>アクティビティ: サービス プリンシパルの更新| URI の追加、変更、削除など、AppID URI の変更を探します。 |


これらの変更が、承認済みの変更管理手順の外部で検出されたときは常にアラートを生成します。

### <a name="new-owner"></a>新しい所有者


| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| アプリケーション所有者に対する変更| Medium| Azure AD ログ| Service-Core ディレクトリ、Category-ApplicationManagement<br>アクティビティ: アプリケーションへの所有者の追加| 通常の変更管理アクティビティの外部で、アプリケーション所有者として追加されているユーザーのインスタンスを探します。 |

### <a name="logout-url-modified-or-removed"></a>ログアウト URL が変更または削除された

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
|-|-|-|-|-|
| ログアウト URL の変更| 低| Azure AD ログ| Service-Core ディレクトリ、Category-ApplicationManagement<br>アクティビティ: アプリケーションの更新<br>および<br>アクティビティ: サービス プリンシパルの更新| サインアウト URL に対する変更を探します。 空のエントリまたは存在しない場所へのエントリがあると、ユーザーによるセッションの終了が停止されます。 |

## <a name="additional-resources"></a>その他のリソース

役に立つリソースへのリンクを次に示します。

* Github Azure AD ツールキット - [https://github.com/microsoft/AzureADToolkit](https://github.com/microsoft/AzureADToolkit)

* Azure Key Vault の概要とセキュリティ ガイダンス - [Azure Key Vault セキュリティの概要](../../key-vault/general/security-features.md)

* Solorgate リスクの情報とツール - [Solorgate リスクへのアクセスに役立つ Azure AD ブック](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* OAuth 攻撃検出ガイダンス - [OAuth アプリへの資格情報の異常な追加](/cloud-app-security/investigate-anomaly-alerts)

SIEM に対する Azure AD 監視構成情報 - [Azure Monitor とパートナー ツールの統合](../..//azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

 ## <a name="next-steps"></a>次のステップ

これらのセキュリティ運用ガイドの記事を参照してください。

[Azure AD セキュリティ運用の概要](security-operations-introduction.md)

[ユーザー アカウントのためのセキュリティ運用](security-operations-user-accounts.md)

[特権アカウントのためのセキュリティ運用](security-operations-privileged-accounts.md)

[Privileged Identity Management のためのセキュリティ運用](security-operations-privileged-identity-management.md)

[アプリケーションのためのセキュリティ運用](security-operations-applications.md)

[デバイスのためのセキュリティ運用](security-operations-devices.md)

 
[インフラストラクチャのためのセキュリティ運用](security-operations-infrastructure.md)
