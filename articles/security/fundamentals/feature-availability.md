---
title: 米国政府機関のお客様向けの Azure サービス クラウド機能の利用可能性
description: 米国政府機関のお客様向け Azure Sentinel などの Azure セキュリティ サービスの機能の利用可能性を一覧表示します
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 04/29/2021
ms.openlocfilehash: f9ce2ab9df2febc4dcc5d79e5937b97ef498aebe
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2021
ms.locfileid: "109845026"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>米国政府機関のお客様向けのクラウド機能の利用可能性

この記事では、次のセキュリティ サービスについての、Microsoft Azure および Azure Government クラウドの機能の利用可能性について説明します。

- Azure Sentinel

> [!NOTE]
> 近日中に追加のセキュリティ サービスがこの記事に追加される予定です。
> 

## <a name="azure-government"></a>Azure Government

Azure Government では Azure と同じ基盤となるテクノロジ (Azure Commercial または Azure Public と呼ばれることもある) が使用されており、それにはサービスとしてのインフラストラクチャ (IaaS)、サービスとしてのプラットフォーム (PaaS)、サービスとしてのソフトウェア (SaaS) のコア コンポーネントが含まれます。 Azure と Azure Government にはどちらも、包括的なセキュリティ コントロールが用意されており、顧客データの保護に対する Microsoft のコミットメントがあります。

Azure Government は、米国連邦政府、州政府、地方自治体、部族政府、およびそれらのパートナー専用の物理的に分離されたクラウド環境です。 どちらのクラウド環境も、FedRAMP High インパクト レベルで評価および認可されていますが、Azure Government では、米国内の顧客データの保存に関する契約上のコミットメントと、顧客データを処理するシステムへの可能性のあるアクセスを、スクリーニングされた米国ユーザーに制限することによって、顧客に追加の保護レイヤーが提供されます。 このようなコミットメントは、クラウドを使用して、EAR、ITAR、DoE 10 CFR Part 810 などの米国の輸出制御規制の対象となるデータを保存または処理するお客様にとって、関心が高い可能性があります。

Azure Government の詳細については、「[Azure Government とは](/azure/azure-government/documentation-government-welcome)」を参照してください。

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

詳細については、[Azure Sentinel 製品のドキュメント](/azure/sentinel/overview)を参照してください。

次の表に、Azure と Azure Government での現在の Azure Sentinel 機能の利用可能性を示しています。


