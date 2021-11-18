---
title: 米国政府機関のお客様向けの Azure サービス クラウド機能の利用可能性
description: 米国政府機関のお客様向け Microsoft Sentinel などの Azure セキュリティ サービスの機能の利用可能性を一覧表示します
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 651ec7ec4e0fe661330807a856f29eb025087609
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718152"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>米国政府機関のお客様向けのクラウド機能の利用可能性

この記事では、次のセキュリティ サービスについての、Microsoft Azure および Azure Government クラウドの機能の利用可能性について説明します。

- [Azure Information Protection](#azure-information-protection)
- [Microsoft Defender for Cloud](#microsoft-defender-for-cloud)
- [Microsoft Sentinel](#microsoft-sentinel)
- [Microsoft Defender for IoT](#microsoft-defender-for-iot)
- [Azure Attestation](#azure-attestation)

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

## <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection (AIP) はクラウドベースのソリューションです。これにより、組織はコンテンツにラベルを適用してドキュメントや電子メールの検出、分類、および保護を行うことができます。

AIP は、Microsoft Information Protection (MIP) ソリューションの一部です。これにより、Microsoft 365 で提供される[ラベル付け](/microsoft-365/compliance/sensitivity-labels)および[分類](/microsoft-365/compliance/data-classification-overview)の機能が拡張されます。

詳細については、[Azure Information Protection 製品ドキュメント](/azure/information-protection/)に関するページをご覧ください。

- Office 365 GCC は、Azure の Azure Active Directory (Azure AD) とペアになります。 Office 365 GCC High および Office 365 DoD は、Azure Government の Azure AD とペアになります。 [相互運用性が可能である](#microsoft-365-integration)箇所を理解するために、Azure 環境に注意を払ってください。 次の表では、*可能でない* 相互運用性をダッシュ (-) でマークし、サポートが関係しないことを示しています。

- GCC-High および DoD のお客様の場合は、追加の構成が必要です。 詳細については、「[Azure Information Protection Premium Government サービスの説明](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description)」を参照してください。

> [!NOTE]
> 政府機関のお客様に対するサポートの詳細については、表の下の脚注に記載されています。
>
> GCC High および DoD のお客様向けの Azure Information Protection を構成するには、追加の手順が必要です。 詳細については、「[Azure Information Protection Premium Government サービスの説明](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description)」を参照してください。
>

|機能とサービス  |Azure  |Azure Government  |
|---------|---------|---------|
|**[Azure Information Protection スキャナー](/azure/information-protection/deploy-aip-scanner)** <sup>[1](#aipnote1)</sup>       |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|**管理**     |         |         |
|[スキャナー管理用の Azure Information Protection ポータル](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)     |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **分類とラベル付け** <sup>[2](#aipnote2)</sup>   |         |         |
| [オンプレミスのファイル サーバー/リポジトリ内のすべてのファイルに "*既定のラベル*" を適用する AIP スキャナー](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)    |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| [サポートされているオンプレミスのファイルの分類、ラベル付け、および保護を自動化するための AIP スキャナー](/azure/information-protection/deploy-aip-scanner)    |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| |  |  |

<sup><a name="aipnote1"></a> 1</sup> スキャナーは Office 365 がなくてもファイルのみをスキャンするように機能します。 スキャナーでは、Office 365 なしで、ファイルにラベルを適用することはできません。

<sup><a name="aipnote2"></a>2</sup> 分類およびラベル付けアドインは、Professional Plus (ProPlus) および Click-to-Run (C2R) のバージョンを含む、Microsoft 365 Apps (バージョン 9126.1001 以降) を使用している政府機関のお客様に対してのみサポートされます。 Office 2010、Office 2013、その他の Office 2016 バージョンではサポートされていません。

### <a name="office-365-features"></a>Office 365 の機能

|機能とサービス  |Office 365 GCC  |Office 365 GCC High |Office 365 DoD  |
|---------|---------|---------|---------|
|**管理**     |         |         | |
|- [RMS サービス管理用の PowerShell](/powershell/module/aipservice/)      |  GA       |    GA     |   GA      |
|- [AIP UL クライアントの一括操作用の PowerShell](/powershell/module/azureinformationprotection/)      |         |         |         |
|**SDK**     |         |         |         |
|- [MIP および AIP ソフトウェア開発キット (SDK)](/information-protection/develop/)     |     GA       |    GA     |   GA  |
|**カスタマイズ**     |         |         |         |
|- [ドキュメントの追跡と取り消し](/azure/information-protection/rms-client/track-and-revoke-admin)      |   GA      |  使用不可       |     使用不可    |
|**キー管理**      |         |         |         |
|- [Bring Your Own Key (BYOK)](/azure/information-protection/byok-price-restrictions)      |   GA       |    GA     |   GA   |
|- [二重キー暗号化 (DKE)](/azure/information-protection/plan-implement-tenant-key)     |    GA       |    GA     |   GA    |
|**Office ファイル** <sup>[3](#aipnote6)</sup>      |         |         |         |
|- [Protection for Microsoft Exchange Online、Microsoft SharePoint Online、Microsoft OneDrive for Business](/azure/information-protection/requirements-applications)      |     GA    |  GA <sup>[4](#aipnote3)</sup>       |   GA <sup>[4](#aipnote3)</sup>      |
|- [Rights Management コネクタを介した Exchange と SharePoint コンテンツの保護](/azure/information-protection/deploy-rms-connector)     |    GA <sup>[5](#aipnote5)</sup>      |  使用不可       |     使用不可         |
|- [Office 365 メッセージの暗号化](/microsoft-365/compliance/set-up-new-message-encryption-capabilities)      |     GA       |    GA     |   GA        |
|- [Outlook で事前構成された M/MIME 保護を自動的に適用するようにラベルを設定します](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)      |         GA       |    GA     |   GA        |
|- [Outlook を使用する際の情報の過剰共有を制御します](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)     |      GA   |  GA <sup>[6](#aipnote6)</sup>        |    GA <sup>[6](#aipnote6)</sup>      |
|**分類とラベル付け** <sup>[2](#aipnote2) / [7](#aipnote7)</sup>      |         |         |         |
|- 部門テンプレートを含むカスタム テンプレート     |     GA       |    GA     |   GA         |
|- 手動による既定の必須のドキュメント分類     |       GA       |    GA     |   GA       |
|- 自動的な推奨される分類の条件を構成する      GA       |    GA     |   GA        |
|- [PTXT、PJPG、PFILE など、Microsoft Office 以外のファイル形式の保護 (一般的な保護)](/azure/information-protection/rms-client/clientv2-admin-guide-file-types)     |        GA       |    GA     |   GA       |
|     |         |         |         |

<sup><a name="aipnote3"></a>3</sup> 現在、政府機関のお客様は AD RMS 用のモバイル デバイス拡張機能を利用できません。

<sup><a name="aipnote4"></a>4</sup> Information Rights Management と SharePoint Online (IRM で保護されたサイトとライブラリ) の組み合わせは現在使用できません。

<sup><a name="aipnote5"></a>5</sup> Information Rights Management (IRM) は、Professional Plus (ProPlus) および Click-to-Run (C2R) のバージョンを含む、Microsoft 365 Apps (バージョン 9126.1001 以降) に対してのみサポートされます。 Office 2010、Office 2013、その他の Office 2016 バージョンではサポートされていません。

<sup><a name="aipnote6"></a> 6</sup>政府機関のクラウドから商用クラウド内のユーザーへの保護されたドキュメントと電子メールを共有することは、現在できません。 商用クラウド内の Microsoft 365 Apps ユーザー、商用クラウド内の Microsoft 365 Apps 以外のユーザー、および個人向け RMS ライセンスを持つユーザーが含まれます。

<sup><a name="aipnote7"></a>7</sup> Microsoft 365 セキュリティ/コンプライアンス センターでの[機密情報の種類](/microsoft-365/compliance/sensitive-information-type-entity-definitions)の数は、リージョンによって異なる場合があります。

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

Microsoft Defender for Cloud は、データ センターのセキュリティ体制を強化する統合インフラストラクチャ セキュリティ管理システムです。Azure 内かどうかにかかわらずクラウド内とオンプレミス上のハイブリッド ワークロード全体を保護する高度な脅威防止機能があります。

詳細については、[Microsoft Defender for Cloud 製品ドキュメント](../../defender-for-cloud/defender-for-cloud-introduction.md)を参照してください。

次の表は、Azure および Azure Government の現在の Defender for Cloud 機能の可用性を示します。

| 機能とサービス                                                                                                                                                                      | Azure          | Azure Government               |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **Microsoft Defender for Cloud の無料機能**                                                                                                                                       |                |                                |
| <li> [連続エクスポート](../../defender-for-cloud/continuous-export.md)                                                                                                                 | GA             | GA                             |
| <li> [ワークフローの自動化](../../defender-for-cloud/workflow-automation.md)                                                                                                               | GA             | GA                             |
| <li> [推奨事項の除外規則](../../defender-for-cloud/exempt-resource.md)                                                                                                      | パブリック プレビュー | 利用不可                  |
| <li> [アラートの抑制ルール](../../defender-for-cloud/alerts-suppression-rules.md)                                                                                                    | GA             | GA                             |
| <li> [セキュリティ アラートのメール通知](../../defender-for-cloud/configure-email-notifications.md)                                                            | GA             | GA                             |
| <li> [エージェントと拡張機能の自動プロビジョニング](../../defender-for-cloud/enable-data-collection.md)                                                                  | GA             | GA                             |
| <li> [資産インベントリ](../../defender-for-cloud/asset-inventory.md)                                                                                                                     | GA             | GA                             |
| <li> [Microsoft Defender for Cloud のブック ギャラリーの Azure Monitor ブック レポート](../../defender-for-cloud/custom-dashboards-azure-workbooks.md)                               | GA             | GA                             |
| **Microsoft Defender のプランと拡張機能**                                                                                                                                          |                |                                |
| <li> [Microsoft Defender for servers](../../defender-for-cloud/defender-for-servers-introduction.md)                                                                                    | GA             | GA                             |
| <li> [Microsoft Defender for App Service](../../defender-for-cloud/defender-for-app-service-introduction.md)                                                                            | GA             | 利用不可                  |
| <li> [Microsoft Defender for DNS](../../defender-for-cloud/defender-for-dns-introduction.md)                                                                                            | GA             | GA                             |
| <li> [コンテナー レジストリ用 Microsoft Defender](../../defender-for-cloud/defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> |
| <li> [CI/CD ワークフロー内の画像をスキャンするコンテナー レジストリ用 Microsoft Defender](../../defender-for-cloud/defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | パブリック プレビュー | 利用不可                  |
| <li> [Microsoft Defender for Kubernetes](../../defender-for-cloud/defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             |
| <li> [Azure Arc 対応 Kubernetes クラスター用 Defender 拡張機能](../../defender-for-cloud/defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                           | パブリック プレビュー | 利用不可                  |
| <li> [Azure SQL データベース サーバー用 Microsoft Defender](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                     | GA             | GA                             |
| <li> [Microsoft Defender for SQL servers on machines](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                        | GA             | GA                             |
| <li> [オープンソース リレーショナル データベース用 Microsoft Defender](../../defender-for-cloud/defender-for-databases-introduction.md)                                                         | GA             | 利用不可                  |
| <li> [Microsoft Defender for Key Vault](../../defender-for-cloud/defender-for-key-vault-introduction.md)                                                                                | GA             | 利用不可                  |
| <li> [Microsoft Defender for Resource Manager](../../defender-for-cloud/defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             |
| <li> [Microsoft Defender for Storage](../../defender-for-cloud/defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             |
| <li> [Threat protection for Cosmos DB](../../defender-for-cloud/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                              | パブリック プレビュー | 利用不可                  |
| <li> [Kubernetes ワークロード保護](../../defender-for-cloud/kubernetes-workload-protections.md)                                                                                      | GA             | GA                             |
| <li> [Sentinel との双方向アラート同期](../../sentinel/connect-azure-security-center.md)                                                                           | パブリック プレビュー | 利用不可                  |
| **Microsoft Defender for servers の機能** <sup>[7](#footnote7)</sup>                                                                                                               |                |                                |
| <li> [Just In Time VM アクセス](../../defender-for-cloud/just-in-time-access-overview.md)                                                                                                 | GA             | GA                             |
| <li> [ファイルの整合性の監視](../../defender-for-cloud/file-integrity-monitoring-overview.md)                                                                                 | GA             | GA                             |
| <li> [アダプティブ アプリケーション制御](../../defender-for-cloud/adaptive-application-controls.md)                                                                                  | GA             | GA                             |
| <li> [アダプティブ ネットワークのセキュリティ強化機能](../../defender-for-cloud/adaptive-network-hardening.md)                                                                               | GA             | 利用不可                  |
| <li> [Docker ホストのセキュリティ強化機能](../../defender-for-cloud/harden-docker-hosts.md)                                                                                                           | GA             | GA                             |
| <li> [マシンに対する統合された脆弱性評価](../../defender-for-cloud/deploy-vulnerability-assessment-vm.md)                                                                 | GA             | 利用不可                  |
| <li> [規制へのコンプライアンス ダッシュボードおよびレポート](../../defender-for-cloud/regulatory-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                           | GA             | GA                             |
| <li> [Microsoft Defender for Endpoint のデプロイと統合ライセンス](../../defender-for-cloud/integration-defender-for-endpoint.md)                                                             | GA             | GA                             |
| <li> [AWS アカウントを接続する](../../defender-for-cloud/quickstart-onboard-aws.md)                                                                                                          | GA             | 利用不可                  |
| <li> [GCP アカウントを接続する](../../defender-for-cloud/quickstart-onboard-gcp.md)                                                                                                          | GA             | 利用不可                  |
|                                                                                                                                                                                      |                |                                |

<sup><a name="footnote1"></a>1</sup> 部分的に GA: 脆弱性スキャンで発見された特定の項目を無効にする機能はパブリック プレビュー段階です。

<sup><a name="footnote2"></a>2</sup> Azure Gov 上のコンテナー レジストリの脆弱性スキャンは、スキャン オン プッシュ機能でのみ実行できます。

<sup><a name="footnote3"></a>3</sup> コンテナー レジストリ用 Microsoft Defender が必要です。

<sup><a name="footnote4"></a>4</sup> 部分的に GA: Azure Arc 対応クラスターのサポートはパブリック プレビュー段階であり、Azure Government では使用できません。

<sup><a name="footnote5"></a>5</sup> Microsoft Defender for Kubernetes が必要です。

<sup><a name="footnote6"></a>6</sup> 部分的に GA: Microsoft Defender for Storage の脅威保護アラートの一部はパブリック プレビュー段階です。

<sup><a name="footnote7"></a>7</sup> これらの機能には、すべて [Microsoft Defender for servers](../../defender-for-cloud/defender-for-servers-introduction.md) が必要です。

<sup><a name="footnote8"></a>8</sup> クラウドの種類ごとに提供される標準が異なる場合があります。

<a name="azure-sentinel"></a>

## <a name="microsoft-sentinel"></a>Microsoft Sentinel

Microsoft Sentinel は、スケーラブルでクラウドネイティブのセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Microsoft Sentinel は、高度なセキュリティ分析と脅威インテリジェンスを企業全体で実現し、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションを提供します。

詳細については、[Microsoft Sentinel 製品ドキュメント](../../sentinel/overview.md)を参照してください。

次の表に、Azure と Azure Government での現在の Microsoft Sentinel 機能の利用可能性を示しています。

| 機能 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [オートメーション ルール](../../sentinel/automate-incident-handling-with-automation-rules.md) | パブリック プレビュー | パブリック プレビュー |
|- [Bring Your Own ML (BYO-ML)](../../sentinel/bring-your-own-ml.md) | パブリック プレビュー | パブリック プレビュー |
| - [クロステナントまたはクロスワークスペース インシデント ビュー](../../sentinel/multiple-workspace-view.md) |パブリック プレビュー | パブリック プレビュー |
| - [エンティティ分析情報](../../sentinel/enable-entity-behavior-analytics.md) | GA | パブリック プレビュー |
| - [Fusion](../../sentinel/fusion.md)<br>高度なマルチステージ攻撃の検出 <sup>[1](#footnote1)</sup> | GA | GA |
| - [ハンティング](../../sentinel/hunting.md) | GA | GA |
|- [ノートブック](../../sentinel/notebooks.md) | GA | GA |
|- [SOC インシデント監査メトリック](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [ウォッチリスト](../../sentinel/watchlists.md) | GA | GA |
| **脅威インテリジェンス サポート** | | |
| - [脅威インテリジェンス - TAXII データ コネクタ](../../sentinel/understand-threat-intelligence.md)  | GA | GA |
| - [脅威インテリジェンス プラットフォーム データ コネクタ](../../sentinel/understand-threat-intelligence.md)  | パブリック プレビュー | 利用不可 |
| - [脅威インテリジェンスの調査ブレード](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | GA | GA |
| - [URL デトネーション](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | パブリック プレビュー | 利用不可 |
| - [脅威インテリジェンス ブック](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | GA |
|**検出のサポート** | | |
| - [異常な Windows ファイル共有アクセスの検出](../../sentinel/fusion.md)  | パブリック プレビュー | 利用不可 |
| - [異常な RDP ログインの検出](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>組み込みの ML 検出 | パブリック プレビュー | 利用不可 |
| - [異常な SSH ログインの検出](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>組み込みの ML 検出 | パブリック プレビュー | 利用不可 |
| **Azure サービスのコネクタ** |  |  |
| - [Azure アクティビティ ログ](../../sentinel/data-connectors-reference.md#azure-activity) | GA | GA |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | GA | GA |
| - [Azure ADIP](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) | GA | GA |
| - [Azure DDoS Protection](../../sentinel/data-connectors-reference.md#azure-ddos-protection) | GA | GA |
| - [Microsoft Defender for Cloud](../../sentinel/connect-azure-security-center.md) | GA | GA |
| - [Microsoft Defender for IoT](../../sentinel/data-connectors-reference.md#microsoft-defender-for-iot) | パブリック プレビュー | 利用不可 |
| - [Azure Firewall ](../../sentinel/data-connectors-reference.md#azure-firewall) | GA | GA |
| - [Azure Information Protection](../../sentinel/data-connectors-reference.md#azure-information-protection) | パブリック プレビュー | 利用不可 |
| - [Azure Key Vault ](../../sentinel/data-connectors-reference.md#azure-key-vault) | パブリック プレビュー | 利用不可 |
| - [Azure Kubernetes Services (AKS)](../../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks) | パブリック プレビュー | 利用不可 |
| - [Azure SQL Databases](../../sentinel/data-connectors-reference.md#azure-sql-databases) | GA | GA |
| - [Azure WAF](../../sentinel/data-connectors-reference.md#azure-web-application-firewall-waf) | GA | GA |
| **Windows のコネクタ** |  |  |
| - [Windows Firewall](../../sentinel/data-connectors-reference.md#windows-firewall) | GA | GA |
| - [Windows セキュリティ イベント](../../sentinel/connect-windows-security-events.md) | GA | GA |
| **外部コネクタ** |  |  |
| - [Agari のフィッシング対策とブランド保護](../../sentinel/data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview) | パブリック プレビュー | パブリック プレビュー |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md) | パブリック プレビュー | パブリック プレビュー |
| - [AI Vectra 検出](../../sentinel/data-connectors-reference.md#ai-vectra-detect-preview) | パブリック プレビュー | パブリック プレビュー |
| - [Akamai セキュリティ イベント](../../sentinel/data-connectors-reference.md#akamai-security-events-preview) | パブリック プレビュー | パブリック プレビュー |
| - [Alcide kAudit](../../sentinel/data-connectors-reference.md#alcide-kaudit) | パブリック プレビュー | 利用不可 |
| - [Alsid for Active Directory](../../sentinel/data-connectors-reference.md#alsid-for-active-directory) | パブリック プレビュー | 利用不可 |
| - [Apache HTTP Server](../../sentinel/data-connectors-reference.md#apache-http-server) | パブリック プレビュー | 利用不可 |
| - [Aruba ClearPass](../../sentinel/data-connectors-reference.md#aruba-clearpass-preview) | パブリック プレビュー | パブリック プレビュー |
| - [AWS](../../sentinel/connect-data-sources.md) | GA | GA |
| - [Barracuda CloudGen Firewall](../../sentinel/data-connectors-reference.md#barracuda-cloudgen-firewall) | GA | GA |
| - [Barracuda Web App Firewall](../../sentinel/data-connectors-reference.md#barracuda-waf) | GA | GA |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/data-connectors-reference.md#better-mobile-threat-defense-mtd-preview) | パブリック プレビュー | 利用不可 |
| - [Beyond Security beSECURE](../../sentinel/data-connectors-reference.md#beyond-security-besecure) | パブリック プレビュー | 利用不可 |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md) | パブリック プレビュー | パブリック プレビュー |
| - [Broadcom Symantec DLP](../../sentinel/data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview) | パブリック プレビュー | パブリック プレビュー |
| - [Check Point](../../sentinel/data-connectors-reference.md#check-point) | GA | GA |
| - [Cisco ASA](../../sentinel/data-connectors-reference.md#cisco-asa) | GA | GA |
| - [Cisco Meraki](../../sentinel/data-connectors-reference.md#cisco-meraki-preview) | パブリック プレビュー | パブリック プレビュー |
| - [Cisco Umbrella](../../sentinel/data-connectors-reference.md#cisco-umbrella-preview) | パブリック プレビュー | パブリック プレビュー |
| - [Cisco UCS](../../sentinel/data-connectors-reference.md#cisco-unified-computing-system-ucs-preview) | パブリック プレビュー | パブリック プレビュー |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md) | パブリック プレビュー | パブリック プレビュー |
| - [Citrix Analytics WAF](../../sentinel/data-connectors-reference.md#citrix-web-app-firewall-waf-preview) | GA | GA |
| - [Common Event Format (CEF)](../../sentinel/connect-common-event-format.md) | GA | GA |
| - [CyberArk Enterprise Password Vault (EPV) Events](../../sentinel/data-connectors-reference.md#cyberark-enterprise-password-vault-epv-events-preview) | パブリック プレビュー | パブリック プレビュー |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | パブリック プレビュー | 利用不可      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | パブリック プレビュー | 利用不可      |
| - [ExtraHop Reveal(x)](../../sentinel/data-connectors-reference.md#extrahop-revealx)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/data-connectors-reference.md#f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/data-connectors-reference.md#f5-networks-asm)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | パブリック プレビュー | パブリック プレビュー |
| - [Forcepoint CASB](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | パブリック プレビュー | パブリック プレビュー |
| - [Forcepoint DLP ](../../sentinel/data-connectors-reference.md#forcepoint-data-loss-prevention-dlp-preview)                                   | パブリック プレビュー | 利用不可      |
| - [ForgeRock Common Audit for CEF](../../sentinel/connect-data-sources.md)                  | パブリック プレビュー | パブリック プレビュー |
| - [Fortinet](../../sentinel/data-connectors-reference.md#fortinet)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](../../sentinel/data-connectors-reference.md#google-workspace-g-suite-preview)                      | パブリック プレビュー | 利用不可      |
| - [Illusive 攻撃管理システム](../../sentinel/data-connectors-reference.md#illusive-attack-management-system-ams-preview)                | パブリック プレビュー | パブリック プレビュー |
| - [Imperva WAF Gateway](../../sentinel/data-connectors-reference.md#imperva-waf-gateway-preview)                             | パブリック プレビュー | パブリック プレビュー |
| - [Infoblox NIOS](../../sentinel/data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)                                    | パブリック プレビュー | パブリック プレビュー |
| - [Juniper SRX](../../sentinel/data-connectors-reference.md#juniper-srx-preview)                                      | パブリック プレビュー | パブリック プレビュー |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | パブリック プレビュー | パブリック プレビュー |
| - [NXLog Windows DNS](../../sentinel/data-connectors-reference.md#nxlog-dns-logs-preview)                                             | パブリック プレビュー | 利用不可      |
| - [NXLog LinuxAudit](../../sentinel/data-connectors-reference.md#nxlog-linuxaudit-preview)                                 | パブリック プレビュー | 利用不可      |
| - [Okta シングル サインオン](../../sentinel/data-connectors-reference.md#okta-single-sign-on-preview)                              | パブリック プレビュー | パブリック プレビュー |
| - [Onapsis Platform](../../sentinel/connect-data-sources.md)                                 | パブリック プレビュー | パブリック プレビュー |
| - [One Identity Safeguard](../../sentinel/data-connectors-reference.md#one-identity-safeguard-preview)                          | GA             | GA             |
| - [Orca Security アラート](../../sentinel/data-connectors-reference.md#orca-security-preview)                            | パブリック プレビュー | 利用不可      |
| - [Palo Alto Networks](../../sentinel/data-connectors-reference.md#palo-alto-networks)                               | GA             | GA             |
| - [Perimeter 81 のアクティビティ ログ](../../sentinel/data-connectors-reference.md#perimeter-81-activity-logs-preview)                      | GA             | 利用不可      |
| - [Proofpoint On Demand Email Security](../../sentinel/data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)             | パブリック プレビュー | 利用不可      |
| - [Proofpoint TAP](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                                   | パブリック プレビュー | パブリック プレビュー |
| - [Pulse Connect Secure](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                             | パブリック プレビュー | パブリック プレビュー |
| - [Qualys Vulnerability Management](../../sentinel/data-connectors-reference.md#qualys-vulnerability-management-vm-preview)                  | パブリック プレビュー | パブリック プレビュー |
| - [Salesforce Service Cloud](../../sentinel/data-connectors-reference.md#salesforce-service-cloud-preview)                         | パブリック プレビュー | 利用不可      |
| - [SonicWall ファイアウォール](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                              | パブリック プレビュー | パブリック プレビュー |
| - [Sophos Cloud Optix](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                               | パブリック プレビュー | 利用不可      |
| - [Sophos XG Firewall](../../sentinel/data-connectors-reference.md#sophos-xg-firewall-preview)                               | パブリック プレビュー | パブリック プレビュー |
| - [Squadra Technologies secRMM](../../sentinel/data-connectors-reference.md#squadra-technologies-secrmm)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/data-connectors-reference.md#squid-proxy-preview)                                      | パブリック プレビュー | 利用不可      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/data-connectors-reference.md#symantec-integrated-cyber-defense-exchange-icdx)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/data-connectors-reference.md#symantec-proxysg-preview)                                | パブリック プレビュー | パブリック プレビュー |
| - [Symantec VIP](../../sentinel/data-connectors-reference.md#symantec-vip-preview)                                     | パブリック プレビュー | パブリック プレビュー |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [Thycotic Secret Server](../../sentinel/data-connectors-reference.md#thycotic-secret-server-preview)                          | パブリック プレビュー | パブリック プレビュー |
| - [Trend Micro Deep Security](../../sentinel/data-connectors-reference.md#trend-micro-deep-security)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/data-connectors-reference.md#trend-micro-tippingpoint-preview)                         | パブリック プレビュー | パブリック プレビュー |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | パブリック プレビュー | 利用不可      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)           | パブリック プレビュー | パブリック プレビュー |
| - [VMware ESXi](../../sentinel/data-connectors-reference.md#vmware-esxi-preview)                                      | パブリック プレビュー | パブリック プレビュー |
| - [WireX Network Forensics Platform](../../sentinel/data-connectors-reference.md#wirex-network-forensics-platform-preview)                | パブリック プレビュー | パブリック プレビュー |
| - [Zimperium Mobile Threat Defense](../../sentinel/data-connectors-reference.md#zimperium-mobile-thread-defense-preview)                  | パブリック プレビュー | 利用不可      |
| - [Zscaler](../../sentinel/data-connectors-reference.md#zscaler)                                         | GA             | GA             |
| | | |

<sup><a name="footnote1"></a>1</sup> SSH および RDP 検出は、ソブリン クラウドでは Databricks ML プラットフォームを使用できないため、サポートされていません。

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 データ コネクタ

Office 365 GCC は、Azure の Azure Active Directory (Azure AD) とペアになります。 Office 365 GCC High および Office 365 DoD は、Azure Government の Azure AD とペアになります。

> [!TIP]
> [相互運用性が可能である](#microsoft-365-integration)箇所を理解するために、Azure 環境に注意を払ってください。 次の表では、*可能でない* 相互運用性をダッシュ (-) でマークし、サポートが関係しないことを示しています。
>

| コネクタ | Azure | Azure Government |
|--|--|--|
| **[Dynamics365](../../sentinel/data-connectors-reference.md#dynamics-365)** |  |  |
| - Office 365 GCC | パブリック プレビュー | - |
| - Office 365 GCC High | - | 利用不可 |
| - Office 365 DoD | - | 利用不可 |
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)** |  |  |
| - Office 365 GCC | パブリック プレビュー | - |
| - Office 365 GCC High | - | 利用不可 |
| - Office 365 DoD | - | 利用不可 |
| **[Microsoft Defender for Cloud Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender for Cloud Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** <br>シャドウ IT ログ |  |  |
| - Office 365 GCC | パブリック プレビュー | - |
| - Office 365 GCC High | - | パブリック プレビュー |
| - Office 365 DoD | - | パブリック プレビュー |
| **[Microsoft Defender for Cloud Apps](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)**                  <br>警告 |  |  |
| - Office 365 GCC | パブリック プレビュー | - |
| - Office 365 GCC High | - | パブリック プレビュー |
| - Office 365 DoD | - | パブリック プレビュー |
| **[Microsoft Defender for Endpoint](../../sentinel/data-connectors-reference.md#microsoft-defender-for-endpoint)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender for Identity](../../sentinel/data-connectors-reference.md#microsoft-defender-for-identity)** |  |  |
| - Office 365 GCC | パブリック プレビュー | - |
| - Office 365 GCC High | - | 利用不可 |
| - Office 365 DoD | - | 利用不可 |
| **[Microsoft Defender for Office 365](../../sentinel/data-connectors-reference.md#microsoft-defender-for-office-365)** |  |  |
| - Office 365 GCC | パブリック プレビュー | - |
| - Office 365 GCC High | - | 利用不可 |
| - Office 365 DoD | - | 利用不可 |
| **[Office 365](../../sentinel/data-connectors-reference.md#microsoft-office-365)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|  |  |

<a name="azure-defender-for-iot"></a>

## <a name="microsoft-defender-for-iot"></a>Microsoft Defender for IoT

Microsoft Defender for IoT を使用すると、IoT/OT デバイス全体にわたって包括的なセキュリティを利用して、IoT/OT イノベーションを加速させることができます。 エンド ユーザー組織のためには、Microsoft Defender for IoT からは、エージェントがなく、短期間でデプロイされるネットワーク層セキュリティが提供されます。また、さまざまな産業用機器と連動し、Microsoft Sentinel やその他の SOC ツールと相互運用されます。 オンプレミスまたは Azure が接続されている環境でデプロイします。 IoT デバイス ビルダーについては、Microsoft Defender for IoT セキュリティ エージェントを使用すると、新しい IoT デバイスと Azure IoT プロジェクトにセキュリティを直接組み込むことができます。 マイクロ エージェントには、バイナリ パッケージとしてデプロイする機能やソース コードを変更する機能など、柔軟なデプロイ オプションが用意されています。 また、マイクロ エージェントは、Linux や Azure RTOS などの標準的な IoT オペレーティング システムで利用できます。 詳細については、[Microsoft Defender for IoT 製品ドキュメント](../../defender-for-iot/index.yml)を参照してください。

次の表は、Azure および Azure Government の現在の Microsoft Defender for IoT 機能の可用性を示します。

### <a name="for-organizations"></a>組織向け

| 機能 | Azure | Azure Government |
|--|--|--|
| [オンプレミス デバイスの検出とインベントリ](../../defender-for-iot/how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md) | GA | GA |
| [脆弱性の管理](../../defender-for-iot/how-to-create-risk-assessment-reports.md) | GA | GA |
| [IoT および OT 行動分析による脅威の検出](../../defender-for-iot/how-to-work-with-alerts-on-your-sensor.md) | GA | GA |
| [脅威インテリジェンスの手動更新と自動更新](../../defender-for-iot/how-to-work-with-threat-intelligence-packages.md) | GA | GA |
| **SIEM、SOAR、XDR を使用して、IT とセキュリティを統合する** |  |  |
| [Active Directory](../../defender-for-iot/organizations/how-to-create-and-manage-users.md#integrate-with-active-directory-servers) | GA | GA |
| [ArcSight](../../defender-for-iot/organizations/how-to-accelerate-alert-incident-response.md#accelerate-incident-workflows-by-using-alert-groups) | GA | GA |
| [ClearPass (アラート & インベントリ)](../../defender-for-iot/organizations/how-to-install-software.md#attach-a-span-virtual-interface-to-the-virtual-switch) | GA | GA |
| [CyberArk PSM](../../defender-for-iot/organizations/concept-key-concepts.md#integrations) | GA | GA |
| [Email](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#email-address-action) | GA | GA |
| [FortiGate](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [FortiSIEM](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [Microsoft Sentinel](../../defender-for-iot/organizations/how-to-configure-with-sentinel.md) | パブリック プレビュー | パブリック プレビュー |
| [NetWitness](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#netwitness-action) | GA | GA |
| [Palo Alto NGFW](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [Palo Alto Panorama](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [ServiceNow (アラート & インベントリ)](../../defender-for-iot/organizations/tutorial-servicenow.md) | GA | GA |
| [SNMP MIB の監視](../../defender-for-iot/organizations/how-to-set-up-snmp-mib-monitoring.md) | GA | GA |
| [Splunk](../../defender-for-iot/organizations/tutorial-splunk.md) | GA | GA |
| [SYSLOG サーバー (CEF 形式)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG サーバー (LEEF 形式)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG サーバー (オブジェクト)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG サーバー (テキスト メッセージ)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [Web コールバック (Webhook)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#webhook-server-action) | GA | GA |

### <a name="for-device-builders"></a>デバイス ビルダーの場合

| 機能 | Azure | Azure Government |
|--|--|--|
| [Azure RTOS 用マイクロ エージェント](../../defender-for-iot/iot-security-azure-rtos.md) | GA | GA |
| [Microsoft Defender for IoT を使用して Sentinel を構成する](../../defender-for-iot/how-to-configure-with-sentinel.md) | パブリック プレビュー | パブリック プレビュー |
| **スタンドアロンのマイクロ エージェント for Linux** |  |  |
| [スタンドアロン エージェントのバイナリ インストール](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | パブリック プレビュー | パブリック プレビュー |

## <a name="azure-attestation"></a>Azure Attestation

Microsoft Azure Attestation は、プラットフォームの信頼性とその内部で実行されているバイナリの整合性をリモートで検証するための統合ソリューションです。 このサービスでは、プラットフォームから証拠を受け取り、それをセキュリティ標準を使用して検証し、構成可能なポリシーに照らし合わせて評価し、クレームベースのアプリケーション (証明書利用者、監査機関など) 向けの構成証明トークンを生成します。 

Azure Attestation は、現在、Azure Public および Government クラウドの複数のリージョンで利用できます。 Azure Government では、US Gov Virginia と US Gov Arizona の中でこのサービスをプレビュー状態で利用できます。 

詳細については、Azure Attestation の[パブリック ドキュメント](../../attestation/overview.md)を参照してください。 

| 機能 | Azure | Azure Government |
|--|--|--|
| コントロールプレーンおよびデータプレーンの操作を実行するための[ポータル エクスペリエンス](../../attestation/quickstart-portal.md) | GA | - |
| コントロールプレーンおよびデータプレーンの操作を実行するための[PowerShell エクスペリエンス](../../attestation/quickstart-powershell.md)  | GA | GA |
| TLS 1.2 の適用   | GA | GA |
| BCDR のサポート   | GA | - |
| [サービス タグの統合](../../virtual-network/service-tags-overview.md) | GA | GA |
| [不変のログ ストレージ](../../attestation/audit-logs.md) | GA | GA |
| プライベート リンクを使用したネットワーク分離 | パブリック プレビュー | - |
| [FedRAMP High 認定](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md) | GA | - |
| カスタマー ロックボックス | GA | - |

## <a name="next-steps"></a>次のステップ

- [共同責任](shared-responsibility.md)モデル、クラウド プロバイダーが処理するセキュリティ タスク、およびお客様が処理するタスクについて理解します。
- [Azure Government クラウド](../../azure-government/documentation-government-welcome.md)の機能と、連邦、州、地方の各統治機関や関連組織に対して適用されるコンプライアンスをサポートする安定した設計やセキュリティについて理解します。
- [Office 365 Government プラン](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)について理解します。
- 法的および規制基準に対する [Azure のコンプライアンス](../../compliance/index.yml)について理解します。