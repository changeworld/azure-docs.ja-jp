---
title: インフラストラクチャのための Azure Active Directory セキュリティ運用
description: セキュリティの脅威を特定するために、インフラストラクチャ コンポーネントの監視とアラートを行う方法について説明します。
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
ms.openlocfilehash: c96362b522100d27757618e330f45514e4d442df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349245"
---
# <a name="security-operations-for-infrastructure"></a>インフラストラクチャのためのセキュリティ運用

インフラストラクチャには、適切に構成されていないと脆弱性が発生するおそれがある多くのコンポーネントがあります。 インフラストラクチャの監視とアラートの戦略の一環として、次の領域においてイベントの監視とアラートを行ないます。

* 認証と承認

* フェデレーション サーバーを含むハイブリッド認証コンポーネント

* ポリシー 

* サブスクリプション

認証インフラストラクチャのコンポーネントを監視し、アラートを生成することが重要です。 セキュリティが侵害されると、環境全体が完全に侵害される可能性があります。 Azure AD を使用する多くの企業は、ハイブリッド認証環境で運用しています。 これは、クラウドとオンプレミスの両方のコンポーネントを監視とアラートの戦略に含める必要があることを意味します。 ハイブリッド認証環境を使用すると、環境に別の攻撃ベクトルももたらされます。

すべてのコンポーネントおよびそれを管理するために用いられるアカウントは、コントロール プレーンあるいは階層 0 の資産と見なすことをお勧めします。 環境の設計と実装についてのガイダンスは、[特権資産のセキュリティ保護](/security/compass/overview) (SPA) に関する記事を参照してください。 このガイダンスには、Azure AD テナントで使用される可能性があるハイブリッド認証コンポーネントのそれぞれに関する推奨事項が記載されています。

予期しないイベントや潜在的な攻撃を検出できるようにするための最初の手順は、ベースラインを確立することです。 この記事に記載されているすべてのオンプレミス コンポーネントについては、「[特権アクセスの展開](/security/compass/privileged-access-deployment)」を参照してください。これは、特権資産のセキュリティ保護 (SPA) ガイダンスの一部です。

## <a name="where-to-look"></a>確認先

調査と監視に使用するログ ファイルは次のとおりです。 

* [Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md)

* [サインイン ログ](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 監査ログ](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault のログ](../../key-vault/general/logging.md?tabs=Vault)

Azure portal から、Azure AD 監査ログを表示したり、コンマ区切り値 (CSV) または JavaScript Object Notation (JSON) ファイルとしてダウンロードしたりできます。 Azure portal には、Azure AD ログを他のツールと統合する方法がいくつか用意されており、監視とアラートの自動化を強化することができます。

* [Microsoft Sentinel](../../sentinel/overview.md) – セキュリティ情報イベント管理 (SIEM) 機能を備え、エンタープライズ レベルでインテリジェントにセキュリティを分析します。 

* [Azure Monitor](../../azure-monitor/overview.md) – さまざまな状況の自動化された監視とアラートを有効にできます。 ブックを作成または使用して、異なるソースのデータを結合できます。

* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) と SIEM の統合 - Azure Event Hub 統合を介して、Splunk、ArcSight、QRadar、Sumo Logic など、[他の SIEM に Azure AD ログを統合できます](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。

* [Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security) – アプリを検出し、アプリを管理し、すべてのアプリとリソースを制御し、クラウド アプリのコンプライアンス状況を確認できます。 

この記事の残りの部分で、監視とアラートが必要な対象について説明し、脅威の種類別にまとめています。 特定の事前構築済みソリューションがある場合は、表の後にリンクを示しています。 それ以外の場合は、前述のツールを使用してアラートを作成できます。

## <a name="authentication-infrastructure"></a>認証インフラストラクチャ

オンプレミスとクラウドベースの両方のリソースとアカウントを含むハイブリッド環境では、Active Directory インフラストラクチャは認証スタックの重要な部分です。 スタックは攻撃のターゲットでもあるため、セキュリティで保護された環境を維持するように構成し、適切に監視する必要があります。 例に挙げた認証インフラストラクチャに使用される最新の攻撃の種類では、パスワード スプレーと Solorigate 手法が使用されます。 推奨される記事へのリンクを次に示します。

* [特権アクセスのセキュリティ保護の概要](/security/compass/overview) – この記事では、セキュリティで保護された特権アクセスを作成および維持するために、ゼロ トラスト手法を使用する最新手法の概要を説明します。

* [Microsoft Defender for Identity が監視するドメイン アクティビティ](/defender-for-identity/monitored-activities) - この記事では、監視しアラートを設定するアクティビティの包括的な一覧を示します。 

* [Microsoft Defender for Identity のセキュリティ アラート チュートリアル](/defender-for-identity/understanding-security-alerts) - この記事では、セキュリティ アラート戦略の作成と実装に関するガイダンスを提供します。

認証インフラストラクチャの監視とアラートに焦点を当てた具体的な記事へのリンクを次に示します。

* [Microsoft Defender for Identity での横移動パスの理解と使用](/defender-for-identity/use-case-lateral-movement-path) - この記事では、機密性の低いアカウントを使用してネットワーク全体の機密性の高いアカウントにアクセスされたときに識別できる検出方法について説明します。

* [Microsoft Defender for Identity でのセキュリティ アラートの処理](/defender-for-identity/working-with-suspicious-activities) - この記事では、アラートがログに記録された後に、アラートを確認して管理する方法について説明します。 

 具体的な項目について以下で説明します。

| [What to monitor] (監視対象)| リスク レベル| Where| メモ |
| - | - | - | - |
| エクストラネットのロックアウトの傾向| 高| Azure AD Connect Health| エクストラネットのロックアウトの傾向を検出するためのツールと手法については、「[Azure AD Connect Health を使用した AD FS の監視](../hybrid/how-to-connect-health-adfs.md)」にある情報を参照してください。 |
| 失敗したサインイン|高 | Connect Health ポータル| 危険な IP のレポートをエクスポートもしくはダウンロードし、「[危険な IP のレポート (パブリック レビュー)](../hybrid/how-to-connect-health-adfs-risky-ip.md)」にあるガイダンスに従って次のステップに進んでください。 |
| プライバシー準拠| 低| Azure AD Connect Health| 「[ユーザー プライバシーと Azure AD Connect Health](../hybrid/reference-connect-health-user-privacy.md)」の記事を使用して Azure AD Connect Health を構成し、データ コレクションと監視を無効化します。 |
| LDAP に対するブルート フォース攻撃の可能性| Medium| Microsoft Defender for Identity| センサーを使用して、LDAP に対する潜在的なブルート フォース攻撃を検出できます。 |
| アカウント列挙の偵察| Medium| Microsoft Defender for Identity| センサーを使用して、アカウントの列挙攻撃による偵察を実行できます。 |
| Azure AD と Azure AD FS の間の一般的な相関関係|Medium | Microsoft Defender for Identity| Azure AD と Azure AD FS 環境の間でアクティビティを関連付ける機能を使用します。 |


 

### <a name="pass-through-authentication-monitoring"></a>パススルー認証に対する監視 

Azure Active Directory (Azure AD) パススルー認証では、オンプレミスの Active Directory に対してパスワードを直接検証することで、ユーザーをサインインします。 

具体的な項目について以下で説明します。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - | - | - | - |
| Azure AD パススルー認証エラー|Medium | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80001 - Unable to connect to Active Directory (Active Directory に接続できません)| エージェント サーバーが、パスワードを検証する必要のあるユーザーと同じ AD フォレストのメンバーであり、Active Directory に接続できることを確認します。 |
| Azure AD パススルー認証エラー| Medium| Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS8002 - A timeout occurred connecting to Active Directory (Active Directory への接続中にタイムアウトが発生しました)| Active Directory が使用可能で、エージェントからの要求に応答していることを確認します。 |
| Azure AD パススルー認証エラー|Medium | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80004 - The username passed to the agent was not valid (エージェントに渡されたユーザー名が無効です)| サインインしようとしているユーザーのユーザー名が正しいことを確認してください。 |
| Azure AD パススルー認証エラー|Medium | Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80005 - Validation encountered unpredictable WebException (検証で予測外の WebException が発生しました)| 一時的なエラーです。 要求をやり直してください。 引き続きエラーが発生する場合は、Microsoft サポートに連絡してください。 |
| Azure AD パススルー認証エラー| Medium| Application and ‎Service Logs\Microsoft\A‎zureAdConnec‎t\Authenticatio‎nAgent\Admin| AADSTS80007 - An error occurred communicating with Active Directory (Active Directory との通信中にエラーが発生しました)| Check the agent logs for more information and verify that Active Directory is operating as expected. (エージェント ログで詳細を確認し、Active Directory が期待通りに動作していることを確認してください。) |
| Azure AD パススルー認証エラー|高 | Win32 LogonUserA 関数 API| Logon events 4624(s): アカウントが正常にログオンしました<br>- 次と関連 -<br>4625(F): アカウントがログオンに失敗しました| 要求を認証しているドメイン コントローラーで、疑わしいユーザー名に使用します。 [LogonUserA 関数 (winbase.h)](/windows/win32/api/winbase/nf-winbase-logonusera) におけるガイダンス |
| Azure AD パススルー認証エラー| Medium| ドメイン コントローラーの PowerShell スクリプト| 表の下にあるクエリを参照してください。 | さらにガイダンスが必要な場合は、「[Azure AD Connect: パススルー認証のトラブルシューティング](../hybrid/tshoot-connect-pass-through-authentication.md)」にある情報を使用します。 |

```Kusto

<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>

</Query>

</QueryList>
```

### <a name="appproxy-connector"></a>AppProxy コネクタ

Azure AD と Azure AD アプリケーション プロキシを使用すると、リモート ユーザーにシングル サインオン (SSO) エクスペリエンスが提供されます。 仮想プライベート ネットワーク (VPN) またはデュアル ホーム サーバーとファイアウォール規則を使用しなくても、ユーザーはオンプレミス アプリケーションに安全に接続します。 Azure AD アプリケーション プロキシ コネクタ サーバーが侵害された場合、攻撃者によって SSO エクスペリエンスが変更されたり、公開されたアプリケーションへのアクセス権が変更されたりするおそれがあります。 

アプリケーション プロキシの監視を構成するには、「[アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](../app-proxy/application-proxy-troubleshoot.md)」を参照してください。 情報を記録するデータ ファイルは、Application and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin にあります。監査アクティビティの完全なリファレンス ガイドについては、「[Azure AD 監査アクティビティのリファレンス](../reports-monitoring/reference-audit-activities.md)」を参照してください。 具体的な監視項目は次の通りです。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - | - | - | - |
| Kerberos のエラー| Medium | 各種のツール| Medium | 「[アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](../app-proxy/application-proxy-troubleshoot.md)」の「Kerberos エラー」に Kerberos 認証エラーのガイダンスがあります。 |
| DC セキュリティに関する問題| 高| DC セキュリティ監査ログ| Event ID 4742(S): コンピューター アカウントが変更されました<br>および<br>フラグ - 委任に対して信頼されている<br>または<br>フラグ – 委任の認証に対して信頼されている| フラグの変更を調査します。 |
| Pass-the-Ticket と同様の攻撃| 高| | | 次のガイダンスに従ってください。<li>[セキュリティ プリンシパルによる偵察 (LDAP) (外部 ID 2038)](/defender-for-identity/reconnaissance-alerts)<li>[チュートリアル:資格証明の侵害のアラート](/defender-for-identity/compromised-credentials-alerts)<li> [Microsoft Defender for Identity で横移動パスを理解して使用する](/defender-for-identity/use-case-lateral-movement-path)<li> [エンティティのプロファイルを理解する](/defender-for-identity/entity-profiles) |


### <a name="legacy-authentication-settings"></a>レガシの認証設定

多要素認証 (MFA) を有効にするには、レガシ認証をブロックする必要もあります。 次に、環境を監視し、レガシ認証の使用に対してアラートを生成する必要があります。 POP、SMTP、IMAP、MAPI などのレガシ認証プロトコルでは MFA を強制できないからです。 そのために、これらのプロトコルは組織の攻撃者が好むエントリ ポイントとなります。 レガシ認証をブロックするために使用できるツールの詳細については、「[組織内のレガシ認証をブロックする新しいツール](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302)」を参照してください。 

レガシ認証は、イベントの詳細の一部として Azure AD サインイン ログに取得されます。 Azure Monitor ブックを使用して、レガシ認証の使用状況を識別できます。 詳細情報については、「[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](../reports-monitoring/howto-use-azure-monitor-workbooks.md)」の「[レガシ認証を使用したサインイン](../reports-monitoring/howto-use-azure-monitor-workbooks.md)」を参照してください。 Microsoft Sentinel の安全でないプロトコル ブックを使用することもできます。 詳細については、[Microsoft Sentinel の安全でないプロトコル ブックの実装ガイド](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-insecure-protocols-workbook-implementation-guide/ba-p/1197564)を参照してください。 監視する具体的なアクティビティは次の通りです。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - | - | - | - |
| レガシ認証|高 | Azure AD サインイン ログ| ClientApp: POP<br>ClientApp: IMAP<br>ClientApp: MAPI<br>ClientApp: SMTP<br>ClientApp: ActiveSync go to EXO<br>Other Clients = SharePoint and EWS| フェデレーション ドメイン環境では、失敗した認証は記録されないため、ログには表示されません。 |


## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect は、オンプレミスとクラウドベースの Azure AD 環境の間でアカウントと属性の同期を可能にする一元的な場所を提供します。 Azure AD Connect は、ハイブリッド ID の目標に適合し、それを達成するように設計された Microsoft のツールです。 また、以下のような特徴があります。

* [パスワード ハッシュ同期](../hybrid/whatis-phs.md) - ユーザーのオンプレミス AD パスワードのハッシュを Azure AD と同期させるサインイン方法。

* [同期](../hybrid/how-to-connect-sync-whatis.md) - ユーザー、グループ、およびその他のオブジェクトを作成する役割を果たします。 また、オンプレミスのユーザーやグループの ID 情報をクラウド側と一致させる役割もあります。 この同期にはパスワード ハッシュも含まれます。

* [正常性の監視](../hybrid/whatis-azure-ad-connect.md) - Azure AD Connect Health は、堅牢な監視を提供したり、このアクティビティを表示するための Azure Portal 内の中央の場所を提供したりできます。

オンプレミス環境とクラウド環境の間で ID を同期させることで、オンプレミスとクラウドベースの環境に新しい攻撃面がもたらされます。 以下のことが推奨されます。

* Azure AD Connect のプライマリ サーバーとステージング サーバーを、コントロール プレーンの階層 0 システムとして扱います。 

* 環境内で各種のアカウントとその使用状況を管理する標準のポリシー セットに従います。

*  Azure AD Connect と Connect Health をインストールします。 これらは主に、環境の運用データを提供します。 

Azure AD Connect 操作のログ記録は、次のようにさまざまな方法で行われます。

* Azure AD Connect ウィザードでは、データが \ProgramData\AADConnect に記録されます。 ウィザードが起動されるたびに、タイムスタンプ付きのトレース ログ ファイルが作成されます。 トレース ログは、解析用に Sentinel または他のサードパーティのセキュリティ情報とイベント管理 (SIEM) ツールにインポートできます。

* 一部の操作では、ログ情報を取り込むために PowerShell スクリプトを開始します。 このデータを収集するには、スクリプト ブロックのログが有効になっていることを確認する必要があります。

### <a name="monitoring-configuration-changes"></a>構成の変更の監視

Azure AD は Microsoft SQL Server データ エンジンまたは SQL を使用して Azure AD Connect 構成情報を格納します。 そのため、構成に関連付けられているログ ファイルの監視と監査は、監視と監査の戦略に含める必要があります。 具体的には、監視とアラートの戦略に次の表を含めます。

| [What to monitor] (監視対象)| Where| メモ |
| - | - | - |
| mms_management_agent| SQL サービス監査レコード| 「[SQL Server 監査レコード](/sql/relational-databases/security/auditing/sql-server-audit-records)」を参照してください |
| mms_partition| SQL サービス監査レコード| 「[SQL Server 監査レコード](/sql/relational-databases/security/auditing/sql-server-audit-records)」を参照してください |
| mms_run_profile| SQL サービス監査レコード| 「[SQL Server 監査レコード](/sql/relational-databases/security/auditing/sql-server-audit-records)」を参照してください |
| mms_server_configuration| SQL サービス監査レコード| 「[SQL Server 監査レコード](/sql/relational-databases/security/auditing/sql-server-audit-records)」を参照してください |
| mms_synchronization_rule| SQL サービス監査レコード| 「[SQL Server 監査レコード](/sql/relational-databases/security/auditing/sql-server-audit-records)」を参照してください |


監視すべき構成情報とその方法の詳細については、次を参照してください。

* SQL Server の場合は、「[SQL Server 監査レコード](/sql/relational-databases/security/auditing/sql-server-audit-records)」を参照してください。

* Microsoft Sentinel の場合、[Windows サーバーに接続してセキュリティ イベントを収集する](/sql/relational-databases/security/auditing/sql-server-audit-records)方法に関するページを参照してください。 

* Azure AD Connect の構成と使用の詳細については、「[Azure AD Connect とは](../hybrid/whatis-azure-ad-connect.md)」を参照してください

### <a name="monitoring-and-troubleshooting-synchronization"></a>同期の監視とトラブルシューティング

 Azure AD Connect の機能の 1 つに、ユーザーのオンプレミスのパスワードと Azure AD の間のハッシュ同期があります。 パスワードが想定どおりに同期されない場合、その同期によって、一部またはすべてのユーザーに影響を及ぼすおそれがあります。 適切な操作を確認したり、問題のトラブルシューティングを行ったりするには、次の情報を使用します。

* ハッシュ同期の確認とトラブルシューティングについては、「[Azure AD Connect Sync を使用したパスワード ハッシュ同期のトラブルシューティング](../hybrid/tshoot-connect-password-hash-synchronization.md)」を参照してください。 

* コネクタ スペースへの変更については、「[Azure AD Connect オブジェクトと属性のトラブルシューティング](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)」を参照してください。 

**監視に関する重要なリソース**

| [What to monitor] (監視対象) | リソース |
| - | - |
| ハッシュ同期の検証|「[Azure AD Connect Sync を使用したパスワード ハッシュ同期のトラブルシューティング](../hybrid/tshoot-connect-password-hash-synchronization.md)」を参照してください |
 コネクタ スペースの変更|「[Azure AD Connect オブジェクトと属性のトラブルシューティング](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)」を参照してください |
| 構成した規則に対する変更| 具体的には、フィルタリングの変更、ドメインと OU の変更、属性の変更、およびグループベースの変更を監視します |
| SQL と MSDE の変更 | ログ パラメーターの変更とカスタム関数の追加 |

**以下を監視します**。 

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - | - | - | - |
| スケジューラの変更|高 | PowerShell| Set-ADSyncScheduler| スケジュールへの変更を検索する |
| スケジュールされたタスクの変更| 高 | Azure AD 監査ログ| Activity = 4699(S): スケジュールされたタスクが削除されました<br>または<br>Activity = 4701(s): スケジュールされたタスクが無効化されました<br>または<br>Activity = 4701(s): スケジュールされたタスクが更新されました| すべてを監視する |



* PowerShell スクリプト操作のログの詳細については、PowerShell リファレンス ドキュメントの一部である「[スクリプト ブロックのログ記録を有効にする](/powershell/module/microsoft.powershell.core/about/about_logging_windows)」を参照してください。

* Splunk による解析のために PowerShell のログを構成する方法の詳細については、「[Get Data into Splunk User Behavior Analytics](https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell)」を参照してください。

### <a name="monitoring-seamless-single-sign-on"></a>シームレス シングル サインオンの監視

Azure Active Directory (Azure AD) シームレス シングル サインオン (シームレス SSO) により、ユーザーは企業ネットワークにつながっている会社のデスクトップを使用するときに自動でサインインできます。 シームレス SSO により、オンプレミス コンポーネントの追加を必要とすることなく、ユーザーはクラウド ベースのアプリケーションに簡単にアクセスできるようになります。 SSO では、Azure AD Connect によって提供されるパススルー認証とパスワード ハッシュ同期機能を使用します。

シングル サインオンと Kerberos アクティビティの監視は、資格情報の盗用に関する一般的な攻撃パターンを検出するのに役立ちます。 次の情報を使用して監視してください。

| [What to monitor] (監視対象)| リスク レベル| Where| フィルターまたはサブフィルター| メモ |
| - | - | - | - | - |
| SSO および Kerberos 検証の失敗に関連するエラー|Medium | Azure AD サインイン ログ| | シングル サインオンに関するエラー コードの一覧は、[シングル サインオン](../hybrid/tshoot-connect-sso.md)に関する記事を参照してください。 |
| エラーのトラブルシューティングに関するクエリ|Medium | PowerShell| 表の下にあるクエリを参照してください。 SSO を有効化してフォレストにチェックインします。| SSO を有効化してフォレストにチェックインします。 |
| Kerberos 関連のイベント|高 | Microsoft Defender for Identity の監視| | 「[Microsoft Defender for Identity の横移動パス (LMP)](/defender-for-identity/use-case-lateral-movement-path)」で利用可能なガイダンスを確認します |

```kusto
<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>

</Query>

</QueryList>
```
## <a name="password-protection-policies"></a>パスワード保護ポリシー

Azure AD パスワード保護をデプロイする場合、監視とレポートは重要なタスクです。 以下のリンクでは、各サービスが情報をログに記録する場所や、Azure AD パスワード保護の使用についてレポートを作成する方法など、さまざまな監視手法を理解できるように詳しく説明します。 

ドメイン コントローラー (DC) エージェントとプロキシ サービスは両方とも、イベント ログ メッセージを記録します。 以下で説明するすべての PowerShell コマンドレットは、プロキシ サーバーでのみ使用できます (AzureADPasswordProtection PowerShell モジュールを参照)。 DC エージェント ソフトウェアでは、PowerShell モジュールはインストールされません。

オンプレミスのパスワード保護の計画と実装の詳細については、「[オンプレミスの Azure Active Directory パスワード保護を計画してデプロイする](../authentication/howto-password-ban-bad-on-premises-deploy.md)」を参照してください。 監視の詳細については、[オンプレミスの Azure AD パスワード保護の監視](../authentication/howto-password-ban-bad-on-premises-monitor.md)に関する記事を参照してください。 各ドメイン コントローラーでは、DC エージェント サービス ソフトウェアによって、各個人のパスワード検証操作の結果 (およびその他の状態) が次のローカルのイベント ログに書き込まれます。

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

* \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

DC エージェント管理ログは、ソフトウェアの動作に関する情報の主要なソースです。 既定では、トレース ログはオフであり、データをログに記録する前に有効にする必要があります。 アプリケーション プロキシの問題とエラー メッセージのトラブルシューティングを行う場合、詳しい情報は、[Azure Active Directory アプリケーション プロキシのトラブルシューティング](../app-proxy/application-proxy-troubleshoot.md)に関する記事で確認できます。 これらのイベントの情報は次に記録されます。

* Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin

* Azure AD 監査ログ、カテゴリ アプリケーション プロキシ

Azure AD 監査アクティビティの完全なリファレンスは、[Azure Active Directory (Azure AD) 監査アクティビティ リファレンス](../reports-monitoring/reference-audit-activities.md)に関する記事で確認できます。 

## <a name="next-steps"></a>次のステップ


次のセキュリティ運用ガイドの記事もご覧ください。

[Azure AD セキュリティ運用の概要](security-operations-introduction.md)

[ユーザー アカウントのためのセキュリティ運用](security-operations-user-accounts.md)

[特権アカウントのためのセキュリティ運用](security-operations-privileged-accounts.md)

[Privileged Identity Management のためのセキュリティ運用](security-operations-privileged-identity-management.md)

[アプリケーションのためのセキュリティ運用](security-operations-applications.md)

[デバイスのためのセキュリティ運用](security-operations-devices.md)
 
[インフラストラクチャのためのセキュリティ運用](security-operations-infrastructure.md)


 

  
‎