| 機能 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Bring Your Own ML (BYO-ML)](/azure/sentinel/bring-your-own-ml) | パブリック プレビュー | パブリック プレビュー |
| - [クロステナントまたはクロスワークスペース インシデント ビュー](/azure/sentinel/multiple-workspace-view) |パブリック プレビュー | パブリック プレビュー |
| - [エンティティ分析情報](/azure/sentinel/enable-entity-behavior-analytics) | パブリック プレビュー | 利用不可 |
| - [Fusion](/azure/sentinel/fusion)<br>高度なマルチステージ攻撃の検出 <sup>[1](#footnote1)</sup> | GA | GA |
| - [ハンティング](/azure/sentinel/hunting) | GA | GA |
|- [ノートブック](/azure/sentinel/notebooks) | GA | GA |
|- [SOC インシデント監査メトリック](/azure/sentinel/manage-soc-with-incident-metrics) | GA | GA |
|- [ウォッチリスト](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | パブリック プレビュー | 利用不可 |
| **脅威インテリジェンス サポート** | | |
| - [脅威インテリジェンス - TAXII データ コネクタ](/azure/sentinel/import-threat-intelligence)  | パブリック プレビュー | 利用不可 |
| - [脅威インテリジェンス プラットフォーム データ コネクタ](/azure/sentinel/import-threat-intelligence)  | パブリック プレビュー | 利用不可 |
| - [脅威インテリジェンスの調査ブレード](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | パブリック プレビュー | 利用不可 |
| - [URL デトネーション](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | 利用不可 |
| - [脅威インテリジェンス ブック](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | 利用不可 |
|**検出のサポート** | | |
| - [異常な Windows ファイル共有アクセスの検出](/azure/sentinel/fusion)  | パブリック プレビュー | 利用不可 |
| - [異常な RDP ログインの検出](/azure/sentinel/connect-windows-security-events#configure-the-security-events-connector-for-anomalous-rdp-login-detection)<br>組み込みの ML 検出 | パブリック プレビュー | 利用不可 |
| - [異常な SSH ログインの検出](/azure/sentinel/connect-syslog#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>組み込みの ML 検出 | パブリック プレビュー | 利用不可 |
| **Azure サービスのコネクタ** | | |
| - [Azure アクティビティ ログ](/azure/sentinel/connect-azure-activity)                                  |   GA           |    GA         |
| - [Azure Active Directory](/azure/sentinel/connect-azure-active-directory)                |      GA        |       GA        |
| - [Azure ADIP](/azure/sentinel/connect-azure-ad-identity-protection)                         |  GA            |        GA              |
| - [Azure DDoS Protection](/azure/sentinel/connect-azure-ddos-protection)                |     GA         |       GA               |
| - [Azure Defender](/azure/sentinel/connect-azure-security-center)                  |    GA          |        GA              |
| - [Azure Defender for IoT](/azure/sentinel/connect-asc-iot)           |       GA       |  利用不可           |
| - [Azure Firewall ](/azure/sentinel/connect-azure-firewall)                        |   GA           |        GA              |
| - [Azure Information Protection](/azure/sentinel/connect-azure-information-protection)              |     パブリック プレビュー         |         利用不可             |
| - [Azure Key Vault ](/azure/sentinel/connect-azure-key-vault)                           |       パブリック プレビュー         |         利用不可                       |
| - [Azure Kubernetes Services (AKS)](/azure/sentinel/connect-azure-kubernetes-service)           |       パブリック プレビュー         |         利用不可                |
| - [Azure SQL Databases](/azure/sentinel/connect-azure-sql-logs)                        |     GA         |         GA             |
| - [Azure WAF](/azure/sentinel/connect-azure-waf)                                  |      GA        |      GA                |
| **Windows のコネクタ** | | |
| - [Windows Firewall](/azure/sentinel/connect-windows-firewall)                                 |     GA         |   GA           |
| - [Windows セキュリティ イベント](/azure/sentinel/connect-windows-security-events)                                  |      GA        |         GA     |
| **外部コネクタ**| | |
| - [Agari のフィッシング対策とブランド保護](/azure/sentinel/connect-agari-phishing-defense)       | パブリック プレビュー | パブリック プレビュー |
| - [AI Analyst Darktrace](/azure/sentinel/connect-data-sources)                            | パブリック プレビュー | パブリック プレビュー |
| - [AI Vectra 検出](/azure/sentinel/connect-ai-vectra-detect)                                 | パブリック プレビュー | パブリック プレビュー |
| - [Akamai セキュリティ イベント](/azure/sentinel/connect-akamai-security-events)                           | パブリック プレビュー | パブリック プレビュー |
| - [Alcide kAudit](/azure/sentinel/connect-alcide-kaudit)                                   | パブリック プレビュー | 利用不可      |
| - [Alsid for Active Directory](/azure/sentinel/connect-alsid-active-directory)                      | パブリック プレビュー | 利用不可      |
| - [Apache HHTP Server](/azure/sentinel/connect-apache-http-server)                               | パブリック プレビュー | 利用不可      |
| - [Aruba ClearPass](/azure/sentinel/connect-aruba-clearpass)                                  | パブリック プレビュー | パブリック プレビュー |
| - [AWS](/azure/sentinel/connect-data-sources)                                             | GA             | GA             |
| - [Barracuda CloudGen Firewall](/azure/sentinel/connect-barracuda-cloudgen-firewall)                      | GA             | GA             |
| - [Barracuda Web App Firewall](/azure/sentinel/connect-barracuda)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](/azure/sentinel/connect-better-mtd)                 | パブリック プレビュー | 利用不可      |
| - [Beyond Security beSECURE](/azure/sentinel/connect-besecure)                        | パブリック プレビュー | 利用不可      |
| - [Blackberry CylancePROTECT](/azure/sentinel/connect-data-sources)                        | パブリック プレビュー | パブリック プレビュー |
| - [Broadcom Symantec DLP](/azure/sentinel/connect-broadcom-symantec-dlp)                            | パブリック プレビュー | パブリック プレビュー |
| - [Check Point](/azure/sentinel/connect-checkpoint)                                      | GA             | GA             |
| - [Cisco ASA](/azure/sentinel/connect-cisco)                                        | GA             | GA             |
| - [Cisco Meraki](/azure/sentinel/connect-cisco-meraki)                                     | パブリック プレビュー | パブリック プレビュー |
| - [Cisco Umbrella](/azure/sentinel/connect-cisco-umbrella)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Cisco UCS](/azure/sentinel/connect-cisco-ucs)                                        | パブリック プレビュー | パブリック プレビュー |
| - [Cisco Firepower EStreamer](/azure/sentinel/connect-data-sources)                          | パブリック プレビュー | パブリック プレビュー |
| - [Citrix Analytics WAF](/azure/sentinel/connect-citrix-waf)                             | GA             | GA             |
| - [Common Event Format (CEF)](/azure/sentinel/connect-common-event-format)                        | GA             | GA             |
| - [CyberArk Enterprise Password Vault (EPV) Events](/azure/sentinel/connect-cyberark) | パブリック プレビュー | パブリック プレビュー |
| - [ESET Enterprise Inspector](/azure/sentinel/connect-data-sources)                       | パブリック プレビュー | 利用不可      |
| - [Eset Security Management Center](/azure/sentinel/connect-data-sources)                  | パブリック プレビュー | 利用不可      |
| - [ExtraHop Reveal(x)](/azure/sentinel/connect-extrahop)                               | GA             | GA             |
| - [F5 BIG-IP ](/azure/sentinel/connect-f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](/azure/sentinel/connect-f5)                                     | GA             | GA             |
| - [Forcepoint NGFW](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | パブリック プレビュー | パブリック プレビュー |
| - [Forepoint CASB](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | パブリック プレビュー | パブリック プレビュー |
| - [Forepoint DLP ](/azure/sentinel/connect-forcepoint-dlp)                                   | パブリック プレビュー | 利用不可      |
| - [ForgeRock Common Audit for CEF](/azure/sentinel/connect-data-sources)                  | パブリック プレビュー | パブリック プレビュー |
| - [Fortinet](/azure/sentinel/connect-fortinet)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](/azure/sentinel/connect-google-workspace)                      | パブリック プレビュー | 利用不可      |
| - [Illusive 攻撃管理システム](/azure/sentinel/connect-illusive-attack-management-system)                | パブリック プレビュー | パブリック プレビュー |
| - [Imperva WAF Gateway](/azure/sentinel/connect-imperva-waf-gateway)                             | パブリック プレビュー | パブリック プレビュー |
| - [Infoblox NIOS](/azure/sentinel/connect-infoblox)                                    | パブリック プレビュー | パブリック プレビュー |
| - [Juniper SRX](/azure/sentinel/connect-juniper-srx)                                      | パブリック プレビュー | パブリック プレビュー |
| - [Morphisec UTPP](/azure/sentinel/connect-data-sources)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Netskope](/azure/sentinel/connect-data-sources)                                         | パブリック プレビュー | パブリック プレビュー |
| - [NXLog Windows DNS](/azure/sentinel/connect-nxlog-dns)                                             | パブリック プレビュー | 利用不可      |
| - [NXLog LinuxAudit](/azure/sentinel/connect-nxlog-linuxaudit)                                 | パブリック プレビュー | 利用不可      |
| - [Okta シングル サインオン](/azure/sentinel/connect-okta-single-sign-on)                              | パブリック プレビュー | パブリック プレビュー |
| - [Onapsis Platform](/azure/sentinel/connect-data-sources)                                 | パブリック プレビュー | パブリック プレビュー |
| - [One Identity Safeguard](/azure/sentinel/connect-one-identity)                          | GA             | GA             |
| - [Orca Security アラート](/azure/sentinel/connect-orca-security-alerts)                            | パブリック プレビュー | 利用不可      |
| - [Palo Alto Networks](/azure/sentinel/connect-paloalto)                               | GA             | GA             |
| - [Perimeter 81 のアクティビティ ログ](/azure/sentinel/connect-perimeter-81-logs)                      | GA             | 利用不可      |
| - [Proofpoint On Demand Email Security](/azure/sentinel/connect-proofpoint-pod)             | パブリック プレビュー | 利用不可      |
| - [Proofpoint TAP](/azure/sentinel/connect-proofpoint-tap)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Pulse Connect Secure](/azure/sentinel/connect-proofpoint-tap)                             | パブリック プレビュー | パブリック プレビュー |
| - [Qualys Vulnerability Management](/azure/sentinel/connect-qualys-vm)                  | パブリック プレビュー | パブリック プレビュー |
| - [Salesforce Service Cloud](/azure/sentinel/connect-salesforce-service-cloud)                         | パブリック プレビュー | 利用不可      |
| - [SonicWall ファイアウォール](/azure/sentinel/connect-sophos-cloud-optix)                              | パブリック プレビュー | パブリック プレビュー |
| - [Sophos Cloud Optix](/azure/sentinel/connect-sophos-cloud-optix)                               | パブリック プレビュー | 利用不可      |
| - [Sophos XG Firewall](/azure/sentinel/connect-sophos-xg-firewall)                               | パブリック プレビュー | パブリック プレビュー |
| - [Squadra Technologies secRMM](/azure/sentinel/connect-squadra-secrmm)               | GA             | GA             |
| - [Squid Proxy](/azure/sentinel/connect-squid-proxy)                                      | パブリック プレビュー | 利用不可      |
| - [Symantec Integrated Cyber Defense Exchange](/azure/sentinel/connect-symantec)       | GA             | GA             |
| - [Symantec ProxySG](/azure/sentinel/connect-symantec-proxy-sg)                                | パブリック プレビュー | パブリック プレビュー |
| - [Symantec VIP](/azure/sentinel/connect-symantec-vip)                                     | パブリック プレビュー | パブリック プレビュー |
| - [Syslog](/azure/sentinel/connect-syslog)                                           | GA             | GA             |
| - [脅威インテリジェンス プラットフォーム](/azure/sentinel/connect-threat-intelligence)                   | パブリック プレビュー | 利用不可      |
| - [脅威インテリジェンス TAXII](/azure/sentinel/connect-threat-intelligence)                       | パブリック プレビュー | 利用不可      |
| - [Thycotic Secret Server](/azure/sentinel/connect-thycotic-secret-server)                          | パブリック プレビュー | パブリック プレビュー |
| - [Trend Micro Deep Security](/azure/sentinel/connect-trend-micro)                       | GA             | GA             |
| - [Trend Micro TippingPoint](/azure/sentinel/connect-trend-micro-tippingpoint)                         | パブリック プレビュー | パブリック プレビュー |
| - [Trend Micro XDR](/azure/sentinel/connect-data-sources)                                  | パブリック プレビュー | 利用不可      |
| - [VMware Carbon Black Endpoint Standard](/azure/sentinel/connect-vmware-carbon-black)           | パブリック プレビュー | パブリック プレビュー |
| - [VMware ESXi](/azure/sentinel/connect-vmware-esxi)                                      | パブリック プレビュー | パブリック プレビュー |
| - [WireX Network Forensics Platform](/azure/sentinel/connect-wirex-systems)                | パブリック プレビュー | パブリック プレビュー |
| - [Zimperium Mobile Threat Defense](/azure/sentinel/connect-zimperium-mtd)                  | パブリック プレビュー | 利用不可      |
| - [Zscaler](/azure/sentinel/connect-zscaler)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> SSH および RDP 検出は、ソブリン クラウドでは Databricks ML プラットフォームを使用できないため、サポートされていません。

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 データ コネクタ

Office 365 GCC は、Azure の Azure Active Directory (Azure AD) とペアになります。 Office 365 GCC High および Office 365 DoD は、Azure Government の Azure AD とペアになります。

> [!TIP]
> [相互運用性が可能である](#microsoft-365-integration)箇所を理解するために、Azure 環境に注意を払ってください。 次の表では、*可能でない* 相互運用性をダッシュ (-) でマークし、サポートが関係しないことを示しています。
>

| コネクタ | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| **[Dynamics365](/azure/sentinel/connect-dynamics-365)**                               |              |                      |
| - Office 365 GCC |パブリック プレビュー | -|
| - Office 365 GCC High | -|利用不可 |
| - Office 365 DoD |- | 利用不可|
| **[Microsoft 365 Defender](/azure/sentinel/connect-microsoft-365-defender)**                             |              |                      |
| - Office 365 GCC | パブリック プレビュー| -|
| - Office 365 GCC High |- |利用不可 |
| - Office 365 DoD |- | 利用不可|
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)** <br>シャドウ IT ログ                                  |              |                      |
| - Office 365 GCC | パブリック プレビュー| -|
| - Office 365 GCC High |-|パブリック プレビュー |
| - Office 365 DoD |- |パブリック プレビュー |
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)**                  <br>警告                    |              |                      |
| - Office 365 GCC | パブリック プレビュー| -|
| - Office 365 GCC High |-|パブリック プレビュー |
| - Office 365 DoD |- |パブリック プレビュー |
| **[Microsoft Defender for Endpoint](/azure/sentinel/connect-microsoft-defender-advanced-threat-protection)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |利用不可 |
| - Office 365 DoD |- | 利用不可|
| **[Microsoft Defender for Identity](/azure/sentinel/connect-azure-atp)**                                        |              |                      |
| - Office 365 GCC |パブリック プレビュー | -|
| - Office 365 GCC High |- | 利用不可 |
| - Office 365 DoD |- |利用不可 |
| **[Microsoft Defender for Office 365](/azure/sentinel/connect-office-365-advanced-threat-protection)**               |              |                      |
| - Office 365 GCC |パブリック プレビュー |- |
| - Office 365 GCC High |- |利用不可 |
| - Office 365 DoD | -|利用不可 |
| **[Office 365](/azure/sentinel/connect-office-365)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>次のステップ

- [共同責任](shared-responsibility.md)モデル、クラウド プロバイダーが処理するセキュリティ タスク、およびお客様が処理するタスクについて理解します。
- [Azure Government クラウド](/azure/azure-government/documentation-government-welcome)の機能と、連邦、州、地方の各統治機関や関連組織に対して適用されるコンプライアンスをサポートする安定した設計やセキュリティについて理解します。
- [Office 365 Government プラン](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)について理解します。
- 法的および規制基準に対する [Azure のコンプライアンス](/azure/compliance/)について理解します。
