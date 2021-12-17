---
title: Microsoft Sentinel データ コネクタを見つける | Microsoft Docs
description: Microsoft Sentinel データ コネクタの具体的な構成手順について説明します。
services: sentinel
documentationcenter: na
author: batamig
ms.service: microsoft-sentinel
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: c5ba2be13c48c85786f2ab916eaf9a37c649a5e9
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518567"
---
# <a name="find-your-microsoft-sentinel-data-connector"></a>Microsoft Azure Sentinel データ コネクタを見つける

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel にデータ コネクタをデプロイする方法について説明します。サポート対象ですぐに使えるデータ コネクタをすべて列挙し、一般的なデプロイ手順へのリンクと、特定のコネクタに必要な追加手順を示します。

> [!TIP]
> 一部のデータ コネクタは、ソリューションによってのみデプロイされます。 詳細については、[Microsoft Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)に関するページを参照してください。 また、[Microsoft Sentinel の GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors)で、コミュニティで作成された他のデータ コネクタを見つけることもできます。
>

## <a name="how-to-use-this-guide"></a>このガイドを使用する方法

1. まず、右側の見出しメニューで、製品、サービス、またはデバイスのコネクタを見つけて選択します。

    各コネクタに関して表示される 1 つ目の情報は、その **データ インジェスト方法** です。 そこに表示される方法は、次の一般的なデプロイ手順の 1 つへのリンクになっており、データ ソースを Microsoft Sentinel に接続するために必要な情報のほとんどを確認できます。

    | データ インジェスト方法 | 手順を含むリンクされた記事 |
    | --- | --- |
    | **Azure サービス間の統合** | [Azure、Windows、Microsoft、Amazon サービスへの接続](connect-azure-windows-microsoft-services.md) |
    | **Syslog を介した Common Event Format (CEF)** | [デバイスまたはアプライアンスの CEF 形式のログを Microsoft Sentinel に取得する](connect-common-event-format.md) |
    | **Microsoft Sentinel データ コレクター API** | [データ ソースを Microsoft Sentinel のデータ コレクター API に接続してデータを取り込む](connect-rest-api-template.md) |
    | **Azure Functions と REST API** | [Azure Functions を使用して Microsoft Sentinel をデータ ソースに接続する](connect-azure-functions-template.md) |
    | **Syslog** | [Syslog を使用して Linux ベースのソースからデータを収集する](connect-syslog.md) |
    | **カスタム ログ** | [Log Analytics エージェントを使用してカスタム ログ形式のデータを Microsoft Sentinel に収集する](connect-custom-logs.md) |
    |

    > [!NOTE]
    > **Azure サービス間の統合** データ インジェスト方法は、コネクタの種類に応じて、その記事の 3 つの異なるセクションにリンクしています。 以下の各コネクタのセクションでは、その記事内のリンク先のセクションを示しています。

1. 特定のコネクタをデプロイする場合は、その **データ インジェスト方法** にリンクされている適切な記事を選択し、以下の関連セクションの情報と追加のガイダンスを使用して、その記事の情報を補足します。

> [!TIP]
>
> - 多くのデータ コネクタは、[Microsoft Sentinel ソリューション](sentinel-solutions.md)の一部として、関連する分析ルール、ブック、プレイブックと共にデプロイすることもできます。 詳細については、[Microsoft Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)に関するページを参照してください。
>
> - その他のデータ コネクタは、Microsoft Sentinel コミュニティから提供され、Azure Marketplace で見つけることができます。 コミュニティ データ コネクタに関するドキュメントは、コネクタを作成した組織によって作成されます。
>
> - 一覧に表示されていない、または現在サポートされていないデータ ソースがある場合は、独自のカスタム コネクタを作成することもできます。 詳細については、「[Microsoft Sentinel カスタム コネクタを作成するためのリソース](create-custom-connector.md)」を参照してください。

> [!IMPORTANT]
> Microsoft Sentinel データ コネクタは、現在 **プレビュー** 段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari のフィッシング対策とブランド保護 (プレビュー)

