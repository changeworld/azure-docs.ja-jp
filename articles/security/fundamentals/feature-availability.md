---
title: 米国政府機関のお客様向けの Azure サービス クラウド機能の利用可能性
description: 米国政府機関のお客様向け Azure Sentinel などの Azure セキュリティ サービスの機能の利用可能性を一覧表示します
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 05/23/2021
ms.openlocfilehash: 817e23bfe21aeabde51064cd2606fa447ee6de22
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060014"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>米国政府機関のお客様向けのクラウド機能の利用可能性


この記事では、次のセキュリティ サービスについての、Microsoft Azure および Azure Government クラウドの機能の利用可能性について説明します。

- [Azure Sentinel](#azure-sentinel)

> [!NOTE]
> 近日中に追加のセキュリティ サービスがこの記事に追加される予定です。
> 

## <a name="azure-government"></a>Azure Government

Azure Government では Azure と同じ基盤となるテクノロジ (Azure Commercial または Azure Public と呼ばれることもある) が使用されており、それにはサービスとしてのインフラストラクチャ (IaaS)、サービスとしてのプラットフォーム (PaaS)、サービスとしてのソフトウェア (SaaS) のコア コンポーネントが含まれます。 Azure と Azure Government にはどちらも、包括的なセキュリティ コントロールが用意されており、顧客データの保護に対する Microsoft のコミットメントがあります。

Azure Government は、米国連邦政府、州政府、地方自治体、部族政府、およびそれらのパートナー専用の物理的に分離されたクラウド環境です。 どちらのクラウド環境も、FedRAMP High インパクト レベルで評価および認可されていますが、Azure Government では、米国内の顧客データの保存に関する契約上のコミットメントと、顧客データを処理するシステムへの可能性のあるアクセスを、スクリーニングされた米国ユーザーに制限することによって、顧客に追加の保護レイヤーが提供されます。 このようなコミットメントは、クラウドを使用して、EAR、ITAR、DoE 10 CFR Part 810 などの米国の輸出制御規制の対象となるデータを保存または処理するお客様にとって、関心が高い可能性があります。

Azure Government の詳細については、「[Azure Government とは](../../azure-government/documentation-government-welcome.md)」を参照してください。

## <a name="microsoft-365-integration"></a>Microsoft 365 の統合

製品間の統合は、Azure と Office プラットフォーム間の相互運用性に依存します。 Azure 環境でホストされているオファリングには、Microsoft 365 Enterprise および Microsoft 365 Government プラットフォームからアクセスできます。 Office 365 と Office 365 GCC は、Azure の Azure Active Directory (Azure AD) とペアになります。 Office 365 GCC High および Office 365 DoD は、Azure Government の Azure AD とペアになります。

次の図は、Microsoft クラウドの階層と、それらが相互にどのように関連しているかを示しています。

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Microsoft 365 のクラウド統合。":::

Office 365 GCC 環境は、お客様が FedRAMP High、CJIS、IRS 1075 などの米国政府の要件に準拠するために役立ちます。 Office 365 GCC High および DoD 環境では、DoD IL4/5、DFARS 7012、NIST 800-171、および ITAR に準拠する必要があるお客様をサポートします。

Office 365 US Government 環境の詳細については、以下を参照してください。

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High および DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)


以下のセクションでは、サービスが Microsoft 365 と統合されたタイミング、および Office 365 GCC、Office 365 High、および Office 365 DoD の機能の利用可能性について説明します。

## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel は、スケーラブルでクラウドネイティブ型のセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Azure Sentinel は、高度なセキュリティ分析と脅威インテリジェンスを企業全体で実現し、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションを提供します。

詳細については、[Azure Sentinel 製品のドキュメント](../../sentinel/overview.md)を参照してください。

次の表に、Azure と Azure Government での現在の Azure Sentinel 機能の利用可能性を示しています。


