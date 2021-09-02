---
title: Azure Sentinel のパートナー データ コネクタ | Microsoft Docs
description: Azure Sentinel のすべてのパートナー データ コネクタについて説明します。
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 25b09b496ff505bb37452b80355cae705af8936d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723519"
---
# <a name="azure-sentinel-partner-data-connectors"></a>Azure Sentinel パートナー データ コネクタ

この記事では、Azure Sentinel によってサポートされる、Microsoft 以外のパートナー組織のデータ コネクタをアルファベット順に示します。 探しているコネクタがわかっている場合は、このページでその名前までスクロールするかその名前を検索するか、または記事の右側または上部にあるリンクを使用してください。

- 一覧表示されたコネクタを有効にして構成するには、「[データ ソースの接続](connect-data-sources.md)」と、個々の一覧のリンクを参照してください。
- Microsoft 製のサービス間データ コネクタの詳細については、「[サービス間の統合](connect-data-sources.md#service-to-service-integration)」を参照してください。
- Azure Sentinel サポート モデルと **サポート元** フィールドの詳細については、「[データ コネクタのサポート](connect-data-sources.md#data-connector-support)」を参照してください。

> [!IMPORTANT]
> 次の Azure Sentinel パートナー データ コネクタの多くは、現在 **プレビュー** の段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari のフィッシング対策とブランド保護 (プレビュー)

Agari のフィッシング対策およびブランド保護コネクタでは、ブランド保護とフィッシング対策ソリューションのログを Azure Sentinel に接続します。

詳細については、[Agari の開発者サイト](https://developers.agari.com/agari-platform)と、[Azure Sentinel への Agari フィッシング対策およびブランド保護の接続](connect-agari-phishing-defense.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support)

## <a name="ai-analyst-aia-by-darktrace-preview"></a>Darktrace による AI Analyst (AIA) (プレビュー)

Darktrace コネクタでは、モデル侵害と AIA インシデントを Azure Sentinel に送信します。 Darktrace AIA ブックの視覚化を使用して対話形式でデータを探索します。これには、時間ブラシが付いた概要グラフが含まれています。 特定の侵害とインシデントへの詳細なドリルダウンを表示し、Darktrace UI でインシデントを表示してさらに探索することができます。

AIA では、Azure Log Analytics の Azure Sentinel CommonSecurityLog テーブルに侵害データを送信します。 CommonSecurityLog テーブルでは、さまざまなパートナー コネクタ間で簡単なクエリ照合順序と集計をサポートしています。

**データ インジェスト方法:** Syslog を介した [Common Event Format (CEF)](connect-common-event-format.md)。

**サポート元:** [Darktrace](https://customerportal.darktrace.com/)

## <a name="ai-vectra-detect-preview"></a>Al Vectra 検出 (プレビュー)

AI Vectra 検出データ コネクタでは、AI Vectra 検出データを Azure Sentinel に取り込みます。 AI Vectra ブックを使用すると、Sentinel から直接ネットワーク攻撃の調査を開始できます。 重要なホスト、アカウント、キャンペーン、検出を表示し、Vectra のシステム正常性と監査ログを監視することができます。

Azure Sentinel への接続の詳細については、「[Azure Sentinel に AI Vectra 検出を接続する](connect-ai-vectra-detect.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Vectra](https://www.vectra.ai/support)

## <a name="akamai-security-events-preview"></a>Akamai セキュリティ イベント (プレビュー)

Akamai セキュリティ イベント データ コネクタでは、[Akamai セキュリティ イベント](https://www.akamai.com/us/en/products/security/)を Azure Sentinel に取り込みます。 詳細については、[Splunk および CEF Syslog の Akamai SIEM 統合](https://developer.akamai.com/tools/integrations/siem)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Akamai セキュリティ イベントの接続](connect-akamai-security-events.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** [Akamai](https://www.akamai.com/us/en/support/)

## <a name="alcide-kaudit-preview"></a>Alcide kAudit (プレビュー)

Alcide kAudit コネクタでは、Kubernetes クラスター監査ログを Azure Sentinel にリアルタイムで自動的にエクスポートします。 KAudit コネクタによって、Kubernetes 監査ログの可視性と監視が向上します。 Alcide kAudit では、フォレンジクスのために堅牢なセキュリティと監視機能を提供します。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Alcide kAudit の接続](connect-alcide-kaudit.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** [Alcide](https://www.alcide.io/company/contact-us/)

## <a name="alsid-for-active-directory-preview"></a>Alsid for Active Directory (プレビュー)

Alsid for Active Directory コネクタでは、露出の Alsid インジケーター、軌跡フロー、攻撃ログのインジケーターをリアルタイムで Azure Sentinel にエクスポートします。 コネクタには、ログの操作に役立つデータ パーサーも用意されています。 ブックでは、Active Directory の監視とデータの可視化を利用できます。 分析テンプレートは、イベント、露出、攻撃に対する応答を自動化するのに役立ちます。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Alsid for Active Directory の接続](connect-alsid-active-directory.md)に関するページを参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** [Alsid](https://www.alsid.com/contact-us/)

## <a name="amazon-web-services"></a>アマゾン ウェブ サービス

AWS データ コネクタでは、AWS CloudTrail 管理イベントを Azure Sentinel にインポートします。 このコネクタを有効にするには、「[Azure Sentinel を AWS CloudTrail に接続する](connect-aws.md)」を参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** Microsoft

## <a name="apache-http-server-preview"></a>Apache HTTP Server (プレビュー)

Apache HTTP Server データ コネクタでは、Apache HTTP Server イベントを Azure Sentinel に取り込みます。 詳細については、[Apache ログのドキュメント](https://httpd.apache.org/docs/2.4/logs.html)を参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="apache-tomcat-preview"></a>Apache Tomcat (プレビュー)

Apache Tomcat データ コネクタでは、[Apache Tomcat](http://tomcat.apache.org/) イベントを Azure Sentinel に取り込みます。 詳細については、[Apache Tomcat のドキュメント](http://tomcat.apache.org/tomcat-10.0-doc/logging.html)を参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (プレビュー)

Aruba ClearPass コネクタでは、Aruba ClearPass の監査、セッション、システム、分析情報の各ログを Azure Sentinel に接続します。 Syslog を転送する Aruba ClearPass ソリューションの構成の詳細については、[Syslog エクスポート フィルターの追加](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Aruba ClearPass の接続](connect-aruba-clearpass.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence 監査 (プレビュー)

[Atlassian Confluence](https://www.atlassian.com/software/confluence) 監査データ コネクタでは、Confluence 監査レコードを取り込みます。 コネクタでは、潜在的なセキュリティ リスクを調査したり、チームのコラボレーションを分析したり、構成の問題を診断したりするためのイベントを取得できます。 詳細については、[監査ログの表示](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira 監査 (プレビュー)

Atlassian Jira 監査データ コネクタでは、Jira 監査レコード イベントを Azure Sentinel に取り込みます。 コネクタでは、潜在的なセキュリティ リスクを調査したり、チームのコラボレーションを分析したり、構成の問題を診断したりするためのイベントを取得できます。 詳細については、[監査レコード](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="barracuda-cloudgen-firewall-cgfw"></a>Barracuda CloudGen Firewall (CGFW)

Barracuda CGFW コネクタでは、Barracuda CGFW ログを Azure Sentinel に接続します。 Barracuda CGFW の Syslog ストリーミングを構成するには、[Syslog ストリーミングを構成する方法](https://aka.ms/sentinel-barracudacloudfirewall-connector)に関するページを参照してください。

Azure Sentinel への接続の詳細については、「[Barracuda CloudGen Firewall を Azure Sentinel に接続する](connect-barracuda-cloudgen-firewall.md)」を参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** [Barracuda](https://www.barracuda.com/support)

## <a name="barracuda-web-application-firewall-waf"></a>Barracuda Web Application Firewall (WAF)

Barracuda WAF コネクタでは、Barracuda WAF ログを Azure Sentinel に接続します。 詳細については、[Barracuda Web Application Firewall の構成](https://aka.ms/asi-barracuda-connector)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Barracuda WAF の接続](connect-barracuda.md)に関するページを参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。

**サポート元:** [Barracuda](https://www.barracuda.com/support)

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (プレビュー)

BETTER MTD コネクタを使用すると、企業は BETTER MTD インスタンスを Azure Sentinel に接続できます。 このコネクタでは、組織のモバイル デバイスと現在のモバイル セキュリティの状態に関する分析情報を提供し、全体的な SecOps 機能を改善します。 詳細については、BETTER Mobile の [Azure Sentinel のセットアップ ドキュメント](https://mtd-docs.bmobi.net/integrations/how-to-setup-azure-sentinel-integration#mtd-integration-configuration)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への BETTER Mobile Threat Defense の接続](connect-better-mtd.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** BETTER Mobile

## <a name="beyond-security-besecure-preview"></a>Beyond Security beSECURE (プレビュー)

Beyond Security beSECURE コネクタでは、Beyond Security beSECURE のスキャン イベント、スキャン結果、監査証跡を Azure Sentinel に接続します。 詳細については、beSECURE の[概要](https://beyondsecurity.com/solutions/besecure.html)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Beyond Security beSECURE の接続](connect-besecure.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:**  [Beyond Security](https://beyondsecurity.freshdesk.com/support/home)

## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT (プレビュー)

Blackberry CylancePROTECT コネクタでは、CylancePROTECT の監査、脅威、アプリケーション制御、デバイス、メモリ保護、脅威の分類のログを Azure Sentinel に接続します。 Syslog を転送するよう CylancePROTECT を構成するには、[Cylance Syslog のガイド](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf)を参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec データ損失防止 (DLP) (プレビュー)

Broadcom Symantec DLP コネクタでは、Symantec DLP ポリシー/応答ルール トリガーを Azure Sentinel に接続します。 このコネクタを使用すると、調査を支援するカスタム ダッシュボードとアラートを作成できます。 Syslog を転送するように Symantec DLP を構成するには、[Syslog Server アクションへのログの構成](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Broadcom Symantec DLP の接続](connect-broadcom-symantec-dlp.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="check-point"></a>Check Point

Check Point ファイアウォール コネクタでは、Check Point のログを Azure Sentinel に接続します。 ログをエクスポートするように Check Point 製品を構成するには、[ログ エクスポーター - Check Point ログのエクスポート](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323)に関するページを参照してください。

Azure Sentinel への接続の詳細については、「[Check Point を Azure Sentinel に接続する](connect-checkpoint.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Check Point](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.checkpoint.com%2Fsupport-services%2Fcontact-support%2F&data=04%7C01%7CNayef.Yassin%40microsoft.com%7C9965f53402ed44988a6c08d913fc51df%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637562796697084967%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=Av1vTkeYoEXzRKO%2FotZLtMMexIQI%2FIKjJGnPQsbhqmE%3D&reserved=0)

## <a name="cisco-asa"></a>Cisco ASA

Cisco ASA ファイアウォール コネクタでは、Cisco ASA ログを Azure Sentinel に接続します。 接続をセットアップするには、『[Cisco ASA シリーズ CLI 構成ガイド](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html)』の手順に従います。

Azure Sentinel への接続の詳細については、「[Cisco ASA を Azure Sentinel に接続する](connect-cisco.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** Microsoft

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (プレビュー)

Cisco Firepower eStreamer コネクタでは、eStreamer ログを Azure Sentinel に接続します。 Cisco Firepower eStreamer は、Cisco Firepower NGFW ソリューション向けに設計されたクライアント/サーバー API です。 詳細については、[EStreamer eNcore For Sentinel の運用ガイド](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html)を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Cisco](https://www.cisco.com/c/en/us/support/index.html)

## <a name="cisco-meraki-preview"></a>Cisco Meraki (プレビュー)

Cisco Meraki コネクタでは、Cisco Meraki (MX/MR/MS) のイベント ログ、URL、フロー、IDS_Alerts、セキュリティ イベント、Airmarshal イベントを Azure Sentinel に接続します。 Syslog を転送するように Meraki デバイスを構成するには、[Meraki デバイスのレポート - Syslog、SNMP、API](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) に関するページの手順に従います。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Cisco Meraki の接続](connect-cisco-meraki.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (プレビュー)

Cisco UCS コネクタでは、Cisco UCS 監査、イベント、障害ログを Azure Sentinel に接続します。 Syslog を転送するように Cisco UCS を構成するには、[リモート Syslog サーバーに対して Syslog を構成する手順](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog)に関するページの手順に従います。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Cisco Unified Computing System (UCS) の接続](connect-cisco-ucs.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (プレビュー)

Cisco Umbrella データ コネクタでは、DNS、プロキシ、IP ログなど、Amazon S3 に格納されている Cisco Umbrella イベントを Azure Sentinel に取り込みます。 Cisco Umbrella データ コネクタでは、Amazon S3 REST API を使用します。 ログを設定し、資格情報を取得するには、「[Amazon S3 へのログ記録](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3)」を参照してください。

Azure Sentinel への接続の詳細については、「[Azure Sentinel に Cisco Umbrella を接続する](connect-cisco-umbrella.md)」を参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="citrix-analytics-security"></a>Citrix Analytics (セキュリティ)

Citrix Analytics (セキュリティ) データ コネクタでは、Citrix Analytics (セキュリティ) から Azure Sentinel に危険なイベント データをエクスポートします。 カスタム ダッシュボードを作成したり、Citrix Analytics (セキュリティ) データと一緒に他のソースからのデータを分析したり、カスタムの Logic Apps ワークフローを作成してセキュリティ イベントを監視および軽減したりできます。 詳細については、[Microsoft Azure Sentinel の統合](https://docs.citrix.com/en-us/security-analytics/getting-started-security/azure-sentinel-integration.html)に関する記事を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Connect Citrix Analytics (セキュリティ) の接続](connect-citrix-analytics.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Citrix](https://www.citrix.com/support/)

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (プレビュー)

Citrix WAF データ コネクタでは、Citrix WAF ログを Azure Sentinel に接続します。 Citrix WAF では、Web サイト、アプリ、API など、パブリックに公開されている資産に対する脅威を軽減します。
- WAF を構成するには、[サポート WIKI - WAF の構成と NetScaler](https://support.citrix.com/article/CTX234174) に関するページを参照してください。
- CEF ログを構成するには、[アプリケーション ファイアウォールでの CEF のログ記録のサポート](https://support.citrix.com/article/CTX136146)に関するページを参照してください。
- ログをプロキシに転送するには、[監査ログ用に Citrix ADC アプライアンスを構成](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Citrix WAF の接続](connect-citrix-waf.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Citrix](https://www.citrix.com/support/)

## <a name="cognni-preview"></a>Cognni (プレビュー)

Cognni データ コネクタでは、Azure Sentinel への迅速で簡単な統合を実現します。 Cognni を使用して、以前に分類されていない重要な情報を自律的にマップし、関連するインシデントを検出できます。 Cognni を使用すると、重要な情報に対するリスクを認識し、インシデントの重大度を把握し、修復する必要がある詳細を調査して、十分に迅速に変化をもたらすことができます。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Cognni](https://cognni.ai/contact-support/)

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk Enterprise Password Vault (EPV) Events (プレビュー)

CyberArk データ コネクタでは、コンテナーに対して実行されるアクションに関する CyberArk EPV XML Syslog メッセージを取り込みます。 EPV では、*Sentinel.xsl* 変換プログラムを使用して XML メッセージを CEF 標準形式に変換し、選択した Syslog ステージング サーバー (syslog-ng、rsyslog) に送信します。 Syslog ステージング サーバー上の Log Analytics エージェントによって、Log Analytics にメッセージがインポートされます。 詳細については、CyberArk のドキュメントの[セキュリティ情報イベント管理 (SIEM) アプリケーション](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm)に関する記事を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への CyberArk Enterprise Password Vault の接続](connect-cyberark.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [CyberArk](https://www.cyberark.com/customer-support/)

## <a name="cyberpion-security-logs-preview"></a>Cyberpion セキュリティ ログ (プレビュー)

Cyberpion セキュリティ ログ データ コネクタでは、Cyberpion システムから Azure Sentinel に直接ログを取り込みます。 詳細については、Cyberpion のドキュメントで [Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** Cyberpion

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (プレビュー)

ESET Enterprise Inspector データ コネクタでは、ESET Enterprise Inspector バージョン 1.4 以降で提供されている REST API を使用して、ESET Enterprise Inspector からの検出を取り込みます。 詳細については、ESET Enterprise Inspector のドキュメントで [REST API](https://help.eset.com/eei/1.5/en-US/api.html) に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** [ESET](https://support.eset.com/en)

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (プレビュー)

ESET SMC データ コネクタでは、ESET SMC の脅威イベント、監査ログ、ファイアウォール イベント、Web サイト フィルターを Azure Sentinel に取り込みます。 詳細については、ESET SMC のドキュメントで [Syslog サーバー](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html)に関するページを参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。

**サポート元:** [ESET](https://support.eset.com/en)

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (プレビュー)

Exabeam Advanced Analytics データ コネクタでは、システム正常性、注目すべきセッション/異常、高度な分析、ジョブの状態などの Exabeam Advanced Analytics イベントを Azure Sentinel に取り込みます。 Syslog を使用して Exabeam Advanced Analytics からログを送信するには、[Advanced Analytics システム アクティビティ通知の構成](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08)に関するページの手順に従います。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

ExtraHop Reveal(x) データ コネクタでは、Reveal(x) システムを Azure Sentinel に接続します。 Azure Sentinel の統合には、ExtraHop Detection SIEM コネクタが必要です。 Reveal(x) システムに SIEM コネクタをインストールするには、[ExtraHop Detection SIEM コネクタ](https://aka.ms/asi-syslog-extrahop-forwarding)に関するページの手順に従います。

Azure Sentinel への接続の詳細については、「[Azure Sentinel に ExtraHop Reveal(x) を接続する](connect-extrahop.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [ExtraHop](https://www.extrahop.com/support/)

## <a name="f5-big-ip"></a>F5 BIG-IP 

F5 BIG IP コネクタでは、F5 LTM、システム、ASM ログを Azure Sentinel に接続します。 詳細については、[F5 BIGIP と Azure Sentinel の統合](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への F5 BIG-IP の接続](connect-f5-big-ip.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** Microsoft

## <a name="f5-networks"></a>F5 Networks

F5 ファイアウォール コネクタでは、F5 アプリケーション セキュリティ イベントを Azure Sentinel に接続します。 リモート ログを設定するには、[アプリケーション セキュリティ イベント ログの構成](https://aka.ms/asi-syslog-f5-forwarding)に関するページを参照してください。

Azure Sentinel への接続の詳細については、「[F5 ASM を Azure Sentinel に接続する](connect-f5.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** Microsoft

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint クラウド アクセス セキュリティ ブローカー (CASB) (プレビュー)

Forcepoint CASB データ コネクタでは、CASB のログとイベントを Azure Sentinel にリアルタイムで自動的にエクスポートします。 詳細については、[Forcepoint CASB と Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/) に関するページを参照してください。

Azure Sentinel への接続の詳細については、「[Forcepoint 製品を Azure Sentinel に接続する](connect-forcepoint-casb-ngfw.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (プレビュー)

Forcepoint CSG データ コネクタでは、CSG ログを Azure Sentinel に自動的にエクスポートします。 CSG は、ユーザーとデータの場所にかかわらず、可視性、制御、脅威に対する保護を提供する、集約型クラウド セキュリティ サービスです。 詳細については、[Forcepoint Cloud Security Gateway と Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/) に関するページを参照してください。

Azure Sentinel への接続の詳細については、「[Forcepoint 製品を Azure Sentinel に接続する](connect-forcepoint-casb-ngfw.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (プレビュー)

Forcepoint DLP データ コネクタでは、Forcepoint DLP から Azure Sentinel に DLP インシデント データをリアルタイムで自動的にエクスポートします。 詳細については、[Forcepoint Data Loss Prevention と Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/) に関するページを参照してください。

Azure Sentinel への接続の詳細については、「[Forcepoint DLP を Azure Sentinel に接続する](connect-forcepoint-dlp.md)」を参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (プレビュー)

Forcepoint NGFW データ コネクタでは、ユーザー定義の Forcepoint NGFW ログをリアルタイムで Azure Sentinel に自動的にエクスポートします。 詳細については、[Forcepoint Next-Gen Firewall と Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/) に関するページを参照してください。

Azure Sentinel への接続の詳細については、「[Forcepoint 製品を Azure Sentinel に接続する](connect-forcepoint-casb-ngfw.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Forcepoint](https://support.forcepoint.com/)

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) for CEF (プレビュー)

ForgeRock Identity Platform では、ログ データを総合的を追跡できるように、1 つの共通の監査フレームワークを提供します。 CAUD のイベント ハンドラーと一意の ID を使用して、プラットフォーム全体でログ データを抽出して集計することができます。 CAUD for CEF コネクタはオープンで拡張可能であり、Azure Sentinel との統合に監査ログとレポート機能を使用することができます。 詳細については、[ForgeRock Common Audit (CAUD) For Azure Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler) に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [ForgeRock](https://www.forgerock.com/support)

## <a name="fortinet"></a>Fortinet 

Fortinet ファイアウォール コネクタでは、Fortinet ログを Azure Sentinel に接続します。 詳細については、[Fortinet のドキュメント ライブラリ](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)にアクセスし、お使いのバージョンを選択して、"*Handbook*" と "*Log Message Reference*" の PDF を使用してください。

Azure Sentinel への接続の詳細については、「[Azure Sentinel に Fortinet を接続する](connect-fortinet.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Fortinet](https://support.fortinet.com/)

## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (プレビュー)

Google Workspace データ コネクタでは、REST API を使用して Google Workspace アクティビティ イベントを Azure Sentinel に取り込みます。 イベントを取り込む機能によって、次のことができます。
- 潜在的なセキュリティ リスクを調べます。
- チームのコラボレーションを分析します。
- 構成の問題を診断します。
- だれがいつサインインするかを追跡します。
- 管理者アクティビティを分析します。
- ユーザーがコンテンツを作成および共有する方法を理解します。
- 組織のイベントを確認します。

資格情報を取得するには、[Google Workspace ドメイン全体の権限の委任の実行](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)に関するページの手順に従います。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Google Workspace (以前の G Suite) の接続](connect-google-workspace.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive 攻撃管理システム (AMS) (プレビュー)

Illusive AMS コネクタでは、Illusive の攻撃対象領域の分析データとインシデント ログを Azure Sentinel と共有します。 この情報は専用ダッシュボードで表示できます。 ASM ブックでは、組織の攻撃対象領域リスクに関する分析情報を提供し、ADS ブックでは、組織のネットワーク内での認可されていない横移動を追跡します。 詳細については、[Illusive Networks の管理者ガイド](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Illusive Networks AMS の接続](connect-illusive-attack-management-system.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Illusive Networks](https://www.illusivenetworks.com/technical-support/)

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (プレビュー)

Imperva コネクタでは、Imperva WAF Gateway のアラートを Azure Sentinel に接続します。 詳細については、[Azure Sentinel への Imperva WAF Gateway のアラートのログ記録を有効にする手順](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Imperva WAF Gateway の接続](connect-imperva-waf-gateway.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Imperva](https://www.imperva.com/support/technical-support/)

## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (プレビュー)

Infoblox NIOS コネクタでは、Infoblox NIOS ログを Azure Sentinel に接続します。 Infoblox NIOS ログの Syslog 転送を有効にするには、[NIOS SNMP および Syslog のデプロイ ガイド](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Infoblox NIOS の接続](connect-infoblox.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="juniper-srx-preview"></a>Juniper SRX (プレビュー)

Juniper SRX コネクタでは、Juniper SRX ログを Azure Sentinel に接続します。 トラフィック ログを転送するには、[SRX Branch デバイスのトラフィック ログ (セキュリティ ポリシー ログ) の構成](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)に関するページを参照してください。 システム ログを転送するには、[システム ログの構成](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Juniper SRX の接続](connect-juniper-srx.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** [Juniper Networks](https://support.juniper.net/support/)

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (プレビュー)

Morphisec Data Connector for Azure Sentinel では、セキュリティ製品からの重要な分析情報を統合します。 検索と関連付け、脅威インテリジェンス、カスタマイズされたアラートを使用して、分析機能を拡張できます。 Morphisec データ コネクタでは、高度なファイルレス攻撃、インメモリ攻撃、ゼロデイなど、複雑な脅威を可視化できます。 単一のクロス製品ビューを使用すると、データを基にしたリアルタイムの意思決定を行って、最も重要な資産を保護できます。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Morphisec

## <a name="netskope-preview"></a>Netskope (プレビュー)

Netskope Cloud Security Platform コネクタでは、Netskope ログとイベントを Azure Sentinel に取り込みます。 詳細については、[Netskope Cloud Security Platform](https://www.netskope.com/platform) に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (プレビュー)

NGINX HTTP Server データ コネクタでは、NGINX HTTP Server イベントを Azure Sentinel に取り込みます。 詳細については、[モジュール ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html) に関するページを参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS (プレビュー)

NXLog BSM macOS データ コネクタでは、Sun BSM 監査 API を使用して、macOS プラットフォーム上のカーネルから監査イベントを直接キャプチャします。 このデータ コネクタによって、macOS 監査イベントをリアルタイムで効率的に Azure Sentinel にエクスポートできます。 詳しくは、[NXLog Azure Sentinel のユーザー ガイド](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)を参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-dns-logs-preview"></a>NXLog DNS ログ (プレビュー)

NXLog DNS ログ データ コネクタでは、Event Tracing for Windows (ETW) を使用して、監査と分析の両方の DNS サーバー イベントを収集します。 効率を最大限に高めるために、NXLog im_etw モジュールでは、イベント トレースを *.etl* ファイルにキャプチャすることなく、イベント トレース データを直接読み取ります。 この REST API コネクタでは、DNS サーバー イベントをリアルタイムで Azure Sentinel に転送できます。 詳しくは、[NXLog Azure Sentinel のユーザー ガイド](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への NXLog (Windows) DNS ログの接続](connect-nxlog-dns.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (プレビュー)

NXLog LinuxAudit データ コネクタでは、カスタム監査規則をサポートし、AuditD やその他のユーザー ソフトウェアを使用せずにログを収集します。 コネクタでは、IP アドレスとグループ/ユーザー ID はそれぞれの名前に解決されるため、Linux 監査ログはよりわかりやすくなります。 この REST API コネクタでは、Linux セキュリティ イベントをリアルタイムで Azure Sentinel にエクスポートできます。 詳しくは、[NXLog Azure Sentinel のユーザー ガイド](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への NXLog LinuxAudit の接続](connect-nxlog-linuxaudit.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [NXLog](https://nxlog.co/community-forum)

## <a name="okta-single-sign-on-preview"></a>Okta シングル サインオン (プレビュー)

Okta シングル サインオン (SSO) データ コネクタでは、監査およびイベント ログを Okta API から Azure Sentinel に取り込みます。 API トークンを作成するには、[トークンの作成](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)に関するページの手順に従います。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Okta SSO の接続](connect-okta-single-sign-on.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="onapsis-platform-preview"></a>Onapsis Platform (プレビュー)

Onapsis データ コネクタでは、Onapsis Platform でトリガーされたアラームをリアルタイムで Azure Sentinel にエクスポートします。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Onapsis](https://onapsis.force.com/s/login/)

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (プレビュー)

One Identity Safeguard CEF Sentinel データ コネクタでは、標準の CEF コネクタを Safeguard for Privileged Sessions 固有のダッシュボードで拡張します。 このコネクタでは、視覚化、アラート、調査などのデバイス イベントを使用します。 詳細については、[One Identity Safeguard for Privileged Sessions の管理ガイド](https://aka.ms/sentinel-cef-oneidentity-forwarding)を参照してください。

Azure Sentinel への接続の詳細については、「[One Identity Safeguard を Azure Sentinel に接続する](connect-one-identity.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [One Identity](https://support.oneidentity.com/)

## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (プレビュー)

OracleWebLogicServer データ コネクタでは、OracleWebLogicServer イベントを Azure Sentinel に取り込みます。 詳細については、[Oracle WebLogic Server のドキュメント](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html)を参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="orca-security-alert-preview"></a>Orca Security アラート (プレビュー)

Orca Security アラート コネクタでは、アラート ログを自動的に Azure Sentinel にエクスポートします。 詳細については、[Azure Sentinel の統合](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration)に関する記事をご覧ください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Orca Security の接続](connect-orca-security-alerts.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Orca Security](http://support.orca.security/)

## <a name="ossec-preview"></a>OSSEC (プレビュー)

OSSEC データ コネクタでは、OSSEC イベントを Azure Sentinel に取り込みます。 詳細については、[OSSEC のドキュメント](https://www.ossec.net/docs/)を参照してください。 Syslog 経由でアラートを送信する OSSEC を構成するには、[Syslog 経由でアラートを送信](https://www.ossec.net/docs/docs/manual/output/syslog-output.html)に関するページの手順に従います。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** Microsoft

## <a name="palo-alto-networks"></a>Palo Alto Networks

Palo Alto Networks ファイアウォール データ コネクタでは、Palo Alto Networks ログを Azure Sentinel に接続します。 詳細については、[Common Event Format (CEF) の構成ガイド](https://aka.ms/asi-syslog-paloalto-forwarding)と [Syslog の監視の構成](https://aka.ms/asi-syslog-paloalto-configure)に関するページを参照してください。

Palo Alto Networks データ コネクタでは、Syslog を使用して Azure Sentinel [CEF](connect-common-event-format.md) にログを取り込みます。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Palo Alto Networks の接続](connect-paloalto.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support)

## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 のアクティビティ ログ (プレビュー)

Perimeter 81 のアクティビティ ログ データ コネクタでは、Perimeter 81 アクティビティ ログを Azure Sentinel に接続します。 詳細については、Perimeter 81 の [Azure Sentinel](https://support.perimeter81.com/docs/360012680780) のドキュメントを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Perimeter 81 ログの接続](connect-perimeter-81-logs.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Perimeter 81](https://support.perimeter81.com/)

## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) の電子メール セキュリティ (プレビュー)

POD の電子メール セキュリティ データ コネクタでは、POD 電子メール保護データを取得します。 このコネクタを使用すると、メッセージの追跡可能性を確認し、攻撃者や悪意のある内部関係者による電子メールのアクティビティ、脅威、データの侵入を監視できます。 組織のイベントを迅速に確認し、最近のアクティビティのイベント ログを時間単位で取得できます。 POD ログ API を有効にして確認する方法については、[Proofpoint Community にサインイン](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)し、「[Proofpoint-on-Demand-Pod-Log-API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API)」を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Proofpoint On Demand (POD) 電子メール セキュリティの接続](connect-proofpoint-pod.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (プレビュー)

Proofpoint TAP コネクタでは、Proofpoint TAP ログとイベントを Azure Sentinel に取り込みます。 コネクタによって、Message と Click のイベントが Azure Sentinel で可視化されます。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Proofpoint TAP の接続](connect-proofpoint-tap.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (プレビュー)

Pulse Connect Secure コネクタでは、Pulse Connect Secure ログを Azure Sentinel に接続します。 Pulse Connect Secure ログの Syslog ストリーミングを有効にするには、「[Syslog の構成](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm)」の手順に従います。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Pulse Connect Secure の接続](connect-pulse-connect-secure.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="qualys-vulnerability-management-vm-knowledgebase-kb-preview"></a>Qualys Vulnerability Management (VM) ナレッジ ベース (KB) (プレビュー)

Qualys VM KB データ コネクタでは、最新の脆弱性データを Qualys KB から Azure Sentinel に取り込みます。 このデータを使用して、Qualys VM データ コネクタによる脆弱性検出を関連付け、強化することができます。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="qualys-vm-preview"></a>Qualys VM (プレビュー)

Qualys VM データ コネクタでは、Qualys API を使用して脆弱性ホスト検出データを Azure Sentinel に取り込みます。 コネクタによって、脆弱性スキャンのホスト検出データが可視化されます。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Qualys VM の接続](connect-qualys-vm.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (プレビュー)

Salesforce Service Cloud データ コネクタでは、REST API を使用して Salesforce の操作イベントに関する情報を Azure Sentinel に取り込みます。 このコネクタを使用して、組織のイベントを迅速に確認し、最近のアクティビティのイベント ログを時間単位で取得できます。 詳細と資格情報については、Salesforce [REST API の開発者ガイド](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Salesforce Service Cloud の接続](connect-salesforce-service-cloud.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="sentinelone-preview"></a>SentinelOne (プレビュー)

SentinelOne データ コネクタでは、SentinelOne イベントを Azure Sentinel に取り込みます。 イベントには、脅威、エージェント、アプリケーション、アクティビティ、ポリシー、グループなどのサーバー オブジェクトが含まれます。 コネクタでは、潜在的なセキュリティ リスクを調査したり、チームのコラボレーションを分析したり、構成の問題を診断したりするためのイベントを取得できます。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="sonicwall-firewall-preview"></a>SonicWall ファイアウォール (プレビュー)

SonicWall ファイアウォール データ コネクタでは、ファイアウォール ログを Azure Sentinel に接続します。 詳細については、[[ログ] > [Syslog]](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm) に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [SonicWall](https://www.sonicwall.com/support/)

## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (プレビュー)

Sophos Cloud Optix データ コネクタでは、Sophos Cloud Optix ログを Azure Sentinel に接続します。 詳細については、Cloud Optix の設定で、Azure Sentinel の[統合に関するページ](https://optix.sophos.com/#/integrations/sentinel)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Sophos Cloud Optix の接続](connect-sophos-cloud-optix.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Sophos](https://secure2.sophos.com/en-us/support.aspx)

## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall (プレビュー)

Sophos XG Firewall では、Sophos XG Firewall ログを Azure Sentinel に接続します。 詳細については、[Syslog サーバーの追加](https://docs.sophos.com/nsg/sophos-firewall/18.0/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Sophos XG の接続](connect-sophos-xg-firewall.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

Squadra Technologies secRMM データ コネクタでは、USB リムーバブル ストレージのセキュリティ イベント データを Azure Sentinel にプッシュします。 詳細については、[secRMM Azure Sentinel の管理者ガイド](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Squadra Technologies secRMM の接続](connect-squadra-secrmm.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx)

## <a name="squid-proxy-preview"></a>Squid Proxy (プレビュー)

[Squid Proxy](http://www.squid-cache.org/) データ コネクタでは、Squid Proxy のログを Azure Sentinel に接続します。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Squid Proxy の接続](connect-squid-proxy.md)に関するページを参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

Symantec ICDx データ コネクタでは、Symantec セキュリティ ソリューションのログを Azure Sentinel に接続します。 詳細については、「[Symantec ICDx アプライアンスを接続する](connect-symantec.md)」を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Symantec ICDx の接続](connect-symantec.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Broadcom Symantec](https://support.broadcom.com/security)

## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (プレビュー)

Symantec ProxySG データ コネクタでは、Symantec ProxySG ログを Azure Sentinel に接続します。 詳細については、[Syslog サーバーへのアクセス ログの送信](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html)に関するページを参照してください。

Symantec の ProxySG データ コネクタでは、[Syslog](connect-syslog.md) を使用して Azure Sentinel にログを取り込みます。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Symantec Proxy SG の接続](connect-symantec-proxy-sg.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="symantec-vip-preview"></a>Symantec VIP (プレビュー)

Symantec VIP データ コネクタでは、Symantec VIP ログを Azure Sentinel に接続します。 詳細については、「[Syslog の構成](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US)」を参照してください。

Symantec の VIP データ コネクタでは、[Syslog](connect-syslog.md) 経由で Azure Sentinel にログを取り込みます。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Symantec VIP の接続](connect-symantec-vip.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (プレビュー)

Thycotic Secret Server データ コネクタでは、Secret Server のログを Azure Sentinel に接続します。 詳細については、[安全な Syslog/CEF ログ](https://docs.thycotic.com/ss/10.9.0/events-and-alerts/secure-syslog-cef)に関するページを参照してください。

Thycotic データ コネクタでは、Syslog を使用して Azure Sentinel [CEF](connect-common-event-format.md) にログを取り込みます。

Azure Sentinel への接続の詳細については、「[Thycotic Secret Server を Azure Sentinel に接続する](connect-thycotic-secret-server.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Thycotic](https://thycotic.force.com/support/s/)

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

Trend Micro Deep Security データ コネクタでは、Deep Security のログを Azure Sentinel に接続します。 詳細については、[Syslog または SIEM サーバーへの Deep Security イベントの転送](https://help.deepsecurity.trendmicro.com/12_0/on-premise/siem-syslog-forwarding-secure.html?redirected=true&Highlight=Configure%20Syslog#Protection_modules_DSM)に関するページを参照してください。

Trend Micro Deep Security データ コネクタでは、Syslog を使用して Azure Sentinel [CEF](connect-common-event-format.md) にログを取り込みます。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

Azure Sentinel への接続の詳細については、「[Trend Micro Deep Security を Azure Sentinel に接続する](connect-trend-micro.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (プレビュー)

Trend Micro TippingPoint データ コネクタでは、TippingPoint SMS IPS イベントを Azure Sentinel に接続します。

Azure Sentinel への接続の詳細については、「[Trend Micro TippingPoint を Azure Sentinel に接続する](connect-trend-micro-tippingpoint.md)」を参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-xdr-preview"></a>Trend Micro XDR (プレビュー)

Trend Micro XDR データ コネクタでは、ワークベンチ アラートを Trend Micro XDR API から Azure Sentinel に取り込みます。 アカウントと API 認証トークンを作成するには、[サードパーティによるアクセスのために API キーを取得](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys)に関するページの手順に従います。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (プレビュー)

VMware Carbon Black Endpoint Standard データ コネクタでは、Carbon Black Endpoint Standard データを Azure Sentinel に取り込みます。 API トークンを作成するには、[API トークンの作成](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key)に関するページの手順に従います。

Azure Sentinel への接続の詳細については、[Azure Sentinel への VMware Carbon Black Cloud Endpoint Standard の接続](connect-vmware-carbon-black.md)に関するページを参照してください。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元:** Microsoft

## <a name="vmware-esxi-preview"></a>VMware ESXi (プレビュー)

VMware ESXi データ コネクタでは、VMware ESXi ログを Azure Sentinel に接続します。 Syslog を転送するように VMware ESXi コネクタを構成するには、[ESXi 3.5 および 4.x での Syslog の有効化](https://kb.vmware.com/s/article/1016621)および [ESXi ホストでの Syslog の構成](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)に関するページを参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への VMware ESXi の接続](connect-vmware-esxi.md)に関するページを参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (プレビュー)

WatchGuard Firebox データ コネクタでは、ファイアウォール ログを Azure Sentinel に取り込みます。 詳細については、[Microsoft Azure Sentinel の統合ガイド](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html)を参照してください。

**データ インジェスト方法:** [Syslog](connect-syslog.md)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview)

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (プレビュー)

WireX Systems データ コネクタを使用すると、セキュリティの専門家は Azure Sentinel と統合して、フォレンジック調査を強化することができます。 コネクタでは、WireX によって提供されるコンテキスト コンテンツを含んでいるだけでなく、他のソースのデータを分析することもできます。 カスタム ダッシュボードとワークフローを作成して、フォレンジック調査中に最も詳細な画像を提供できます。 詳細と構成のサポートについては、[WireX サポート](https://wirexsystems.com/contact-us/)にお問い合わせください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への WireX Network Forensics Platform の接続](connect-wirex-systems.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** WireX

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (プレビュー)

Workplace データ コネクタでは、Webhook を使用して一般的な Workplace イベントを Azure Sentinel に取り込みます。 Webhook を使用すると、カスタム統合アプリでは、Workplace 内のイベントを購読し、リアルタイムで更新を受け取ることができます。 変更が発生すると、Workplace によって、イベント情報を含む HTTPS POST 要求がコールバック データ コネクタの URL に送信されます。 詳細については、Webhook のドキュメントを参照してください。 コネクタでは、潜在的なセキュリティ リスクを調査したり、チームのコラボレーションを分析したり、構成の問題を診断したりするためのイベントを取得できます。

**データ インジェスト方法:** [Azure Functions および REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

**サポート元**: Microsoft

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (プレビュー)

Zimperium Mobile Threat Defense データ コネクタでは、Zimperium の脅威ログを Azure Sentinel に接続して、ダッシュボードを表示し、カスタム アラートを作成し、調査を向上させることができます。 このコネクタにより、組織のモバイル脅威のランドスケープに関するより詳細な分析情報が提供され、セキュリティ運用機能が向上します。 詳細については、[Zimperium カスタマー サポート ポータル](https://support.zimperium.com/)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Zimperium の接続](connect-zimperium-mtd.md)に関するページを参照してください。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** [Zimperium](https://www.zimperium.com/support)

## <a name="zoom-reports-preview"></a>Zoom Reports (プレビュー)

Zoom Reports データ コネクタでは、Zoom Reports イベントを Azure Sentinel に取り込みます。 コネクタでは、潜在的なセキュリティ リスクを調査したり、チームのコラボレーションを分析したり、構成の問題を診断したりするためのイベントを取得できます。 資格情報を取得するには、[JWT および Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt) に関するページの手順に従います。

**データ インジェスト方法:** [REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

**サポート元:** Microsoft

## <a name="zscaler"></a>Zscaler 

Zscaler データ コネクタでは、Zscaler Internet Access (ZIA) ログを Azure Sentinel に接続します。 Azure Sentinel で Zscaler を使用すると、組織のインターネット使用状況に関する分析情報が得られ、セキュリティ運用機能が向上します。 詳細については、[Zscaler および Microsoft Azure Sentinel のデプロイ ガイド](https://aka.ms/ZscalerCEFInstructions)を参照してください。

Azure Sentinel への接続の詳細については、[Azure Sentinel への Zscaler の接続](connect-zscaler.md)に関するページを参照してください。

**データ インジェスト方法:** Syslog を介した [CEF](connect-common-event-format.md)。

**サポート元:** [Zscaler](https://help.zscaler.com/submit-ticket-links)

## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (プレビュー)

ZPA データ コネクタでは、Zscaler Private Access イベントを Azure Sentinel に取り込みます。 詳細については、[Zscaler Private Access のドキュメント](https://help.zscaler.com/zpa)を参照してください。

**データ インジェスト方法**: [Log Analytics エージェントのカスタム ログ](connect-data-sources.md#custom-logs)。 コネクタでは、Kusto 関数に基づくログ パーサーも使用されます。

**サポート元:** Microsoft