| コネクタ属性 | 説明|
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) <br><br>**デプロイ前**: [Security Graph API を有効にする (省略可能)](#enable-the-security-graph-api-optional)。 <br>**デプロイ後**: [関数アプリに必要なアクセス許可を割り当てる](#assign-necessary-permissions-to-your-function-app)|
| **Log Analytics テーブル** | agari_bpalerts_log_CL<br>agari_apdtc_log_CL<br>agari_apdpolicy_log_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-agari-functionapp |
| **API 資格情報** | <li>クライアント ID<li>クライアント シークレット<li>(省略可能: Graph テナント ID、Graph クライアント ID、Graph クライアント シークレット) |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[クイック スタート](https://developers.agari.com/agari-platform/docs/quick-start)<li>[Agari の開発者サイト](https://developers.agari.com/agari-platform) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPS) |
| **アプリケーションの設定** | <li>clientID<li>clientSecret<li>workspaceID<li>workspaceKey<li>enableBrandProtectionAPI (true/false)<li>enablePhishingResponseAPI (true/false)<li>enablePhishingDefenseAPI (true/false)<li>resGroup (リソース グループを入力)<li>functionName<li>subId (サブスクリプション ID のを入力)<li>enableSecurityGraphSharing (true/false、下記参照)<br>enableSecurityGraphSharing が true に設定されている場合は必須 (下記参照)。<li>GraphTenantId<li>GraphClientId<li>GraphClientSecret<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support) |
| | |

### <a name="enable-the-security-graph-api-optional"></a>Security Graph API を有効にする (省略可能)

> [!IMPORTANT]
> この手順を実行する場合は、データ コネクタをデプロイする前に実行してください。
>
Agari 関数アプリを使用すると、セキュリティ Graph API を介して Microsoft Sentinel と脅威インテリジェンスを共有できます。 この機能を使用するには、[Sentinel 脅威インテリジェンス プラットフォーム コネクタ](./connect-threat-intelligence-tip.md)を有効にし、Azure Active Directory で[アプリケーションを登録する](/graph/auth-register-app-v2)必要があります。

このプロセスでは、[関数アプリをデプロイ](connect-azure-functions-template.md)するときに使用する 3 つの情報 (**Graph テナント ID**、**Graph クライアント ID**、**Graph クライアント シークレット**) が提供されます (上の表の「*アプリケーション設定*」を参照してください)。

### <a name="assign-necessary-permissions-to-your-function-app"></a>関数アプリに必要なアクセス許可を割り当てる

Agari コネクタでは、環境変数を使用してログ アクセスのタイムスタンプを格納します。 アプリケーションでこの変数に書き込むには、システムに割り当てられている ID にアクセス許可を割り当てる必要があります。

1. Azure portal で **[関数アプリ]** に移動します。
1. **[関数アプリ]** ページで、一覧から関数アプリを選択し、関数アプリのナビゲーション メニューの **[設定]** で **[ID]** を選択します。
1. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に設定します。
1. **[保存]** を選択すると、 **[Azure でのロールの割り当て]** ボタンが表示されます。 それを選択します。
1. **[Azure でのロールの割り当て]** 画面で、 **[ロールの割り当ての追加]** を選択します。 **[スコープ]** を **[サブスクリプション]** に設定し、 **[サブスクリプション]** ドロップダウンからサブスクリプションを選択し、 **[ロール]** を **[App Configuration データ所有者]** に設定します。
1. **[保存]** を選択します。


## <a name="ai-analyst-aia-by-darktrace-preview"></a>Darktrace による AI Analyst (AIA) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[AI Analyst 用に CEF ログ転送を構成する](#configure-cef-log-forwarding-for-ai-analyst) |
| **Log Analytics テーブル** | CommonSecurityLog |
| **サポートしているもの** | [Darktrace](https://customerportal.darktrace.com/) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-analyst"></a>AI Analyst 用に CEF ログ転送を構成する

Log Analytics エージェントを使用して CEF 形式の Syslog メッセージを Azure ワークスペースに転送するように Darktrace を構成します。

1. Darktrace Threat Visualizer 内で、メイン メニューの **[管理]** の下にある **[システム構成]** ページに移動します。
1. 左側のメニューから **[モジュール]** を選択し、使用可能な **[ワークフロー統合]** から **Microsoft Sentinel** を選択します。
1. 構成ウィンドウが開きます。 **Microsoft Sentinel Syslog CEF** を見つけて **[新規]** を選択し、構成設定を表示します (まだ表示されていない場合)。
1. **[サーバー構成]** フィールドにログ フォワーダーの場所を入力し、必要に応じて通信ポートを変更します。 選択したポートが 514 に設定され、中間ファイアウォールによって許可されていることを確認します。
1. 必要に応じて、アラートのしきい値、時間オフセット、または追加の設定を構成します。
1. 有効にする必要があり、Syslog 構文が変更されるその他の構成オプションを確認します。
1. **[アラートの送信]** を有効にして、変更を保存します。

## <a name="ai-vectra-detect-preview"></a>Al Vectra 検出 (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[AI Vectra 検出用に CEF ログ転送を構成する](#configure-cef-log-forwarding-for-ai-vectra-detect)|
| **Log Analytics テーブル** | CommonSecurityLog |
| **サポートしているもの** | [Vectra AI](https://www.vectra.ai/support) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-vectra-detect"></a>AI Vectra 検出用に CEF ログ転送を構成する

Log Analytics エージェントを使用して CEF 形式の Syslog メッセージを Microsoft Sentinel ワークスペースに転送するように Vectra (X シリーズ) エージェントを構成します。

Vectra インターフェイスから、[設定] > [通知] の順に移動して、[Edit Syslog configuration]\(Syslog 構成の編集\) を選択します。 以下の手順に従って、接続を設定します。

- 新しい宛先 (ログ フォワーダーのホスト名) を追加します
- ポートを **514** として設定します
- [プロトコル] を **[UDP]** に設定します
- 形式を **[CEF]** に設定します
- ログの種類を設定します (使用可能なすべてのログの種類を選択します)
- **[保存]** を選びます。

**[テスト]** ボタンを選択して、いくつかのテスト イベントをログ フォワーダーに強制的に送信できます。

詳細については、Detect UI のリソース ページからダウンロードできる Cognito Detect Syslog ガイドをご覧ください。

## <a name="akamai-security-events-preview"></a>Akamai セキュリティ イベント (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto 関数パーサー |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | AkamaiSIEMEvent |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-akamaisecurityevents-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [セキュリティ情報イベント管理 (SIEM) 統合の構成](https://developer.akamai.com/tools/integrations/siem)<br>[CEF コネクタを設定する](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)。 |
| **サポートしているもの** | [Akamai](https://www.akamai.com/us/en/support/) |
| | |

## <a name="alcide-kaudit"></a>Alcide kAudit

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | alcide_kaudit_activity_1_CL - Alcide kAudit アクティビティ ログ<br>alcide_kaudit_detections_1_CL - Alcide kAudit の検出<br>alcide_kaudit_selections_count_1_CL - Alcide kAudit アクティビティ数<br>alcide_kaudit_selections_details_1_CL - Alcide kAudit アクティビティの詳細 |
| **ベンダーのドキュメント/<br>インストール手順** | [Alcide kAudit インストール ガイド](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit) |
| **サポートしているもの** | [Alcide](https://www.alcide.io/company/contact-us/) |
| | |

## <a name="alsid-for-active-directory"></a>Active Directory 用の Alsid

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md) <br><br>[Alsid の追加構成](#extra-configuration-for-alsid)|
| **Log Analytics テーブル** | AlsidForADLog_CL |
| **Kusto 関数エイリアス:** | afad_parser |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-alsidforad-parser |
| **サポートしているもの** | [Alsid](https://www.alsid.com/contact-us/) |
| | |

### <a name="extra-configuration-for-alsid"></a>Alsid の追加構成

1. **Syslog サーバーを構成する**

    まず、Alsid for AD のログの送信先となる **Linux Syslog** サーバーが必要です。 通常は、**Ubuntu** で **rsyslog** を実行できます。 

    その後、このサーバーを必要に応じて構成できますが、AFAD ログを別のファイルに出力することをお勧めします。 または、[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/alsid-syslog-proxy/)を使用して、Syslog サーバーと Microsoft エージェントを自動的にデプロイすることもできます。 テンプレートを使用する場合は、[エージェントのインストール手順](connect-custom-logs.md#install-the-log-analytics-agent)をスキップできます。

1. **Syslog サーバーにログを送信するように Alsid を構成する**

    **Alsid for AD** ポータルで、 **[システム]** 、 **[構成]** 、 **[Syslog]** の順に選択します。 そこから、Syslog サーバーに対して新しい Syslog アラートを作成できます。

    新しい Syslog アラートを作成したら、ログがサーバー上の別のファイルに正しく収集されていることを確認します。 たとえば、ログを確認するには、AFAD の Syslog アラート構成の *[構成のテスト]* ボタンを使用できます。 クイックスタート テンプレートを使用した場合、Syslog サーバーでは、既定で TLS を使用せずにポート 514 (UDP) および 1514 (TCP) でリッスンします。

## <a name="amazon-web-services---cloudtrail"></a>アマゾン ウェブ サービス - CloudTrail

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[AWS CloudTrail を Microsoft Sentinel に接続する](connect-aws.md)** (上位のコネクタの記事) |
| **Log Analytics テーブル** | AWSCloudTrail |
| **サポートしているもの** | Microsoft |
| | |

## <a name="apache-http-server"></a>Apache HTTP Server

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md) |
| **Log Analytics テーブル** | ApacheHTTPServer_CL |
| **Kusto 関数エイリアス:** | ApacheHTTPServer |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-apachehttpserver-parser |
| **カスタム ログのサンプル ファイル:** | access.log または error.log |
| | |

## <a name="apache-tomcat"></a>Apache Tomcat

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md) |
| **Log Analytics テーブル** | Tomcat_CL |
| **Kusto 関数エイリアス:** | TomcatEvent |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-ApacheTomcat-parser |
| **カスタム ログのサンプル ファイル:** | access.log または error.log |
| | |

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto 関数パーサー |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | ArubaClearPass |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-arubaclearpass-parser |
| **ベンダーのドキュメント/<br>インストール手順** | Aruba の指示に従って [ClearPass を構成](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)します。 |
| **サポートしているもの** | Microsoft |
| | |

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence 監査 (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | Confluence_Audit_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-confluenceauditapi-functionapp |
| **API 資格情報** | <li>ConfluenceAccessToken<li>ConfluenceUsername<li>ConfluenceHomeSiteName |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[API ドキュメント](https://developer.atlassian.com/cloud/confluence/rest/api-group-audit/)<li>[資格情報を取得するための要件と手順](https://developer.atlassian.com/cloud/confluence/rest/intro/#auth)<li>[監査ログを表示する](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | ConfluenceAudit |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-confluenceauditapi-parser |
| **アプリケーションの設定** | <li>ConfluenceUsername<li>ConfluenceAccessToken<li>ConfluenceHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira 監査 (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | Jira_Audit_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-jiraauditapi-functionapp |
| **API 資格情報** | <li>JiraAccessToken<li>JiraUsername<li>JiraHomeSiteName |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[API ドキュメント - 監査レコード](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)<li>[資格情報を取得するための要件と手順](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#authentication) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | JiraAudit |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-jiraauditapi-parser |
| **アプリケーションの設定** | <li>JiraUsername<li>JiraAccessToken<li>JiraHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-active-directory"></a>Azure Active Directory

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Azure Active Directory データを Microsoft Sentinel に接続する](connect-azure-active-directory.md)** (上位のコネクタの記事) |
| **ライセンスの前提条件/<br>コスト情報** | <li>サインイン ログについては、Azure Active Directory P1 または P2 ライセンス<li>他のログの種類については、任意の Azure AD ライセンス (Free/O365/P1/P2)<br>その他の料金が適用される場合があります |
| **Log Analytics テーブル** | SigninLogs<br>AuditLogs<br>AADNonInteractiveUserSignInLogs<br>AADServicePrincipalSignInLogs<br>AADManagedIdentitySignInLogs<br>AADProvisioningLogs<br>ADFSSignInLogs |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **ライセンスの前提条件/<br>コスト情報** | [Azure AD Premium P2 サブスクリプション](https://azure.microsoft.com/pricing/details/active-directory/)<br>その他の料金が適用される場合があります |
| **Log Analytics テーブル** | SecurityAlert |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-activity"></a>Azure アクティビティ

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Azure Policy によって管理される診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)**<br><br>[新しい Azure アクティビティ コネクタにアップグレードする](#upgrade-to-the-new-azure-activity-connector) |
| **Log Analytics テーブル** | AzureActivity |
| **サポートしているもの** | Microsoft |
| | |

### <a name="upgrade-to-the-new-azure-activity-connector"></a>新しい Azure アクティビティ コネクタにアップグレードする

#### <a name="data-structure-changes"></a>データ構造の変更

このコネクタでは、最近、アクティビティ ログ イベントを収集するためのバックエンド メカニズムが変更されました。 現在は、**診断設定** パイプラインが使用されています。 このコネクタでまだ従来の方式を使用している場合は、新しいバージョンへの *アップグレードを強くお勧めします*。これにより、機能が強化され、リソース ログとの整合性が向上します。 以下の手順を参照してください。

**診断設定** 方式では、従来の方式でアクティビティ ログ サービスから送信していたものと同じデータを送信しますが、**AzureActivity** テーブルの [構造の変更](../azure-monitor/essentials/activity-log.md#data-structure-changes)がいくつかあります。

診断設定パイプラインへの移行によって向上する点の中で重要なものは次のとおりです。
- インジェストの待機時間の短縮 (イベントのインジェストに発生から 15 ～ 20 分かかっていたのが 2 ～ 3 分以内に)。
- 信頼性が向上します。
- パフォーマンスが向上します。
- アクティビティ ログ サービスによってログに記録されるイベントのすべてのカテゴリをサポートします (従来のメカニズムではサブセットのみをサポートしています。たとえば、サービス正常性イベントはサポートされていません)。
- Azure Policy を使用して大規模に管理します。

[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)と[診断設定パイプライン](../azure-monitor/essentials/diagnostic-settings.md)の詳細については、[Azure Monitor のドキュメント](../azure-monitor/logs/data-platform-logs.md)を参照してください。

#### <a name="disconnect-from-old-pipeline"></a>古いパイプラインからの切断

新しい Azure アクティビティ ログ コネクタを設定する前に、既存のサブスクリプションを従来方式から切断する必要があります。

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。 コネクタの一覧で **[Azure Activity]\(Azure アクティビティ\)** を選択し、右下にある **[Open connector page]\(コネクタ ページを開く\)** ボタンを選択します。

1. **[Instructions]\(手順\)** タブの **[構成]** セクションの手順 1 で、従来方式に接続している既存のサブスクリプションの一覧を確認して、新しい方式に追加するものを特定し、下にある **[Disconnect All]\(すべて切断\)** ボタンをクリックしてすべてを一度に切断します。

1. [上の表にリンクされている指示](connect-azure-windows-microsoft-services.md#diagnostic-settings-based-connections)に従って、新しいコネクタの設定を続けます。

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **ライセンスの前提条件/<br>コスト情報** | <li>[Azure の DDoS 標準保護プラン](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)が構成されている必要があります。<li>[Azure DDoS Standard が有効になっている仮想ネットワーク](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)が構成されている必要があります<br>その他の料金が適用される場合があります |
| **Log Analytics テーブル** | AzureDiagnostics |
| **推奨される診断** | DDoSProtectionNotifications<br>DDoSMitigationFlowLogs<br>DDoSMitigationReports |
| **サポートしているもの** | Microsoft |
| | |

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Microsoft Defender for Cloud からセキュリティ アラートを接続する](connect-azure-security-center.md)** (上位のコネクタの記事) |
| **Log Analytics テーブル** | SecurityAlert |
| **サポートしているもの** | Microsoft |
| | |

<a name="azure-defender-for-iot"></a>

## <a name="microsoft-defender-for-iot"></a>Microsoft Defender for IoT

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics テーブル** | SecurityAlert |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-firewall"></a>Azure Firewall

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics テーブル** | AzureDiagnostics |
| **推奨される診断** | AzureFirewallApplicationRule<br>AzureFirewallNetworkRule<br>AzureFirewallDnsProxy |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-information-protection"></a>Azure Information Protection

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure サービス間の統合**](connect-azure-windows-microsoft-services.md) |
| **Log Analytics テーブル** | InformationProtectionLogs_CL |
| **サポートしているもの** | Microsoft |
| | |

詳細については、[Azure Information Protection のドキュメント](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)をご覧ください。

## <a name="azure-key-vault"></a>Azure Key Vault

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Azure Policy によって管理される診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics テーブル** | KeyVaultData |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Azure Policy によって管理される診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics テーブル** | kube-apiserver<br>kube-audit<br>kube-audit-admin<br>kube-controller-manager<br>kube-scheduler<br>cluster-autoscaler<br>guard |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-sql-databases"></a>Azure SQL Databases

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Azure Policy によって管理される診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** <br><br>[Azure SQL および SQL PaaS 用 Microsoft Sentinel ソリューション](sentinel-solutions-catalog.md#azure)でも利用可能|
| **Log Analytics テーブル** | SQLSecurityAuditEvents<br>SQLInsights<br>AutomaticTuning<br>QueryStoreWaitStatistics<br>エラー<br>DatabaseWaitStatistics<br>Timeouts<br>Blocks<br>デッドロック<br>Basic<br>InstanceAndAppAdvanced<br>WorkloadManagement<br>DevOpsOperationsAudit |
| **サポートしているもの** | Microsoft |
| | |

## <a name="azure-storage-account"></a>Azure Storage アカウント

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)**<br><br>[ストレージ アカウントの診断設定の構成に関する注意事項](#notes-about-storage-account-diagnostic-settings-configuration) |
| **Log Analytics テーブル** | StorageBlobLogs<br>StorageQueueLogs<br>StorageTableLogs<br>StorageFileLogs |
| **推奨される診断** | **アカウント リソース**<li>トランザクション<br>**BLOB/キュー/テーブル/ファイル リソース**<br><li>StorageRead<li>StorageWrite<li>StorageDelete<li>トランザクション |
| **サポートしているもの** | Microsoft |
| | |

### <a name="notes-about-storage-account-diagnostic-settings-configuration"></a>ストレージ アカウントの診断設定の構成に関する注意事項

ストレージ アカウント (親) リソースの内部には、ファイル、テーブル、キュー、BLOB など、ストレージの種類ごとに他の (子) リソースがあります。

ストレージ アカウントの診断を構成する場合は、次の項目を順番に選択して構成する必要があります。
- **Transaction** メトリックをエクスポートする親アカウント リソース。
- すべてのログとメトリックをエクスポートする、子ストレージの種類の各リソース (上の表を参照)。

実際にリソースを定義したストレージの種類だけが表示されます。

## <a name="azure-web-application-firewall-waf"></a>Azure Web アプリケーション ファイアウォール (WAF)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[診断設定ベースの接続](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics テーブル** | AzureDiagnostics |
| **推奨される診断** | **Application Gateway**<br><li>ApplicationGatewayAccessLog<li>ApplicationGatewayFirewallLog<br>**Front Door**<li>FrontdoorAccessLog<li>FrontdoorWebApplicationFirewallLog<br>**CDN WAF ポリシー**<li>WebApplicationFirewallLogs |
| **サポートしているもの** | Microsoft |
| | |


## <a name="barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | CGFWFirewallActivity |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-barracudacloudfirewall-function |
| **ベンダーのドキュメント/<br>インストール手順** | https://aka.ms/Sentinel-barracudacloudfirewall-connector |
| **サポートしているもの** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="barracuda-waf"></a>Barracuda WAF

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md) |
| **Log Analytics テーブル** | syslog |
| **ベンダーのドキュメント/<br>インストール手順** | https://aka.ms/asi-barracuda-connector |
| **サポートしているもの** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | BetterMTDDeviceLog_CL<br>BetterMTDIncidentLog_CL<br>BetterMTDAppLog_CL<br>BetterMTDNetflowLog_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [BETTER MTD のドキュメント](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration)<br><br>脅威ポリシーのセットアップ。Microsoft Sentinel に報告されるインシデントを定義します。<br><ol><li>**BETTER MTD コンソール** で、サイド バーの **[ポリシー]** を選択します。<li>使用しているポリシーの **[編集]** ボタンを選択します。<li>ログに記録するインシデントの種類ごとに、 **[統合に送信]** フィールドに移動し、 **[Sentinel]** を選択します。 |
| **サポートしているもの** | [Better Mobile](mailto:support@better.mobi) |
| | |


## <a name="beyond-security-besecure"></a>Beyond Security beSECURE

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | beSECURE_ScanResults_CL<br>beSECURE_ScanEvents_CL<br>beSECURE_Audit_CL |
| **ベンダーのドキュメント/<br>インストール手順** | **[統合]** メニューにアクセスします。<br><ol><li>**[その他]** メニュー オプションを選択します。<li>**[サーバー]** を選択します<li>**[統合]** を選択します<li>Microsoft Sentinel を有効にします<li>beSECURE の構成で、 **[ワークスペース ID]** および **[主キー]** の値を貼り付けます。<li>**[変更]** を選択します。 |
| **サポートしているもの** | [Beyond Security](https://beyondsecurity.freshdesk.com/support/home) |
| | |


## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | CylancePROTECT |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-cylanceprotect-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [Cylance Syslog ガイド](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec データ損失防止 (DLP) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto 関数パーサー |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | SymantecDLP |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-symantecdlp-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [Syslog サーバーへのログ アクションの構成](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="check-point"></a>Check Point

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[Check Point ソリューション](sentinel-solutions-catalog.md#check-point)からも利用可能|
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Log Exporter - Check Point ログのエクスポート](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323) |
| **サポートしているもの** | [Check Point](https://www.checkpoint.com/support-services/contact-support/) |
| | |


## <a name="cisco-asa"></a>Cisco ASA

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[Cisco ASA ソリューション](sentinel-solutions-catalog.md#cisco)で利用可能|
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Cisco ASA シリーズ CLI 構成ガイド](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[Cisco Firepower eStreamer の追加構成](#extra-configuration-for-cisco-firepower-estreamer)|
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [eStreamer eNcore for Sentinel 操作ガイド](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html) |
| **サポートしているもの** | [Cisco](https://www.cisco.com/c/en/us/support/index.html)
| | |

### <a name="extra-configuration-for-cisco-firepower-estreamer"></a>Cisco Firepower eStreamer の追加構成

1. **Firepower eNcore クライアントをインストールする**  
Firepower eNcore eStreamer クライアントをインストールして構成します。 詳細については、[Cisco の完全なインストール ガイド](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html)をご覧ください。

1. **Firepower Connector を GitHub からダウンロードする**  
[Cisco GitHub リポジトリ](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector)から、Microsoft Sentinel 用の Firepower eNcore コネクタの最新バージョンをダウンロードします。 python3 を使用する予定の場合は、[python3 eStreamer コネクタ](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector/tree/python3)を使用します。

1. **Azure/VM IP アドレスを使用して pkcs12 ファイルを作成する**  
Firepower の **[システム] -> [統合] -> [eStreamer]** で、VM インスタンスのパブリック IP を使用して pkcs12 証明書を作成します。 詳細については、[インストール ガイド](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049443)をご覧ください。

1. **Azure/VM クライアントと FMC の間の接続をテストする**  
pkcs12 ファイルを FMC から Azure/VM インスタンスにコピーし、テスト ユーティリティ (./encore.sh test) を実行して、接続を確立できることを確認します。 詳細については、[設定ガイド](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049430)をご覧ください。

1. **エージェントにデータをストリーミングするように eNcore を構成する**  
TCP 経由で Log Analytics エージェントにデータをストリーミングするように eNcore を構成します。 この設定は既定で有効にする必要がありますが、ネットワークのセキュリティ体制に応じて、追加のポートとストリーミング プロトコルを構成できます。 データをファイル システムに保存することもできます。 詳細については、[eNcore の構成](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049433)に関するページをご覧ください。


## <a name="cisco-meraki-preview"></a>Cisco Meraki (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md)<br><br> [Cisco ISE ソリューション](sentinel-solutions-catalog.md#cisco)で利用可能|
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | CiscoMeraki |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-ciscomeraki-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [Meraki デバイスのレポートに関するドキュメント](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) <br><br> [Cisco Umbrella ソリューション](sentinel-solutions-catalog.md#cisco)で利用可能|
| **Log Analytics テーブル** | Cisco_Umbrella_dns_CL<br>Cisco_Umbrella_proxy_CL<br>Cisco_Umbrella_ip_CL<br>Cisco_Umbrella_cloudfirewall_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-CiscoUmbrellaConn-functionapp |
| **API 資格情報** | <li>AWS アクセス キー ID<li>AWS シークレット アクセス キー<li>AWS S3 バケット名 |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Amazon S3 へのログ記録](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | Cisco_Umbrella |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-ciscoumbrella-function |
| **アプリケーションの設定** | <li>WorkspaceID<li>WorkspaceKey<li>S3Bucket<li>AWSAccessKeyId<li>AWSSecretAccessKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | CiscoUCS |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-ciscoucs-function |
| **ベンダーのドキュメント/<br>インストール手順** | [Cisco UCS の Syslog を設定する - Cisco](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="citrix-analytics-security"></a>Citrix Analytics (セキュリティ)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | CitrixAnalytics_SAlerts_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Citrix を Microsoft Sentinel に接続する](https://aka.ms/Sentinel-Citrix-Connector) |
| **サポートしているもの** | [Citrix Systems](https://www.citrix.com/support/) |
| | |

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | WAF を構成するには、[サポート WIKI - WAF の構成と NetScaler](https://support.citrix.com/article/CTX234174) に関するページを参照してください。<br><br>CEF ログを構成するには、[アプリケーション ファイアウォールでの CEF のログ記録のサポート](https://support.citrix.com/article/CTX136146)に関するページを参照してください。<br><br>ログをプロキシに転送するには、[監査ログ用に Citrix ADC アプライアンスを構成](https://docs.citrix.com/en-us/citrix-adc/current-release/system/audit-logging/configuring-audit-logging.html)に関するページを参照してください。 |
| **サポートしているもの** | [Citrix Systems](https://www.citrix.com/support/) |
| | |


## <a name="cognni-preview"></a>Cognni (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | CognniIncidents_CL |
| **ベンダーのドキュメント/<br>インストール手順** | **Cognni に接続する**<br><ol><li>[Cognni の統合ページ](https://intelligence.cognni.ai/integrations)にアクセスします。<li>[Microsoft Sentinel] ボックスで、 **[Connect]\(接続\)** を選択します。<li>**WorkspaceId** と **sharedKey** (主キー) を Cognni の統合画面のフィールドに貼り付けます。<li>**[接続]** ボタンを選択して構成を完了します。 |
| **サポートしているもの** | [Cognni](https://cognni.ai/contact-support/)
| | |

## <a name="continuous-threat-monitoring-for-sap-preview"></a>Continuous Threat Monitoring for SAP (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [Continuous Threat Monitoring for SAP ソリューション](sentinel-solutions-catalog.md#sap)のインストール後にのみ利用可能|
| **Log Analytics テーブル** | 「[Microsoft Sentinel SAP ソリューション ログ リファレンス](sap-solution-log-reference.md)」をご覧ください |
| **ベンダーのドキュメント/<br>インストール手順** | [SAP の継続的な脅威監視のデプロイ](sap-deploy-solution.md) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk Enterprise Password Vault (EPV) Events (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [セキュリティ情報イベント管理 (SIEM) アプリケーション](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) |
| **サポートしているもの** | [CyberArk](https://www.cyberark.com/customer-support/) |
| | |


## <a name="cyberpion-security-logs-preview"></a>Cyberpion セキュリティ ログ (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | CyberpionActionItems_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Cyberpion サブスクリプションを取得する](https://azuremarketplace.microsoft.com/en/marketplace/apps/cyberpion1597832716616.cyberpion)<br>[Cyberpion のセキュリティ アラートを Microsoft Sentinel に統合する](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) |
| **サポートしているもの** | [Cyberpion](https://www.cyberpion.com/) |
| | |



## <a name="dns-preview"></a>DNS (プレビュー)

**「[Windows DNS Server (プレビュー)](#windows-dns-server-preview)」を参照してください**。

## <a name="dynamics-365"></a>Dynamics 365

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)** <br><br> [Microsoft Sentinel 4 Dynamics 365 ソリューション](sentinel-solutions-catalog.md#azure)の一部として利用することもできます|
| **ライセンスの前提条件/<br>コスト情報** | <li>[Microsoft Dynamics 365 の運用ライセンス](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)。 サンドボックス環境では使用できません。<li>アクティビティのログ記録を行うには、Microsoft 365 Enterprise [E3 または E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) サブスクリプションが必要です。<br>その他の料金が適用される場合があります |
| **Log Analytics テーブル** | Dynamics365Activity |
| **サポートしているもの** | Microsoft |
| | |

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md)<br><br>[API ユーザーを作成する](#create-an-api-user) |
| **Log Analytics テーブル** | ESETEnterpriseInspector_CL |
| **API 資格情報** | <li>EEI ユーザー名<li>EEI パスワード<li>ベース URL |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[ESET Enterprise Inspector REST API ドキュメント](https://help.eset.com/eei/1.5/en-US/api.html) |
| **コネクタのデプロイ手順** | Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM) |
| **サポートしているもの** | [ESET](https://support.eset.com/en) |
| | |
### <a name="create-an-api-user"></a>API ユーザーを作成する

1. 管理者アカウントを使用して ESET Security Management Center/ESET PROTECT コンソールにログインし、 **[その他]** タブと **[ユーザー]** サブタブを選択します。
1. **[新規追加]** ボタンを選択して、**ネイティブ ユーザー** を追加します。
1. API アカウントの新しいユーザーを作成します。 **省略可能:** **[すべて]** 以外の **ホーム グループ** を選択して、取り込む検出を制限します。
1. **[アクセス許可セット]** タブで、**Enterprise Inspector レビュー担当者** のアクセス許可セットを割り当てます。
1. 管理者アカウントからサインアウトし、検証のために新しい API 資格情報を使用してコンソールにログインした後、API アカウントからサインアウトします。

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md)<br><br>[収集する ESET SMC ログを構成する](#configure-the-eset-smc-logs-to-be-collected) <br>[ESET SMC データを API 形式で渡すように OMS エージェントを構成する](#configure-oms-agent-to-pass-eset-smc-data-in-api-format)<br>[oms.api.eset タグをキャッチし、構造化されたデータを解析するように OMS エージェントの構成を変更する](#change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data)<br>[自動構成を無効にして、エージェントを再起動する](#disable-automatic-configuration-and-restart-agent)|
| **Log Analytics テーブル** | eset_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [ESET Syslog サーバーのドキュメント](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) |
| **サポートしているもの** | [ESET](https://support.eset.com/en) |
| | |

### <a name="configure-the-eset-smc-logs-to-be-collected"></a>収集する ESET SMC ログを構成する

ESET SMC の IP アドレスからのログを受け入れるように rsyslog を構成します。

```bash
    sudo -i
    # Set ESET SMC source IP address
    export ESETIP={Enter your IP address}

    # Create rsyslog configuration file
    cat > /etc/rsyslog.d/80-remote.conf << EOF
    \$ModLoad imudp
    \$UDPServerRun 514
    \$ModLoad imtcp
    \$InputTCPServerRun 514
    \$AllowedSender TCP, 127.0.0.1, $ESETIP
    \$AllowedSender UDP, 127.0.0.1, $ESETIP user.=alert;user.=crit;user.=debug;user.=emerg;user.=err;user.=info;user.=notice;user.=warning  @127.0.0.1:25224
    EOF

    # Restart rsyslog
    systemctl restart rsyslog
```

### <a name="configure-oms-agent-to-pass-eset-smc-data-in-api-format"></a>ESET SMC データを API 形式で渡すように OMS エージェントを構成する

ESET データを簡単に認識するため、データを別のテーブルにプッシュし、エージェントで解析して、Microsoft Sentinel クエリを簡略化および高速化します。 

**/etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.conf** ファイルで、`match oms.**` セクションの型を `out_oms_api` に変更して、データを API オブジェクトとして送信するようにします。
    
完全な `match oms.**` セクションの例を次のコードに示します。

```bash
    <match oms.** docker.**>
      type out_oms_api
      log_level info
      num_threads 5
      run_in_background false

      omsadmin_conf_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsadmin.conf
      cert_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.crt
      key_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.key

      buffer_chunk_limit 15m
      buffer_type file
      buffer_path /var/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/state/out_oms_common*.buffer

      buffer_queue_limit 10
      buffer_queue_full_action drop_oldest_chunk
      flush_interval 20s
      retry_limit 10
      retry_wait 30s
      max_retry_wait 9m
    </match>
```

### <a name="change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data"></a>oms.api.eset タグをキャッチし、構造化されたデータを解析するように OMS エージェントの構成を変更する

**/etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.d/syslog.conf** ファイルを変更します。 

次に例を示します。

```bash
    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.api.eset
    </source>

    <filter oms.api.**>
      @type parser
      key_name message
      format /(?<message>.*?{.*})/
    </filter>

    <filter oms.api.**>
      @type parser
      key_name message
      format json
    </filter>
```
### <a name="disable-automatic-configuration-and-restart-agent"></a>自動構成を無効にして、エージェントを再起動する

次に例を示します。 

```bash
    # Disable changes to configuration files from Portal
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'

    # Restart agent
    sudo /opt/microsoft/omsagent/bin/service_control restart

    # Check agent logs
    tail -f /var/opt/microsoft/omsagent/log/omsagent.log
```

### <a name="configure-eset-smc-to-send-logs-to-connector"></a>ログをコネクタに送信するように ESET SMC を構成する

BSD スタイルと JSON 形式を使用して ESET ログを構成します。

- Syslog サーバー構成に移動し、[ホスト] (お使いのコネクタ)、[形式] (BSD)、[トランスポート] (TCP) を構成します
- [ロギング] セクションに移動し、JSON を有効にします

詳細については、ESET のドキュメントをご覧ください。

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | ExabeamEvent |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-Exabeam-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [Advanced Analytics システム アクティビティの通知を構成する](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [ExtraHop 検出 SIEM コネクタ](https://aka.ms/asi-syslog-extrahop-forwarding) |
| **サポートしているもの** | [ExtraHop](https://www.extrahop.com/support/) |
| | |

## <a name="f5-big-ip"></a>F5 BIG-IP

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | F5Telemetry_LTM_CL<br>F5Telemetry_system_CL<br>F5Telemetry_ASM_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [F5 BIG-IP を Microsoft Sentinel と統合する](https://aka.ms/F5BigIp-Integrate) |
| **サポートしているもの** | [F5 Networks](https://support.f5.com/csp/home) |
| | |
## <a name="f5-networks-asm"></a>F5 Networks (ASM)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [アプリケーション セキュリティ イベント ログの構成](https://aka.ms/asi-syslog-f5-forwarding) |
| **サポートしているもの** | [F5 Networks](https://support.f5.com/csp/home) |
| | |



## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint クラウド アクセス セキュリティ ブローカー (CASB) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Forcepoint CASB と Microsoft Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/) |
| **サポートしているもの** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Forcepoint Cloud Security Gateway と Microsoft Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/) |
| **サポートしているもの** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | ForcepointDLPEvents_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Forcepoint Data Loss Prevention と Microsoft Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/) |
| **サポートしているもの** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Forcepoint Next-Gen Firewall と Microsoft Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/) |
| **サポートしているもの** | [Forcepoint](https://support.forcepoint.com/) |
| | |



## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) for CEF (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [最初に ForgeRock Common Audit (CAUD) for Microsoft Sentinel をインストールします](https://github.com/javaservlets/SentinelAuditEventHandler) |
| **サポートしているもの** | [ForgeRock](https://www.forgerock.com/support) |
| | |

## <a name="fortinet"></a>Fortinet

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[Fortinet ログをログ フォワーダーに送信する](#send-fortinet-logs-to-the-log-forwarder) <br<br>[Fortinet Fortigate ソリューション](sentinel-solutions-catalog.md#fortinet-fortigate)で利用可能|
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Fortinet ドキュメント ライブラリ](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)<br>お使いのバージョンを選択し、*Handbook* (ハンドブック) および *Log Message Reference* (ログ メッセージ リファレンス) PDF を使用します。 |
| **サポートしているもの** | [Fortinet](https://support.fortinet.com/) |
| | |

### <a name="send-fortinet-logs-to-the-log-forwarder"></a>Fortinet ログをログ フォワーダーに送信する

お使いの Fortinet アプライアンスで CLI を開き、次のコマンドを実行します。

```Console
config log syslogd setting
set status enable
set format cef
set port 514
set server <ip_address_of_Forwarder>
end
```

- サーバーの **IP アドレス** をログ フォワーダーの IP アドレスに置き換えます。
- **Syslog のポート** を **514** に設定するか、フォワーダーの Syslog デーモンに設定されているポートに設定します。
- 初期の FortiOS のバージョンで CEF 形式を有効にするため、コマンド セット **csv disable** を実行する必要が生じる場合があります。



## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md)<br><br>[Google Reports API の追加構成](#extra-configuration-for-the-google-reports-api) |
| **Log Analytics テーブル** | GWorkspace_ReportsAPI_admin_CL<br>GWorkspace_ReportsAPI_calendar_CL<br>GWorkspace_ReportsAPI_drive_CL<br>GWorkspace_ReportsAPI_login_CL<br>GWorkspace_ReportsAPI_mobile_CL<br>GWorkspace_ReportsAPI_token_CL<br>GWorkspace_ReportsAPI_user_accounts_CL<br> |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-GWorkspaceReportsAPI-functionapp |
| **API 資格情報** | <li>GooglePickleString |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[API ドキュメント](https://developers.google.com/admin-sdk/reports/v1/reference/activities)<li>[Google Workspace ドメイン全体の権限委任の実行](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)に関するページで、資格情報を取得します<li>[token.pickle ファイルを pickle 文字列に変換します](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | GWorkspaceActivityReports |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-GWorkspaceReportsAPI-parser |
| **アプリケーションの設定** | <li>GooglePickleString<li>WorkspaceID<li>workspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

### <a name="extra-configuration-for-the-google-reports-api"></a>Google Reports API の追加構成

[Web アプリケーションの資格情報](https://developers.google.com/workspace/guides/create-credentials#web)の作成時に、 **[認可されたリダイレクト URI]** に http://localhost:8081/ を追加します。

1. [手順に従って](https://developers.google.com/admin-sdk/reports/v1/quickstart/python) credentials.json を取得します。
1. Google pickle 文字列を取得するには、(credentials.json と同じパスで) [この python スクリプト](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode)を実行します。
1. 単一引用符で囲まれた pickle 文字列の出力をコピーして保存します。 これは、関数アプリをデプロイするために必要になります。


## <a name="illusive-attack-management-system-ams-preview"></a>Illusive 攻撃管理システム (AMS) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Illusive Networks 管理者ガイド](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version) |
| **サポートしているもの** | [Illusive Networks](https://www.illusivenetworks.com/technical-support/) |
| | |

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[Imperva Cloud WAF ソリューション](sentinel-solutions-catalog.md#imperva)で利用可能|
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Imperva WAF Gateway アラートの Microsoft Sentinel へのログ記録を有効にする手順](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert) |
| **サポートしているもの** | [Imperva](https://www.imperva.com/support/technical-support/) |
| | |


## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md)<br><br> [InfoBlox Threat Defense ソリューション](sentinel-solutions-catalog.md#infoblox)で利用可能 |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | InfobloxNIOS |
| **Kusto 関数 URL:** | https://aka.ms/sentinelgithubparsersinfoblox |
| **ベンダーのドキュメント/<br>インストール手順** | [NIOS SNMP および Syslog デプロイ ガイド](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf) |
| **サポートしているもの** | Microsoft |
| | |




## <a name="juniper-srx-preview"></a>Juniper SRX (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | JuniperSRX |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-junipersrx-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [SRX Branch デバイスのトラフィック ログ (セキュリティ ポリシー ログ) を構成する](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)<br>[システム ログを構成する](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502) |
| **サポートしているもの** | [Juniper Networks](https://support.juniper.net/support/) |
| | |

## <a name="lookout-mobile-threat-defense-preview"></a>Lookout Mobile Threat Defense (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) <br><br>[Lookout Mobile Threat Defense for Microsoft Sentinel](sentinel-solutions-catalog.md#lookout) ソリューションのインストール後にのみ利用可能 |
| **Log Analytics テーブル** | Lookout_CL |
| **API 資格情報** | <li>Lookout アプリケーション キー |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[インストール ガイド](https://esupport.lookout.com/s/article/Lookout-with-Azure-Sentinel) (サインインが必要)<li>[API のドキュメント](https://esupport.lookout.com/s/article/Mobile-Risk-API-Guide) (サインインが必要)<li>[Lookout Mobile Endpoint Security](https://www.lookout.com/products/mobile-endpoint-security) |
| **サポートしているもの** | [Lookout](https://www.lookout.com/support) |
| | |



## <a name="microsoft-365-defender"></a>Microsoft 365 Defender

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Microsoft 365 Defender から Microsoft Sentinel にデータを接続する](connect-microsoft-365-defender.md)** (上位のコネクタの記事) |
| **ライセンスの前提条件/<br>コスト情報** | [Microsoft 365 Defender の有効なライセンス](/microsoft-365/security/mtp/prerequisites)
| **Log Analytics テーブル** | **アラート:**<br>SecurityAlert<br>SecurityIncident<br>**Defender for Endpoint のイベント:**<br>DeviceEvents<br>DeviceFileEvents<br>DeviceImageLoadEvents<br>DeviceInfo<br>DeviceLogonEvents<br>DeviceNetworkEvents<br>DeviceNetworkInfo<br>DeviceProcessEvents<br>DeviceRegistryEvents<br>DeviceFileCertificateInfo<br>**Defender for Office 365 のイベント:**<br>EmailAttachmentInfo<br>EmailUrlInfo<br>EmailEvents<br>EmailPostDeliveryEvents |
| **サポートしているもの** | Microsoft |
| | |

## <a name="microsoft-365-insider-risk-management-irm-preview"></a>Microsoft 365 Insider Risk Management (IRM) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>[Microsoft 365 Insider Risk Management ソリューション](sentinel-solutions-catalog.md#domain-solutions)でも利用可能 |
| **ライセンスと他の前提条件** | <ul><li>Microsoft 365 E5/A5/G5、またはそれに付随するコンプライアンスまたは IRM アドオンの有効なサブスクリプション。<li>[Microsoft 365 Insider Risk Management](/microsoft-365/compliance/insider-risk-management) が完全にオンボードされ、[IRM ポリシー](/microsoft-365/compliance/insider-risk-management-policies)が定義され、アラートが生成されている。<li>Microsoft Sentinel コネクタ経由でアラートを受信するために、Office 365 Management Activity API への IRM アラートのエクスポートを有効にするように[構成された Microsoft 365 IRM](/microsoft-365/compliance/insider-risk-management-settings#export-alerts-preview)。
| **Log Analytics テーブル** | SecurityAlert |
| **データ クエリ フィルター** | `SecurityAlert`<br>`\| where ProductName == "Microsoft 365 Insider Risk Management"` |
| **サポートしているもの** | Microsoft |
| | |

<a name="microsoft-cloud-app-security-mcas"></a>

## <a name="microsoft-defender-for-cloud-apps"></a>Microsoft Defender for Cloud Apps

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Cloud Discovery ログのために、[Microsoft Defender for Cloud Apps で Microsoft Sentinel を SIEM として有効にします](/cloud-app-security/siem-sentinel) |
| **Log Analytics テーブル** | SecurityAlert - アラート用<br>McasShadowItReporting - Cloud Discovery ログ用 |
| **サポートしているもの** | Microsoft |
| | |

## <a name="microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **ライセンスの前提条件/<br>コスト情報** | [Microsoft Defender for Endpoint デプロイの有効なライセンス](/microsoft-365/security/defender-endpoint/production-deployment)
| **Log Analytics テーブル** | SecurityAlert |
| **サポートしているもの** | Microsoft |
| | |

## <a name="microsoft-defender-for-identity"></a>Microsoft Defender for Identity

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics テーブル** | SecurityAlert |
| **サポートしているもの** | Microsoft |
| | |

## <a name="microsoft-defender-for-office-365"></a>Microsoft Defender for Office 365

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **ライセンスの前提条件/<br>コスト情報** | [Office 365 ATP プラン 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) の有効なライセンスを持っている必要があります
| **Log Analytics テーブル** | SecurityAlert |
| **サポートしているもの** | Microsoft |
| | |

## <a name="microsoft-office-365"></a>Microsoft Office 365

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[API ベースの接続](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **ライセンスの前提条件/<br>コスト情報** | Office 365 のデプロイは、Microsoft Sentinel ワークスペースと同じテナントに存在する必要があります。<br>その他の料金が適用される場合があります |
| **Log Analytics テーブル** | OfficeActivity |
| **サポートしているもの** | Microsoft |
| | |



## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto 関数パーサー |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | Morphisec |
| **Kusto 関数 URL** | https://aka.ms/Sentinel-Morphiescutpp-parser |
| **サポートしているもの** | [Morphisec](https://support.morphisec.com/support/home) |
| | |


## <a name="netskope-preview"></a>Netskope (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | Netskope_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-netskope-functioncode |
| **API 資格情報** | <li>Netskope API トークン |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Netskope クラウド セキュリティ プラットフォーム](https://www.netskope.com/platform)<li>[Netskope API のドキュメント](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v1-overview.html)<li>[API トークンを取得する](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v2-overview.html) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto 関数エイリアス** | Netskope |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-netskope-parser |
| **アプリケーションの設定** | <li>apikey<li>workspaceID<li>workspaceKey<li>uri (リージョンによって異なり、次のスキーマに従います: `https://<Tenant Name>.goskope.com`) <li>timeInterval (5 に設定します)<li>logTypes<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md) |
| **Log Analytics テーブル** | NGINX_CL |
| **Kusto 関数エイリアス:** | NGINXHTTPServer |
| **Kusto 関数 URL** | https://aka.ms/Sentinel-NGINXHTTP-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [モジュール ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html) |
| **カスタム ログのサンプル ファイル:** | access.log または error.log |
| **サポートしているもの** | Microsoft |
| | |

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | BSMmacOS_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [NXLog Microsoft Sentinel ユーザー ガイド](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **サポートしているもの** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-dns-logs-preview"></a>NXLog DNS ログ (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | DNS_Logs_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [NXLog Microsoft Sentinel ユーザー ガイド](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **サポートしているもの** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | LinuxAudit_CL |
| **ベンダーのドキュメント/<br>インストール手順** |  [NXLog Microsoft Sentinel ユーザー ガイド](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **サポートしているもの** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="okta-single-sign-on-preview"></a>Okta シングル サインオン (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | Okta_CL |
| **Azure 関数アプリのコード** | https://aka.ms/sentineloktaazurefunctioncodev2 |
| **API 資格情報** | <li>API トークン |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Okta システム ログ API のドキュメント](https://developer.okta.com/docs/reference/api/system-log/)<li>[API トークンを作成する](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)<li>[Okta SSO を Microsoft Sentinel に接続する](#okta-single-sign-on-preview) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPS) |
| **アプリケーションの設定** | <li>apiToken<li>workspaceID<li>workspaceKey<li>uri (次のスキーマに従います: `https://<OktaDomain>/api/v1/logs?since=`。 [ドメインの名前空間を識別します](https://developer.okta.com/docs/reference/api-overview/#url-namespace)。) <li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="onapsis-platform-preview"></a>Onapsis Platform (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto の検索およびエンリッチメント関数<br><br>[CEF ログをログ フォワーダーに送信するように Onapsis を構成する](#configure-onapsis-to-send-cef-logs-to-the-log-forwarder) |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | incident_lookup |
| **Kusto 関数 URL** | https://aka.ms/Sentinel-Onapsis-parser |
| **サポートしているもの** | [Onapsis](https://onapsis.force.com/s/login/) |
| | |

### <a name="configure-onapsis-to-send-cef-logs-to-the-log-forwarder"></a>CEF ログをログ フォワーダーに送信するように Onapsis を構成する

Log Analytics エージェントへのログ転送を設定するには、Onapsis 製品に組み込まれているヘルプをご覧ください。

1. **[Setup]\(セットアップ\) > [Third-party integrations]\(サードパーティ統合\) > [Defend Alarms]\(防御アラーム\)** に移動し、Microsoft Sentinel 用の手順に従います。
1. Onapsis コンソールからエージェントがインストールされているログ フォワーダー マシンに到達できることを確認します。 ログは、TCP を使用してポート 514 に送信する必要があります。

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [One Identity Safeguard for Privileged Sessions 管理ガイド](https://aka.ms/sentinel-cef-oneidentity-forwarding) |
| **サポートしているもの** | [One Identity](https://support.oneidentity.com/) |
| | |



## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md) |
| **Log Analytics テーブル** | OracleWebLogicServer_CL |
| **Kusto 関数エイリアス:** | OracleWebLogicServerEvent |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-OracleWebLogicServer-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [Oracle WebLogic Server のドキュメント](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html) |
| **カスタム ログのサンプル ファイル:** | server.log |
| **サポートしているもの** | Microsoft |
| | |

## <a name="orca-security-preview"></a>Orca Security (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | OrcaAlerts_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Microsoft Sentinel の統合](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration) |
| **サポートしているもの** | [Orca Security](http://support.orca.security/) |
| | |


## <a name="ossec-preview"></a>OSSEC (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto 関数パーサー |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | OSSECEvent |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-OSSEC-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [OSSEC のドキュメント](https://www.ossec.net/docs/)<br>[Syslog を使用したアラートの送信](https://www.ossec.net/docs/docs/manual/output/syslog-output.html) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="palo-alto-networks"></a>Palo Alto Networks

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** <br><br>[Palo Alto PAN-OS and Prisma ソリューション](sentinel-solutions-catalog.md#palo-alto)でも利用可能|
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Common Event Format (CEF) 構成ガイド](https://aka.ms/asi-syslog-paloalto-forwarding)<br>[Syslog の監視を構成する](https://aka.ms/asi-syslog-paloalto-configure) |
| **サポートしているもの** | [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support) |
| | |


## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 のアクティビティ ログ (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | Perimeter81_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Perimeter 81 のドキュメント](https://support.perimeter81.com/docs/360012680780) |
| **サポートしているもの** | [Perimeter 81](https://support.perimeter81.com/) |
| | |



## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) の電子メール セキュリティ (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) <br><br>[Proofpoint POD ソリューション](sentinel-solutions-catalog.md#proofpoint)でも利用可能 |
| **Log Analytics テーブル** | ProofpointPOD_message_CL<br>ProofpointPOD_maillog_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-proofpointpod-functionapp |
| **API 資格情報** | <li>ProofpointClusterID<li>ProofpointToken |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Proofpoint コミュニティにサインインする](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)<li>[Proofpoint API のドキュメントと手順](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | ProofpointPOD |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-proofpointpod-parser |
| **アプリケーションの設定** | <li>ProofpointClusterID<li>ProofpointToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) <br><br>[Proofpoint TAP ソリューション](sentinel-solutions-catalog.md#proofpoint)でも利用可能 |
| **Log Analytics テーブル** | ProofPointTAPClicksPermitted_CL<br>ProofPointTAPClicksBlocked_CL<br>ProofPointTAPMessagesDelivered_CL<br>ProofPointTAPMessagesBlocked_CL |
| **Azure 関数アプリのコード** | https://aka.ms/sentinelproofpointtapazurefunctioncode |
| **API 資格情報** | <li>API ユーザー名<li>API パスワード |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Proofpoint SIEM API のドキュメント](https://help.proofpoint.com/Threat_Insight_Dashboard/API_Documentation/SIEM_API) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPS) |
| **アプリケーションの設定** | <li>apiUsername<li>apiUsername<li>uri (`https://tap-api-v2.proofpoint.com/v2/siem/all?format=json&sinceSeconds=300` に設定します)<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | PulseConnectSecure |
| **Kusto 関数 URL:** | https://aka.ms/sentinelgithubparserspulsesecurevpn |
| **ベンダーのドキュメント/<br>インストール手順** | [Syslog の構成](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="qualys-vm-knowledgebase-kb-preview"></a>Qualys VM のナレッジベース (KB) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md)<br><br>[Qualys VM KB の追加構成](#extra-configuration-for-the-qualys-vm-kb) <br><br>[Qualys VM ソリューション](sentinel-solutions-catalog.md#qualys)でも利用可能|
| **Log Analytics テーブル** | QualysKB_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-qualyskb-functioncode |
| **API 資格情報** | <li>API ユーザー名<li>API パスワード |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Qualys VM API ユーザー ガイド](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto 関数エイリアス** | QualysKB |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-qualyskb-parser |
| **アプリケーションの設定** | <li>apiUsername<li>apiUsername<li>uri (リージョン別。「[API サーバーの一覧](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348)」を参照してください。 次のスキーマに従います: `https://<API Server>/api/2.0`。)<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (URI の末尾に `&` で区切って追加します。 スペースは含めません。)<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm-kb"></a>Qualys VM KB の追加構成

1. 管理者アカウントを使用して Qualys Vulnerability Management コンソールにログインし、 **[ユーザー]** タブと **[ユーザー]** サブタブを選択します。
1. **[新規]** ドロップダウン メニューを選択し、 **[ユーザー]** を選択します。
1. API アカウントのユーザー名とパスワードを作成します。
1. **[ユーザー ロール]** タブで、アカウント ロールが **[マネージャー]** に設定され、**GUI** と **API** へのアクセスが許可されていることを確認します
1. 管理者アカウントからサインアウトし、検証のために新しい API 資格情報を使用してコンソールにサインインした後、API アカウントからサインアウトします。
1. 管理者アカウントを使用してコンソールにログインし直し、API アカウントのユーザー ロールを変更して、**GUI** へのアクセスを削除します。
1. すべての変更を保存します。

## <a name="qualys-vulnerability-management-vm-preview"></a>Qualys Vulnerability Management (VM) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md)<br><br>[Qualys VM の追加構成](#extra-configuration-for-the-qualys-vm) <br>[手動デプロイ - 関数アプリの構成後](#manual-deployment---after-configuring-the-function-app)|
| **Log Analytics テーブル** | QualysHostDetection_CL |
| **Azure 関数アプリのコード** | https://aka.ms/sentinelqualysvmazurefunctioncode |
| **API 資格情報** | <li>API ユーザー名<li>API パスワード |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Qualys VM API ユーザー ガイド](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPS) |
| **アプリケーションの設定** | <li>apiUsername<li>apiUsername<li>uri (リージョン別。「[API サーバーの一覧](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348)」を参照してください。 次のスキーマに従います: `https://<API Server>/api/2.0/fo/asset/host/vm/detection/?action=list&vm_processed_after=`。)<li>WorkspaceID<li>WorkspaceKey<li>filterParameters (URI の末尾に `&` で区切って追加します。 スペースは含めません。)<li>timeInterval (5 に設定します。 変更する場合は、それに応じて関数アプリのタイマー トリガーを変更します。)<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm"></a>Qualys VM の追加構成

1. 管理者アカウントを使用して Qualys Vulnerability Management コンソールにログインし、 **[ユーザー]** タブと **[ユーザー]** サブタブを選択します。
1. **[新規]** ドロップダウン メニューを選択し、 **[ユーザー]** を選択します。
1. API アカウントのユーザー名とパスワードを作成します。
1. **[ユーザー ロール]** タブで、アカウント ロールが **[マネージャー]** に設定され、**GUI** と **API** へのアクセスが許可されていることを確認します
1. 管理者アカウントからサインアウトし、検証のために新しい API 資格情報を使用してコンソールにログインした後、API アカウントからサインアウトします。
1. 管理者アカウントを使用してコンソールにログインし直し、API アカウントのユーザー ロールを変更して、**GUI** へのアクセスを削除します。
1. すべての変更を保存します。

### <a name="manual-deployment---after-configuring-the-function-app"></a>手動デプロイ - 関数アプリの構成後

**host.json ファイルを構成する**

大量の Qualys ホスト検出データが取り込まれる可能性があるため、実行時間が関数アプリの既定のタイムアウトである 5 分を超える可能性があります。 従量課金プランで、既定のタイムアウト時間を最大の 10 分に増やして、関数アプリを実行できる時間を増やします。

1. 関数アプリで、関数アプリ名を選択し、 **[App Service Editor]** ページを選択します。
1. **[移動]** を選択してエディターを開いた後、**wwwroot** ディレクトリの **host.json** ファイルを選択します。
1. 行 `managedDependancy` の前に行 `"functionTimeout": "00:10:00",` を追加します。
1. **[保存済み]** がエディターの右上隅に表示されたことを確認してから、エディターを終了します。

より長いタイムアウト時間が必要な場合は、[App Service プラン](../azure-functions/functions-scale.md)へのアップグレードを検討してください。



## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | SalesforceServiceCloud_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-SalesforceServiceCloud-functionapp |
| **API 資格情報** | <li>Salesforce API ユーザー名<li>Salesforce API パスワード<li>Salesforce セキュリティ トークン<li>Salesforce コンシューマー キー<li>Salesforce コンシューマー シークレット |
| **ベンダーのドキュメント/<br>インストール手順** | [Salesforce REST API 開発者ガイド](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)<br>**[認可の設定]** で、OAuth の代わりに **セッション ID** の方法を使用します。 |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | SalesforceServiceCloud |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-SalesforceServiceCloud-parser |
| **アプリケーションの設定** | <li>SalesforceUser<li>SalesforcePass<li>SalesforceSecurityToken<li>SalesforceConsumerKey<li>SalesforceConsumerSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="security-events-via-legacy-agent-windows"></a>レガシ エージェントを使用したセキュリティ イベント (Windows)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Log Analytics エージェントベースの接続](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Log Analytics テーブル** | SecurityEvents |
| **サポートしているもの** | Microsoft |
| | |


詳細については、[セキュリティで保護されていないプロトコル ブックの設定](./get-visibility.md#use-built-in-workbooks)に関するページを参照してください。

Azure Monitor エージェント (AMA) に基づく [**AMA コネクタを使用した Windows セキュリティ イベント**](#windows-security-events-via-ama)も参照

[**異常な RDP ログイン検出** 用に **セキュリティ イベント/Windows セキュリティ イベント コネクタ** を構成する](#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)。

## <a name="sentinelone-preview"></a>SentinelOne (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) <br><br>[SentinelOne の追加構成](#extra-configuration-for-sentinelone)|
| **Log Analytics テーブル** | SentinelOne_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-SentinelOneAPI-functionapp |
| **API 資格情報** | <li>SentinelOneAPIToken<li>SentinelOneUrl (`https://<SOneInstanceDomain>.sentinelone.net`) |
| **ベンダーのドキュメント/<br>インストール手順** | <li>https://`<SOneInstanceDomain>`.sentinelone.net/api-doc/overview<li>以下の手順を参照してください |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | SentinelOne |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-SentinelOneAPI-parser |
| **アプリケーションの設定** | <li>SentinelOneAPIToken<li>SentinelOneUrl<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

### <a name="extra-configuration-for-sentinelone"></a>SentinelOne の追加構成

手順に従って資格情報を取得します。

1. 管理者ユーザーの資格情報を使用して SentinelOne 管理コンソールにサインインします。
1. 管理コンソールで、 **[設定]** を選択します。
1. **[設定]** ビューで、 **[ユーザー]** を選択します
1. **[新しいユーザー]** を選択します。
1. 新しいコンソール ユーザーの情報を入力します。
1. [ロール] で、 **[管理者]** を選択します。
1. **[保存]** を選択します
1. 新しいユーザーの資格情報を、データ コネクタで使用するために保存します。



## <a name="sonicwall-firewall-preview"></a>SonicWall ファイアウォール (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [[ログ] > [Syslog]](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)<br>ファシリティとして local4、Syslog 形式として ArcSight をそれぞれ選択します。  |
| **サポートしているもの** | [SonicWall](https://www.sonicwall.com/support/) |
| | | 


## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | SophosCloudOptix_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Microsoft Sentinel と統合します](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (最初の手順はスキップします)。<br>[Sophos クエリのサンプル](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html) |
| **サポートしているもの** | [Sophos](https://secure2.sophos.com/en-us/support.aspx) |
| | |




## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | SophosXGFirewall |
| **Kusto 関数 URL:** | https://aka.ms/sentinelgithubparserssophosfirewallxg |
| **ベンダーのドキュメント/<br>インストール手順** | [Syslog サーバーを追加する](https://docs.sophos.com/nsg/sophos-firewall/18.5/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | secRMM_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [secRMM Microsoft Sentinel 管理者ガイド](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) |
| **サポートしているもの** | [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx) |
| | |


## <a name="squid-proxy-preview"></a>Squid Proxy (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md) |
| **Log Analytics テーブル** | SquidProxy_CL |
| **Kusto 関数エイリアス:** | SquidProxy |
| **Kusto 関数 URL** | https://aka.ms/Sentinel-squidproxy-parser |
| **カスタム ログのサンプル ファイル:** | access.log または cache.log |
| **サポートしているもの** | Microsoft |
| | |

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md) |
| **Log Analytics テーブル** | SymantecICDx_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Microsoft Sentinel (Log Analytics) フォワーダーの構成](https://techdocs.broadcom.com/us/en/symantec-security-software/integrated-cyber-defense/integrated-cyber-defense-exchange/1-4-3/Forwarders/configuring-forwarders-v131944722-d2707e17438.html) |
| **サポートしているもの** | [Broadcom Symantec](https://support.broadcom.com/security) |
| | |


## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | SymantecProxySG |
| **Kusto 関数 URL:** | https://aka.ms/sentinelgithubparserssymantecproxysg |
| **ベンダーのドキュメント/<br>インストール手順** | [Syslog サーバーへのアクセス ログの送信](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html) |
| **サポートしているもの** | Microsoft |
| | |


## <a name="symantec-vip-preview"></a>Symantec VIP (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | SymantecVIP |
| **Kusto 関数 URL:** | https://aka.ms/sentinelgithubparserssymantecvip |
| **ベンダーのドキュメント/<br>インストール手順** | [Syslog の構成](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US) |
| **サポートしているもの** | Microsoft |
| | |



## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [セキュリティで保護された Syslog/CEF ログ](https://thy.center/ss/link/syslog) |
| **サポートしているもの** | [Thycotic](https://thycotic.force.com/support/s/) |
| | |


## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto 関数パーサー |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | TrendMicroDeepSecurity |
| **Kusto 関数 URL** | https://aka.ms/TrendMicroDeepSecurityFunction |
| **ベンダーのドキュメント/<br>インストール手順** | [Deep Security イベントを Syslog または SIEM サーバーに転送する](https://aka.ms/Sentinel-trendMicro-connectorInstructions) |
| **サポートしているもの** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)** と Kusto 関数パーサー |
| **Log Analytics テーブル** | CommonSecurityLog |
| **Kusto 関数エイリアス:** | TrendMicroTippingPoint |
| **Kusto 関数 URL** | https://aka.ms/Sentinel-trendmicrotippingpoint-function |
| **ベンダーのドキュメント/<br>インストール手順** | ArcSight CEF Format v4.2 形式で Syslog メッセージを送信します。 |
| **サポートしているもの** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-vision-one-xdr-preview"></a>Trend Micro Vision One (XDR) (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | TrendMicro_XDR_CL |
| **API 資格情報** | <li>API トークン |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Trend Micro Vision One API](https://automation.trendmicro.com/xdr/home)<li>[サード パーティ アクセス用の API キーの取得](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys) |
| **コネクタのデプロイ手順** | Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM) |
| **サポートしているもの** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |



## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | CarbonBlackEvents_CL<br>CarbonBlackAuditLogs_CL<br>CarbonBlackNotifications_CL |
| **Azure 関数アプリのコード** | https://aka.ms/sentinelcarbonblackazurefunctioncode |
| **API 資格情報** | **API アクセス レベル** ("*監査*" および "*イベント*" ログの場合):<li>API ID<li>API キー<br><br>**SIEM アクセス レベル** ("*通知*" イベントの場合):<li>SIEM API ID<li>SIEM API キー |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Carbon Black API のドキュメント](https://developer.carbonblack.com/reference/carbon-black-cloud/cb-defense/latest/rest-api/)<li>[API キーの作成](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPS) |
| **アプリケーションの設定** | <li>apiId<li>apiKey<li>WorkspaceID<li>WorkspaceKey<li>uri (リージョン別。[オプションの一覧を参照してください](https://community.carbonblack.com/t5/Knowledge-Base/PSC-What-URLs-are-used-to-access-the-APIs/ta-p/67346)。 次のスキーマに従います: `https://<API URL>.conferdeploy.net`。)<li>timeInterval (5 に設定します)<li>SIEMapiId ("*通知*" イベントを取り込む場合)<li>SIEMapiKey ("*通知*" イベントを取り込む場合)<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="vmware-esxi-preview"></a>VMware ESXi (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | VMwareESXi |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-vmwareesxi-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [ESXi 3.5 および 4.x での Syslog の有効化](https://kb.vmware.com/s/article/1016621)<br>[ESXi ホストで Syslog を構成する](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics テーブル** | syslog |
| **Kusto 関数エイリアス:** | WatchGuardFirebox |
| **Kusto 関数 URL:** | https://aka.ms/Sentinel-watchguardfirebox-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [Microsoft Sentinel 統合ガイド](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html) |
| **サポートしているもの** | [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview) |
| | |

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | Syslog メッセージを CEF 形式で送信するように NFP ソリューションを構成するには、[WireX サポート](https://wirexsystems.com/contact-us/)にお問い合わせください。 |
| **サポートしているもの** | [WireX Systems](mailto:support@wirexsystems.com) |
| | |


## <a name="windows-dns-server-preview"></a>Windows DNS Server (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Log Analytics エージェントベースの接続](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Log Analytics テーブル** | DnsEvents<br>DnsInventory |
| **サポートしているもの** | Microsoft |
| | |

## <a name="windows-forwarded-events-preview"></a>Windows の転送済みイベント (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Azure Monitor エージェントベースの接続](connect-azure-windows-microsoft-services.md?tabs=AMA#windows-agent-based-connections)**<br><br>[Windows の転送済みイベント コネクタをデプロイするための追加手順](#additional-instructions-for-deploying-the-windows-forwarded-events-connector) |
| **前提条件** | Windows イベント コレクション (WEC) が有効になっていて、実行されている必要があります。<br>WEC マシンに、Azure Monitor エージェントをインストールします。 |
| **xPath クエリ プレフィックス** | "ForwardedEvents!*" |
| **Log Analytics テーブル** | WindowsEvents |
| **サポートしているもの** | Microsoft |
| | |

### <a name="additional-instructions-for-deploying-the-windows-forwarded-events-connector"></a>Windows の転送済みイベント コネクタをデプロイするための追加手順

データ正規化の完全なサポートを保証するために、[Advanced SIEM Information Model (ASIM)](normalization.md) パーサーをインストールすることをお勧めします。 これらのパーサーは、[`Azure-Sentinel`GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASim%20WindowsEvent)から、 **[Deploy to Azure]\(Azure にデプロイする\)** ボタンを使用してデプロイできます。

## <a name="windows-firewall"></a>Windows ファイアウォール

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Log Analytics エージェントベースの接続](connect-azure-windows-microsoft-services.md?tabs=LAA#windows-agent-based-connections)** |
| **Log Analytics テーブル** | WindowsFirewall |
| **サポートしているもの** | Microsoft |
| | |

## <a name="windows-security-events-via-ama"></a>AMA を使用した Windows セキュリティ イベント

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **Azure サービス間の統合: <br>[Azure Monitor エージェントベースの接続](connect-azure-windows-microsoft-services.md?tabs=AMA#windows-agent-based-connections)** |
| **xPath クエリ プレフィックス** | "Security!*" |
| **Log Analytics テーブル** | SecurityEvents |
| **サポートしているもの** | Microsoft |
| | |

[**レガシ エージェント コネクタを使用したセキュリティ イベント**](#security-events-via-legacy-agent-windows)も参照。

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>異常な RDP ログイン検出用にセキュリティ イベント/Windows セキュリティ イベント コネクタを構成する

> [!IMPORTANT]
> 異常な RDP ログイン検出は現在、パブリック プレビュー段階です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Microsoft Sentinel では、セキュリティ イベント データに機械学習 (ML) を適用して、リモート デスクトップ プロトコル (RDP) ログインの異常なアクティビティを識別できます。 シナリオには以下が含まれます。

- **通常とは異なる IP** - その IP アドレスが過去 30 日間にほとんどまたはまったく確認されていない

- **通常とは異なる地理的な場所** - IP アドレス、市区町村、国、および ASN が過去 30 日間にほとんどまたはまったく確認されていない

- **新しいユーザー** - 新しいユーザーが、過去 30 日間のデータに基づいて予期されないか確認されていない (またはその両方の) IP アドレスおよび地理的な場所からログインしている。

**構成の手順**

1. **セキュリティ イベント** または **Windows セキュリティ イベント** データ コネクタを使用して、RDP ログイン データ (イベント ID 4624) を収集している必要があります。 イベント セットを Microsoft Sentinel にストリーミングするには、"なし" 以外の[イベント セット](windows-security-event-id-reference.md)を選択するか、このイベント ID を含めたデータ収集ルールを作成します。

1. Microsoft Sentinel ポータルで、 **[分析]** を選択した後、 **[規則のテンプレート]** タブを選択します。 **(Preview) Anomalous RDP Login Detection\((プレビュー) 異常な RDP ログイン検出\)** 規則を選択し、 **[状態]** スライダーを **[有効]** に移動します。

    > [!NOTE]
    > 機械学習アルゴリズムでは、ユーザー動作のベースライン プロファイルを作成するために 30 日間分のデータが必要であるため、インシデントを検出する前に、30 日間の Windows セキュリティ イベント データの収集を許可する必要があります。

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md)<br><br>[Webhook を構成する](#configure-webhooks) <br>[Webhook 構成にコールバック URL を追加する](#add-callback-url-to-webhook-configuration)|
| **Log Analytics テーブル** | Workplace_Facebook_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-WorkplaceFacebook-functionapp |
| **API 資格情報** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[Webhook を構成する](https://developers.facebook.com/docs/workplace/reference/webhooks)<li>[権限の構成](https://developers.facebook.com/docs/workplace/reference/permissions) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | Workplace_Facebook |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-WorkplaceFacebook-parser |
| **アプリケーションの設定** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

### <a name="configure-webhooks"></a>Webhook を構成する

1. 管理者ユーザーの資格情報を使用して Workplace にサインインします。
1. 管理者用パネルで、 **[統合]** を選択します。
1. **[すべての統合]** ビューで、 **[カスタム統合を作成]** を選択します。
1. 名前と説明を入力して、 **[作成]** を選択します。
1. **[統合の詳細]** パネルで、 **[アプリ シークレット]** を表示してコピーします。
1. **[統合へのアクセス許可]** パネルで、すべての読み取りアクセス許可を設定します。 詳細については、[アクセス許可に関するページ](https://developers.facebook.com/docs/workplace/reference/permissions)をご覧ください。

### <a name="add-callback-url-to-webhook-configuration"></a>Webhook 構成にコールバック URL を追加する

1. 関数アプリのページを開き、 **[関数]** の一覧に移動し、 **[関数の URL の取得]** を選択してコピーします。
1. **Workplace from Facebook** に戻ります。 **[Webhook を設定]** パネルの各タブで、 **[コールバック URL]** を直前の手順でコピーした関数の URL として設定し、 **[トークンを認証]** を自動デプロイ中に受信した、または手動デプロイ中に入力した同じ値として設定します。
1. **[保存]** を選択します。



## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (プレビュー)

Zimperium Mobile Threat Defense データ コネクタでは、Zimperium の脅威ログを Microsoft Sentinel に接続して、ダッシュボードを表示し、カスタム アラートを作成し、調査を向上させることができます。 このコネクタにより、組織のモバイル脅威のランドスケープに関するより詳細な分析情報が提供され、セキュリティ運用機能が向上します。

詳細については、[Zimperium の Azure Sentinel への接続](#zimperium-mobile-thread-defense-preview)に関する記事をご覧ください。

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Microsoft Sentinel データ コレクター API**](connect-rest-api-template.md)<br><br>[Zimperium MTD を構成して接続する](#configure-and-connect-zimperium-mtd) |
| **Log Analytics テーブル** | ZimperiumThreatLog_CL<br>ZimperiumMitigationLog_CL |
| **ベンダーのドキュメント/<br>インストール手順** | [Zimperium カスタマー サポート ポータル](https://support.zimperium.com/) (サインインが必要) |
| **サポートしているもの** | [Zimperium](https://www.zimperium.com/support) |
| | |

### <a name="configure-and-connect-zimperium-mtd"></a>Zimperium MTD を構成して接続する

1. zConsole のナビゲーション バーで、 **[管理]** を選択します。
1. **[統合]** タブを選択します。
1. **[脅威レポート]** ボタン、 **[統合を追加]** ボタンの順に選択します。
1. 統合を作成します。
    1. 使用可能な統合から、**Microsoft Sentinel** を選択します。
    1. [*ワークスペース ID*] と [*主キー*] を入力して、 **[次へ]** を選択します。
    1. Microsoft Sentinel 統合の名前を入力します。
    1. Microsoft Sentinel にプッシュする脅威データの **[Filter Level]\(フィルター レベル\)** を選択します。
    1. **[完了]** を選択します。

## <a name="zoom-reports-preview"></a>Zoom Reports (プレビュー)

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Azure Functions と REST API**](connect-azure-functions-template.md) |
| **Log Analytics テーブル** | Zoom_CL |
| **Azure 関数アプリのコード** | https://aka.ms/Sentinel-ZoomAPI-functionapp |
| **API 資格情報** | <li>ZoomApiKey<li>ZoomApiSecret |
| **ベンダーのドキュメント/<br>インストール手順** | <li>[JWT With Zoom を使用して資格情報を取得する](https://marketplace.zoom.us/docs/guides/auth/jwt) |
| **コネクタのデプロイ手順** | <li>Azure Resource Manager (ARM) テンプレートを使用した[ワンクリック デプロイ](connect-azure-functions-template.md?tabs=ARM)<li>[手動による展開](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 関数エイリアス** | Zoom |
| **Kusto 関数の URL/<br>パーサーの構成手順** | https://aka.ms/Sentinel-ZoomAPI-parser |
| **アプリケーションの設定** | <li>ZoomApiKey<li>ZoomApiSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri (省略可能) |
| **サポートしているもの** | Microsoft |
| | |

## <a name="zscaler"></a>Zscaler

| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | **[Syslog を介した Common Event Format (CEF)](connect-common-event-format.md)** |
| **Log Analytics テーブル** | CommonSecurityLog |
| **ベンダーのドキュメント/<br>インストール手順** | [Zscaler と Microsoft Sentinel のデプロイ ガイド](https://aka.ms/ZscalerCEFInstructions) |
| **サポートしているもの** | [Zscaler](https://help.zscaler.com/submit-ticket-links) |
| | |


## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (プレビュー)


| コネクタ属性 | 説明 |
| --- | --- |
| **データ インジェスト方法** | [**Log Analytics エージェント - カスタム ログ**](connect-custom-logs.md)<br><br>[Zscaler Private Access の追加構成](#extra-configuration-for-zscaler-private-access) |
| **Log Analytics テーブル** | ZPA_CL |
| **Kusto 関数エイリアス:** | ZPAEvent |
| **Kusto 関数 URL** | https://aka.ms/Sentinel-zscalerprivateaccess-parser |
| **ベンダーのドキュメント/<br>インストール手順** | [Zscaler Private Access のドキュメント](https://help.zscaler.com/zpa)<br>以下も参照してください |
| **サポートしているもの** | Microsoft |
| | |

### <a name="extra-configuration-for-zscaler-private-access"></a>Zscaler Private Access の追加構成

次の構成手順に従って、Zscaler Private Access のログを Microsoft Sentinel に取り込みます。 詳細については、[Azure Monitor のドキュメント](../azure-monitor/agents/data-sources-json.md)を参照してください。 Zscaler Private Access のログは、ログ ストリーミング サービス (LSS) を介して配信されます。 詳細については、[LSS のドキュメント](https://help.zscaler.com/zpa/about-log-streaming-service)をご覧ください。

1. [ログ レシーバー](https://help.zscaler.com/zpa/configuring-log-receiver)を構成します。 ログ レシーバーの構成中に、 **[ログ テンプレート]** として **[JSON]** を選択します。
1. 構成ファイル ([zpa.conf](https://aka.ms/sentinel-zscalerprivateaccess-conf)) をダウンロードします。

    ```bash
    wget -v https://aka.ms/sentinel-zscalerprivateaccess-conf -O zpa.conf
    ```

1. Azure Log Analytics エージェントをインストールしたサーバーにサインインします。
1. zpa.conf を /etc/opt/microsoft/omsagent/`workspace_id`/conf/omsagent.d/ フォルダーにコピーします。
1. 次のように zpa.conf を編集します。
    1. Zscaler ログ レシーバーのログの転送先として設定したポートを指定します (4 行目)
    1. `workspace_id` を実際のワークスペース ID の値に置き換えます (14、15、16、19 行目)
1. 次のコマンドを使用して、Linux 用 Log Analytics エージェント サービスを再起動します。

    ```bash
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```
ワークスペース ID の値は、Zscaler Private Access コネクタのページまたは Log Analytics ワークスペースのエージェント管理ページで確認できます。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Microsoft Azure Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)
- [Microsoft Sentinel への脅威インテリジェンスの統合](threat-intelligence-integration.md)