| 機能 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Bring Your Own ML (BYO-ML)](../../sentinel/bring-your-own-ml.md) | パブリック プレビュー | パブリック プレビュー |
| - [クロステナントまたはクロスワークスペース インシデント ビュー](../../sentinel/multiple-workspace-view.md) |パブリック プレビュー | パブリック プレビュー |
| - [エンティティ分析情報](../../sentinel/enable-entity-behavior-analytics.md) | GA | パブリック プレビュー |
| - [Fusion](../../sentinel/fusion.md)<br>高度なマルチステージ攻撃の検出 <sup>[1](#footnote1)</sup> | GA | GA |
| - [ハンティング](../../sentinel/hunting.md) | GA | GA |
|- [ノートブック](../../sentinel/notebooks.md) | GA | GA |
|- [SOC インシデント監査メトリック](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [ウォッチリスト](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | パブリック プレビュー | 利用不可 |
| **脅威インテリジェンス サポート** | | |
| - [脅威インテリジェンス - TAXII データ コネクタ](../../sentinel/import-threat-intelligence.md)  | パブリック プレビュー | 利用不可 |
| - [脅威インテリジェンス プラットフォーム データ コネクタ](../../sentinel/import-threat-intelligence.md)  | パブリック プレビュー | 利用不可 |
| - [脅威インテリジェンスの調査ブレード](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | パブリック プレビュー | 利用不可 |
| - [URL デトネーション](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | 利用不可 |
| - [脅威インテリジェンス ブック](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | 利用不可 |
|**検出のサポート** | | |
| - [異常な Windows ファイル共有アクセスの検出](../../sentinel/fusion.md)  | パブリック プレビュー | 利用不可 |
| - [異常な RDP ログインの検出](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>組み込みの ML 検出 | パブリック プレビュー | 利用不可 |
| - [異常な SSH ログインの検出](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>組み込みの ML 検出 | パブリック プレビュー | 利用不可 |
| **Azure サービスのコネクタ** | | |
| - [Azure アクティビティ ログ](../../sentinel/connect-azure-activity.md)                                  |   GA           |    GA         |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md)                |      GA        |       GA        |
| - [Azure ADIP](../../sentinel/connect-azure-ad-identity-protection.md)                         |  GA            |        GA              |
| - [Azure DDoS Protection](../../sentinel/connect-azure-ddos-protection.md)                |     GA         |       GA               |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md)                  |    GA          |        GA              |
| - [Azure Defender for IoT](../../sentinel/connect-asc-iot.md)           |       GA       |  利用不可           |
| - [Azure Firewall ](../../sentinel/connect-azure-firewall.md)                        |   GA           |        GA              |
| - [Azure Information Protection](../../sentinel/connect-azure-information-protection.md)              |     パブリック プレビュー         |         利用不可             |
| - [Azure Key Vault ](../../sentinel/connect-azure-key-vault.md)                           |       パブリック プレビュー         |         利用不可                       |
| - [Azure Kubernetes Services (AKS)](../../sentinel/connect-azure-kubernetes-service.md)           |       パブリック プレビュー         |         利用不可                |
| - [Azure SQL Databases](../../sentinel/connect-azure-sql-logs.md)                        |     GA         |         GA             |
| - [Azure WAF](../../sentinel/connect-azure-waf.md)                                  |      GA        |      GA                |
| **Windows のコネクタ** | | |
| - [Windows Firewall](../../sentinel/connect-windows-firewall.md)                                 |     GA         |   GA           |
| - [Windows セキュリティ イベント](../../sentinel/connect-windows-security-events.md)                                  |      GA        |         GA     |
| **外部コネクタ**| | |
| - [Agari のフィッシング対策とブランド保護](../../sentinel/connect-agari-phishing-defense.md)       | パブリック プレビュー | パブリック プレビュー |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md)                            | パブリック プレビュー | パブリック プレビュー |
| - [AI Vectra 検出](../../sentinel/connect-ai-vectra-detect.md)                                 | パブリック プレビュー | パブリック プレビュー |
| - [Akamai セキュリティ イベント](../../sentinel/connect-akamai-security-events.md)                           | パブリック プレビュー | パブリック プレビュー |
| - [Alcide kAudit](../../sentinel/connect-alcide-kaudit.md)                                   | パブリック プレビュー | 利用不可      |
| - [Alsid for Active Directory](../../sentinel/connect-alsid-active-directory.md)                      | パブリック プレビュー | 利用不可      |
| - [Apache HTTP Server](../../sentinel/connect-apache-http-server.md)                               | パブリック プレビュー | 利用不可      |
| - [Aruba ClearPass](../../sentinel/connect-aruba-clearpass.md)                                  | パブリック プレビュー | パブリック プレビュー |
| - [AWS](../../sentinel/connect-data-sources.md)                                             | GA             | GA             |
| - [Barracuda CloudGen Firewall](../../sentinel/connect-barracuda-cloudgen-firewall.md)                      | GA             | GA             |
| - [Barracuda Web App Firewall](../../sentinel/connect-barracuda.md)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/connect-better-mtd.md)                 | パブリック プレビュー | 利用不可      |
| - [Beyond Security beSECURE](../../sentinel/connect-besecure.md)                        | パブリック プレビュー | 利用不可      |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md)                        | パブリック プレビュー | パブリック プレビュー |
| - [Broadcom Symantec DLP](../../sentinel/connect-broadcom-symantec-dlp.md)                            | パブリック プレビュー | パブリック プレビュー |
| - [Check Point](../../sentinel/connect-checkpoint.md)                                      | GA             | GA             |
| - [Cisco ASA](../../sentinel/connect-cisco.md)                                        | GA             | GA             |
| - [Cisco Meraki](../../sentinel/connect-cisco-meraki.md)                                     | パブリック プレビュー | パブリック プレビュー |
| - [Cisco Umbrella](../../sentinel/connect-cisco-umbrella.md)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Cisco UCS](../../sentinel/connect-cisco-ucs.md)                                        | パブリック プレビュー | パブリック プレビュー |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md)                          | パブリック プレビュー | パブリック プレビュー |
| - [Citrix Analytics WAF](../../sentinel/connect-citrix-waf.md)                             | GA             | GA             |
| - [Common Event Format (CEF)](../../sentinel/connect-common-event-format.md)                        | GA             | GA             |
| - [CyberArk Enterprise Password Vault (EPV) Events](../../sentinel/connect-cyberark.md) | パブリック プレビュー | パブリック プレビュー |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | パブリック プレビュー | 利用不可      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | パブリック プレビュー | 利用不可      |
| - [ExtraHop Reveal(x)](../../sentinel/connect-extrahop.md)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/connect-f5-big-ip.md)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/connect-f5.md)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | パブリック プレビュー | パブリック プレビュー |
| - [Forepoint CASB](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | パブリック プレビュー | パブリック プレビュー |
| - [Forepoint DLP ](../../sentinel/connect-forcepoint-dlp.md)                                   | パブリック プレビュー | 利用不可      |
| - [ForgeRock Common Audit for CEF](../../sentinel/connect-data-sources.md)                  | パブリック プレビュー | パブリック プレビュー |
| - [Fortinet](../../sentinel/connect-fortinet.md)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](../../sentinel/connect-google-workspace.md)                      | パブリック プレビュー | 利用不可      |
| - [Illusive 攻撃管理システム](../../sentinel/connect-illusive-attack-management-system.md)                | パブリック プレビュー | パブリック プレビュー |
| - [Imperva WAF Gateway](../../sentinel/connect-imperva-waf-gateway.md)                             | パブリック プレビュー | パブリック プレビュー |
| - [Infoblox NIOS](../../sentinel/connect-infoblox.md)                                    | パブリック プレビュー | パブリック プレビュー |
| - [Juniper SRX](../../sentinel/connect-juniper-srx.md)                                      | パブリック プレビュー | パブリック プレビュー |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | パブリック プレビュー | パブリック プレビュー |
| - [NXLog Windows DNS](../../sentinel/connect-nxlog-dns.md)                                             | パブリック プレビュー | 利用不可      |
| - [NXLog LinuxAudit](../../sentinel/connect-nxlog-linuxaudit.md)                                 | パブリック プレビュー | 利用不可      |
| - [Okta シングル サインオン](../../sentinel/connect-okta-single-sign-on.md)                              | パブリック プレビュー | パブリック プレビュー |
| - [Onapsis Platform](../../sentinel/connect-data-sources.md)                                 | パブリック プレビュー | パブリック プレビュー |
| - [One Identity Safeguard](../../sentinel/connect-one-identity.md)                          | GA             | GA             |
| - [Orca Security アラート](../../sentinel/connect-orca-security-alerts.md)                            | パブリック プレビュー | 利用不可      |
| - [Palo Alto Networks](../../sentinel/connect-paloalto.md)                               | GA             | GA             |
| - [Perimeter 81 のアクティビティ ログ](../../sentinel/connect-perimeter-81-logs.md)                      | GA             | 利用不可      |
| - [Proofpoint On Demand Email Security](../../sentinel/connect-proofpoint-pod.md)             | パブリック プレビュー | 利用不可      |
| - [Proofpoint TAP](../../sentinel/connect-proofpoint-tap.md)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Pulse Connect Secure](../../sentinel/connect-proofpoint-tap.md)                             | パブリック プレビュー | パブリック プレビュー |
| - [Qualys Vulnerability Management](../../sentinel/connect-qualys-vm.md)                  | パブリック プレビュー | パブリック プレビュー |
| - [Salesforce Service Cloud](../../sentinel/connect-salesforce-service-cloud.md)                         | パブリック プレビュー | 利用不可      |
| - [SonicWall ファイアウォール](../../sentinel/connect-sophos-cloud-optix.md)                              | パブリック プレビュー | パブリック プレビュー |
| - [Sophos Cloud Optix](../../sentinel/connect-sophos-cloud-optix.md)                               | パブリック プレビュー | 利用不可      |
| - [Sophos XG Firewall](../../sentinel/connect-sophos-xg-firewall.md)                               | パブリック プレビュー | パブリック プレビュー |
| - [Squadra Technologies secRMM](../../sentinel/connect-squadra-secrmm.md)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/connect-squid-proxy.md)                                      | パブリック プレビュー | 利用不可      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/connect-symantec.md)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/connect-symantec-proxy-sg.md)                                | パブリック プレビュー | パブリック プレビュー |
| - [Symantec VIP](../../sentinel/connect-symantec-vip.md)                                     | パブリック プレビュー | パブリック プレビュー |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [脅威インテリジェンス プラットフォーム](../../sentinel/connect-threat-intelligence.md)                   | パブリック プレビュー | 利用不可      |
| - [脅威インテリジェンス TAXII](../../sentinel/connect-threat-intelligence.md)                       | パブリック プレビュー | 利用不可      |
| - [Thycotic Secret Server](../../sentinel/connect-thycotic-secret-server.md)                          | パブリック プレビュー | パブリック プレビュー |
| - [Trend Micro Deep Security](../../sentinel/connect-trend-micro.md)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/connect-trend-micro-tippingpoint.md)                         | パブリック プレビュー | パブリック プレビュー |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | パブリック プレビュー | 利用不可      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/connect-vmware-carbon-black.md)           | パブリック プレビュー | パブリック プレビュー |
| - [VMware ESXi](../../sentinel/connect-vmware-esxi.md)                                      | パブリック プレビュー | パブリック プレビュー |
| - [WireX Network Forensics Platform](../../sentinel/connect-wirex-systems.md)                | パブリック プレビュー | パブリック プレビュー |
| - [Zimperium Mobile Threat Defense](../../sentinel/connect-zimperium-mtd.md)                  | パブリック プレビュー | 利用不可      |
| - [Zscaler](../../sentinel/connect-zscaler.md)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> SSH および RDP 検出は、ソブリン クラウドでは Databricks ML プラットフォームを使用できないため、サポートされていません。

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 データ コネクタ

