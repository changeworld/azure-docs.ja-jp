---
title: Azure Sentinel における脅威インテリジェンスの統合 | Microsoft Docs
description: Azure Sentinel における脅威インテリジェンス フィードの統合と使用のさまざまな形態について説明します。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2021
ms.author: yelevin
ms.openlocfilehash: c231840b6255ef693353d83323710cb3243efaef
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214303"
---
# <a name="threat-intelligence-integration-in-azure-sentinel"></a>Azure Sentinel への脅威インテリジェンスの統合

既知の脅威を検出して優先順位を付けるセキュリティ アナリストの能力を高めるために、Azure Sentinel には、[脅威インテリジェンスのフィードを使用](work-with-threat-indicators.md)する方法がいくつか用意されています。 

数多く提供されている[統合脅威インテリジェンス プラットフォーム (TIP、Threat Intelligence Platform) 製品](connect-threat-intelligence-tip.md)の 1 つを使用し、[TAXII サーバーに接続](connect-threat-intelligence-taxii.md)して STIX に適合したあらゆる脅威インテリジェンス ソースを活用できるほか、[Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) と直接やり取りするあらゆるカスタム ソリューションを利用することができます。 

効果的な調査や対応の措置につながる TI 情報でインシデントをエンリッチメント処理するために、プレイブックから脅威インテリジェンス ソースに接続することもできます。

> [!TIP]
> [マネージド サービス プロバイダー (MSSP)](mssp-protect-intellectual-property.md) のように、同じテナントに複数のワークスペースがある場合、一元化されたワークスペースにのみ脅威インジケーターを接続した方がコスト効率が高い場合があります。
>
> 同じ脅威インジケーターのセットが個別のワークスペースにインポートされている場合、ワークスペース全体で脅威インジケーターを集計するためのワークスペース横断クエリを実行できます。 MSSP インシデントの検出、調査、ハンティングのエクスペリエンス内でそれらを関連付けます。
>

## <a name="taxii-threat-intelligence-feeds"></a>TAXII 脅威インテリジェンス フィード

TAXII 脅威インテリジェンスフィードに接続するには、以下にリンクされている各ベンダー提供のデータと共に、[Azure Sentinel を STIX/TAXII 脅威インテリジェンス フィードに接続する](connect-threat-intelligence-taxii.md)手順に従います。 コネクタで使用するための必要なデータを入手するためには、適宜ベンダーに直接問い合わせてください。

### <a name="anomali-limo"></a>Anomali Limo