Office 365 GCC は、Azure の Azure Active Directory (Azure AD) とペアになります。 Office 365 GCC High および Office 365 DoD は、Azure Government の Azure AD とペアになります。

> [!TIP]
> [相互運用性が可能である](#microsoft-365-integration)箇所を理解するために、Azure 環境に注意を払ってください。 次の表では、*可能でない* 相互運用性をダッシュ (-) でマークし、サポートが関係しないことを示しています。
>

| コネクタ | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| **[Dynamics365](../../sentinel/connect-dynamics-365.md)**                               |              |                      |
| - Office 365 GCC |パブリック プレビュー | -|
| - Office 365 GCC High | -|利用不可 |
| - Office 365 DoD |- | 利用不可|
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)**                             |              |                      |
| - Office 365 GCC | パブリック プレビュー| -|
| - Office 365 GCC High |- |利用不可 |
| - Office 365 DoD |- | 利用不可|
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)** <br>シャドウ IT ログ                                  |              |                      |
| - Office 365 GCC | パブリック プレビュー| -|
| - Office 365 GCC High |-|パブリック プレビュー |
| - Office 365 DoD |- |パブリック プレビュー |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)**                  <br>警告                    |              |                      |
| - Office 365 GCC | パブリック プレビュー| -|
| - Office 365 GCC High |-|パブリック プレビュー |
| - Office 365 DoD |- |パブリック プレビュー |
| **[Microsoft Defender for Endpoint](../../sentinel/connect-microsoft-defender-advanced-threat-protection.md)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |利用不可 |
| - Office 365 DoD |- | 利用不可|
| **[Microsoft Defender for Identity](../../sentinel/connect-azure-atp.md)**                                        |              |                      |
| - Office 365 GCC |パブリック プレビュー | -|
| - Office 365 GCC High |- | 利用不可 |
| - Office 365 DoD |- |利用不可 |
| **[Microsoft Defender for Office 365](../../sentinel/connect-office-365-advanced-threat-protection.md)**               |              |                      |
| - Office 365 GCC |パブリック プレビュー |- |
| - Office 365 GCC High |- |利用不可 |
| - Office 365 DoD | -|利用不可 |
| **[Office 365](../../sentinel/connect-office-365.md)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>次のステップ

- [共同責任](shared-responsibility.md)モデル、クラウド プロバイダーが処理するセキュリティ タスク、およびお客様が処理するタスクについて理解します。
- [Azure Government クラウド](../../azure-government/documentation-government-welcome.md)の機能と、連邦、州、地方の各統治機関や関連組織に対して適用されるコンプライアンスをサポートする安定した設計やセキュリティについて理解します。
- [Office 365 Government プラン](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)について理解します。
- 法的および規制基準に対する [Azure のコンプライアンス](../../compliance/index.yml)について理解します。