- [Anomali Limo フィードに接続するための要件をご覧ください](https://www.anomali.com/resources/limo)。

### <a name="cybersixgill-darkfeed"></a>Cybersixgill Darkfeed

- [Cybersixgill と Azure Sentinel の統合について@Cybersixgill](https://www.cybersixgill.com/partners/azure-sentinel/)
- Azure Sentinel を Cybersixgill TAXII Server に接続して Darkfeed にアクセスするには、[Cybersixgill に問い合わせて](mailto://azuresentinel@cybersixgill.com)、API ルート、コレクション ID、ユーザー名、パスワードを入手してください。

### <a name="financial-services-information-sharing-and-analysis-center-fs-isac"></a>Financial Services Information Sharing and Analysis Center (FS-ISAC)

- このフィードにアクセスするための資格情報を入手するには、[FS-ISAC](https://www.fsisac.com/membership?utm_campaign=ThirdParty&utm_source=MSFT&utm_medium=ThreatFeed-Join) に入会してください。

### <a name="health-intelligence-sharing-community-h-isac"></a>Health Intelligence Sharing Community (H-ISAC)

- このフィードにアクセスするための資格情報を入手するには、[H-ISAC に入会](https://h-isac.org/soltra/)してください。

### <a name="ibm-x-force"></a>IBM X-Force

- [IBM X-Force 統合の詳細情報](https://www.ibm.com/security/xforce)

### <a name="intsights"></a>IntSights

- [IntSights と Azure Sentinel の統合に関する詳細情報@IntSights](https://intsights.com/resources/intsights-microsoft-azure-sentinel)
- Azure Sentinel を IntSights TAXII Server に接続するには、Azure Sentinel に送信するデータのポリシーを構成した後、API ルート、コレクション ID、ユーザー名、パスワードを IntSights ポータルから取得してください。

### <a name="threatconnect"></a>ThreatConnect

- [STIX と TAXII に関する詳細情報 @ThreatConnect](https://threatconnect.com/stix-taxii/)
- [TAXII Services のドキュメント @ThreatConnect](https://docs.threatconnect.com/en/latest/rest_api/taxii/taxii.html)

## <a name="integrated-threat-intelligence-platform-products"></a>統合された脅威インテリジェンス プラットフォーム製品

脅威インテリジェンス プラットフォーム (TIP) フィードに接続するには、手順に従って[脅威インテリジェンス プラットフォームを Azure Sentinel に接続](connect-threat-intelligence-tip.md)します。 この手順の第 2 部では、TIP ソリューションに情報を入力する必要があります。 詳細については、以下のリンクを参照してください。

### <a name="agari-phishing-defense-and-brand-protection"></a>Agari のフィッシング対策とブランド保護

- [Agari Phishing Defense と Brand Protection](https://agari.com/products/phishing-defense/) を接続するには、組み込みの [Agari データ コネクタ](connect-agari-phishing-defense.md)を Azure Sentinel で使用します。

### <a name="anomali-threatstream"></a>Anomali ThreatStream

- [ThreatStream Integrator と各種の拡張機能](https://www.anomali.com/products/threatstream)、および ThreatStream インテリジェンスを Microsoft Graph Security API に接続するための手順をダウンロードするには、[ThreatStream のダウンロード](https://ui.threatstream.com/downloads)のページを参照してください。

### <a name="alienvault-open-threat-exchange-otx-from-att-cybersecurity"></a>AT&T Cybersecurity の AlienVault Open Threat Exchange (OTX)

- [AlienVault OTX](https://otx.alienvault.com/) は、Azure Logic Apps (プレイブック) を使用して Azure Sentinel に接続します。 オファリング全体をフル活用するうえで必要な[個別の手順](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566)を参照してください。

### <a name="eclecticiq-platform"></a>EclecticIQ Platform

- EclecticIQ Platform は Azure Sentinel と統合され、脅威の検出、ハンティング、対応を強化します。 この双方向統合の[ベネフィットとユース ケース](https://www.eclecticiq.com/resources/azure-sentinel-and-eclecticiq-intelligence-center)について確認してください。

### <a name="groupib-threat-intelligence-and-attribution"></a>GroupIB の脅威インテリジェンスと帰属特定

- [GroupIB の脅威インテリジェンスと帰属特定](https://www.group-ib.com/intelligence-attribution.html)を Azure Sentinel に接続するために、GroupIB では、Azure Logic Apps を使用しています。 オファリング全体をフル活用するうえで必要な[個別の手順](https://techcommunity.microsoft.com/t5/azure-sentinel/group-ib-threat-intelligence-and-attribution-connector-azure/ba-p/2252904)を参照してください。

### <a name="misp-open-source-threat-intelligence-platform"></a>MISP Open Source Threat Intelligence Platform

- 脅威インジケーターを Microsoft Graph Security API に移行するための MISP インスタンスをクライアントに提供するサンプルスクリプトについては、「[MISP から Microsoft Graph Security へのスクリプト](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)」を参照してください。
- [MISP プロジェクトに関する詳細情報](https://www.misp-project.org/)。

### <a name="palo-alto-networks-minemeld"></a>Palo Alto Networks MineMeld

- Azure Sentinel への接続情報を使用して [Palo Alto MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld) を構成する方法については、[MineMeld を使用して Microsoft Graph Security API に IOC を送信する方法](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)に関するページの「**MineMeld の構成**」という見出しのセクションを参照してください。

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future セキュリティ インテリジェンス プラットフォーム

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) は、Azure Logic Apps (プレイブック) を使用して Azure Sentinel に接続します。 オファリング全体をフル活用するうえで必要な[個別の手順](https://go.recordedfuture.com/hubfs/partners/microsoft-azure-installation-guide.pdf)を参照してください。

### <a name="threatconnect-platform"></a>ThreatConnect Platform

- [ThreatConnect](https://threatconnect.com/solution/) を Azure Sentinel に接続する手順については、[Microsoft Graph Security Threat Indicators 統合構成に関するガイド](https://training.threatconnect.com/learn/article/microsoft-graph-security-threat-indicators-integration-configuration-guide-kb-article)を参照してください。

### <a name="threatquotient-threat-intelligence-platform"></a>ThreatQuotient 脅威インテリジェンス プラットフォーム

- [ThreatQuotient TIP](https://www.threatq.com/) を Azure Sentinel に接続する手順とサポート情報については、[ThreatQ 統合用の Microsoft Sentinel コネクタ](https://azuremarketplace.microsoft.com/marketplace/apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?tab=overview)に関するページを参照してください。

## <a name="incident-enrichment-sources"></a>インシデント エンリッチメント処理ソース

脅威インテリジェンス フィードは、脅威インジケーターのインポートに使用されるほか、インシデント内の情報をエンリッチメント処理して、より多くのコンテキストを調査に提供するためのソースとしても利用されます。 以下のフィードは、この目的を担うと共に、[自動インシデント応答](automate-responses-with-playbooks.md)に使用するためのロジック アプリのプレイブックを提供します。

### <a name="hyas-insight"></a>HYAS Insight

- [HYAS Insight](https://www.hyas.com/hyas-insight) のインシデント エンリッチメント処理プレイブックを Azure Sentinel [GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)から探して有効にします。 "Enrich-Sentinel-Incident-HYAS-Insight-" で始まるサブフォルダーを検索してください。
- HYAS Insight ロジック アプリ [コネクタのドキュメント](/connectors/hyasinsight/)を参照してください。

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future セキュリティ インテリジェンス プラットフォーム

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) のインシデント エンリッチメント処理プレイブックを Azure Sentinel [GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)から探して有効にします。 "RecordedFuture_" で始まるサブフォルダーを検索してください。
- Recorded Future ロジック アプリ [コネクタのドキュメント](/connectors/recordedfuture/)を参照してください。

### <a name="reversinglabs-titaniumcloud"></a>ReversingLabs TitaniumCloud

- [ReversingLabs](https://www.reversinglabs.com/products/file-reputation-service) のインシデント エンリッチメント処理プレイブックを Azure Sentinel [GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/ReversingLabs/Playbooks/Enrich-SentinelIncident-ReversingLabs-File-Information)から探して有効にします。
- ReversingLabs Intelligence ロジック アプリ [コネクタのドキュメント](/connectors/reversinglabsintelligence/)を参照してください。

### <a name="riskiq-passive-total"></a>RiskIQ Passive Total

- [RiskIQ Passive Total](https://www.riskiq.com/products/passivetotal/) のインシデント エンリッチメント処理プレイブックを Azure Sentinel [GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)から探して有効にします。 "Enrich-SentinelIncident-RiskIQ-" で始まるサブフォルダーを検索してください。
- RiskIQ プレイブックの使用に関する[詳細情報](https://techcommunity.microsoft.com/t5/azure-sentinel/enrich-azure-sentinel-security-incidents-with-the-riskiq/ba-p/1534412)を参照してください。
- RiskIQ PassiveTotal ロジック アプリ [コネクタのドキュメント](/connectors/riskiqpassivetotal/)を参照してください。

### <a name="virus-total"></a>Virus Total

- [Virus Total](https://developers.virustotal.com/v3.0/reference) のインシデント エンリッチメント処理プレイブックを Azure Sentinel [GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)から探して有効にします。 "Get-VirusTotal" と "Get-VTURL" で始まるサブフォルダーを検索してください。
- Virus Total ロジック アプリ [コネクタのドキュメント](/connectors/virustotal/)を参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel に脅威インテリジェンス プロバイダーを接続する方法を説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
