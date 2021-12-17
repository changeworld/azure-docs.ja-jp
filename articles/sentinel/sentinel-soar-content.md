---
title: Azure Sentinel SOAR コンテンツ カタログ | Microsoft Docs
description: この記事では、セキュリティ オーケストレーション、オートメーション、および応答 (SOAR) のために Azure Sentinel によって提供されるコンテンツを表示し詳細を説明します。これには、プレイブックや Logic Apps コネクタが含まれます。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2021
ms.author: yelevin
ms.openlocfilehash: 28b5e0de931df0f84c2761babc9b0a85a7bbe9c2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711719"
---
# <a name="azure-sentinel-soar-content-catalog"></a>Azure Sentinel の SOAR コンテンツのカタログ

Azure Sentinel には、セキュリティ オーケストレーション、オートメーション、応答 (SOAR) 向けのさまざまなプレイブックおよびコネクタが用意されています。このため、Azure Sentinel を、ご利用の環境内の任意の製品またはサービスと容易に統合できます。

以下に一覧した統合には、次のコンポーネントの一部が含まれる場合もあればすべてが含まれる場合もあります。

| コンポーネントの種類 | 目的 | ユース ケースおよび関連する手順 |
| -------------- | ------- | -------------------------------- |
| **プレイブック テンプレート** | ワークフローの自動化 | プレイブック テンプレートを使用して、脅威に自動的に対応するための既製のプレイブックをデプロイします。<br><br>[Azure Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md) |
| **Azure Logic Apps マネージド コネクタ** | プレイブックを作成するための構成要素 | プレイブックでは、マネージド コネクタを使用して、Microsoft の、および Microsoft 以外の数百のサービスと通信します。<br><br>[Logic Apps コネクタとそのドキュメントの一覧](/connectors/connector-reference/) |
| **Azure Logic Apps カスタム コネクタ** | プレイブックを作成するための構成要素 | 事前構築コネクタとして使用できないサービスと通信することが必要な場合があります。 カスタム コネクタは、ユーザーがコネクタを作成 (および共有) し、独自のトリガーとアクションを定義するのを許可することで、このニーズに対応しています。<br><ul><li>[カスタム コネクタの概要](/connectors/custom-connectors/)<li>[独自のカスタム Logic Apps コネクタを作成する](/connectors/custom-connectors/create-logic-apps-connector) |
|

SOAR 統合とそのコンポーネントは、次の場所で確認できます。

- Azure Sentinel ソリューション
- [Azure Sentinel Automation] ブレードの [プレイブック テンプレート] タブ
- Logic Apps デザイナー (マネージド Logic Apps コネクタ用)
- Azure Sentinel の GitHub リポジトリ

> [!TIP]
> - SOAR 統合の多くは、[Azure Sentinel ソリューション](sentinel-solutions.md)の一部として、関連するデータ コネクタ、分析ルール、およびブックと共にデプロイできます。 詳細については、「[Azure Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)」を参照してください。
> - Azure Sentinel コミュニティによって他にも統合が提供されていて、GitHub リポジトリ内で確認できます。
> - 一覧に記載されていない、または現在サポートされている製品またはサービスをお持ちの場合は、機能要求を送信してください。  
> 次のツールを使用すれば、独自に作成することもできます。
>    - Logic Apps カスタム コネクタ
>    - Azure 関数
>    - Logic Apps HTTP 呼び出し


## <a name="atlassian"></a>Atlassian

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Jira** | [マネージド Logic Apps コネクタ](/connectors/jira/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | インシデントを同期する |
|

## <a name="check-point"></a>Check Point

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Check Point NGFW**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#check-point)) | カスタム Logic Apps コネクタ<br><br>プレイブック | CheckPoint |  |
|

## <a name="cisco"></a>Cisco

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Cisco ASA、<br>Cisco Meraki** | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ | IP をブロックする |
| **Cisco FirePower** | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ | IP と URL をブロックする |
| **Cisco ISE**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#cisco)) | カスタム Logic Apps コネクタ<br><br>プレイブック | Microsoft |  |
| **Cisco Umbrella**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#cisco)) | カスタム Logic Apps コネクタ<br><br>プレイブック | Microsoft | ドメイン、 <br>ポリシー管理、 <br>変換先リストの管理、 <br>エンリッチメント、および調査をブロックする |
|

## <a name="crowdstrike"></a>Crowdstrike

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Falcon Endpoint Protection**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#crowdstrike)) | プレイブック | Microsoft | エンドポイント エンリッチメント、<br>エンドポイントの分離 |
|

## <a name="f5"></a>F5

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Big-IP** | プレイブック | コミュニティ | IP と URL をブロックする |
|

## <a name="forcepoint"></a>Forcepoint

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Forcepoint NGFW** | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ | IP と URL をブロックする |
|

## <a name="fortinet"></a>Fortinet

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **FortiGate**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#fortinet-fortigate)) | カスタム Logic Apps コネクタ<br><br>Azure 関数<br><br>プレイブック | Microsoft | IP と URL をブロックする |
|

## <a name="freshdesk"></a>Freshdesk

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Freshdesk** | [マネージド Logic Apps コネクタ](/connectors/freshdesk/) |  | インシデントを同期する |
|


## <a name="have-i-been-pwned"></a>Have I Been Pwned

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Have I Been Pwned** | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ |  |
|

## <a name="hyas"></a>HYAS

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **HYAS Insight**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#hyas)) | [マネージド Logic Apps コネクタ](/connectors/hyasinsight/)<br><br>プレイブック | HYAS |  |
|

## <a name="ibm"></a>IBM

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Resilient** | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ | インシデントを同期する |
|

## <a name="microsoft"></a>Microsoft

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Azure DevOps** | マネージド Logic Apps コネクタ<br><br>プレイブック | Microsoft<br><br>コミュニティ | インシデントを同期する |
| **Azure Firewall**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#azure)) | カスタム Logic Apps コネクタ<br><br>プレイブック | Microsoft | IP をブロックする |
| **Azure AD Identity Protection** | [マネージド Logic Apps コネクタ](/connectors/azureadip/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | ユーザー エンリッチメント、 <br>ユーザーの修復 |
| **Azure AD** | [マネージド Logic Apps コネクタ](/connectors/azuread/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | ユーザー エンリッチメント、 <br>ユーザーの修復 |
| **Azure Data Explorer** | [マネージド Logic Apps コネクタ](/connectors/kusto/) | Microsoft | クエリと調査 |
| **Azure Log Analytics データ コレクター** | [マネージド Logic Apps コネクタ](/connectors/azureloganalyticsdatacollector/) | Microsoft<br><br>コミュニティ | クエリと調査 |
| **Microsoft Defender for Endpoint** | [マネージド Logic Apps コネクタ](/connectors/wdatp/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | エンドポイント エンリッチメント、 <br>エンドポイントの分離 |
| **Microsoft Teams** | [マネージド Logic Apps コネクタ](/connectors/teams/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | 通知、 <br>コラボレーション、 <br>人間が関与する応答の作成 |
|

## <a name="okta"></a>Okta

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Okta** | マネージド Logic Apps コネクタ<br><br>プレイブック | コミュニティ | ユーザー エンリッチメント、 <br>ユーザーの修復 |
|

## <a name="palo-alto"></a>Palo Alto

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Palo Alto PAN-OS**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#palo-alto)) | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ | IP と URL をブロックする |
| **Wildfire** | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ | Filehash エンリッチメントと応答 |
|

## <a name="proofpoint"></a>Proofpoint

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Proofpoint TAP**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#proofpoint)) | カスタム Logic Apps コネクタ<br><br>プレイブック | Microsoft | アカウント エンリッチメント |
|

## <a name="recorded-future"></a>Recorded Future

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Recorded Future Intelligence** | [マネージド Logic Apps コネクタ](/connectors/recordedfuture/)<br><br>プレイブック | Recorded Future | エンティティ エンリッチメント |
|

## <a name="reversinglabs"></a>ReversingLabs

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **TitaniumCloud File Enrichment**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#reversinglabs)) | [マネージド Logic Apps コネクタ](/connectors/reversinglabsintelligence/)<br><br>プレイブック | ReversingLabs | FileHash エンリッチメント |
|

## <a name="riskiq"></a>RiskIQ

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **RiskIQ Digital Footprint**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#riskiq)) | [マネージド Logic Apps コネクタ](/connectors/riskiqdigitalfootprint/)<br><br>プレイブック | RiskIQ | エンティティ エンリッチメント |
| **RiskIQ Passive Total** | [マネージド Logic Apps コネクタ](/connectors/riskiqpassivetotal/)<br><br>プレイブック | RiskIQ | エンティティ エンリッチメント |
| **RiskIQ Security Intelligence**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#riskiq)) | [マネージド Logic Apps コネクタ](/connectors/riskiqintelligence/)<br><br>プレイブック | RiskIQ | エンティティ エンリッチメント |
|

## <a name="servicenow"></a>ServiceNow

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **ServiceNow** | [マネージド Logic Apps コネクタ](/connectors/service-now/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | インシデントを同期する |
|

## <a name="slack"></a>Slack

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Slack** | [マネージド Logic Apps コネクタ](/connectors/slack/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | 通知、 <br>コラボレーション |
|

## <a name="virus-total"></a>Virus Total

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Virus Total** | [マネージド Logic Apps コネクタ](/connectors/virustotal/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | エンティティ エンリッチメント |
|

## <a name="vmware"></a>VMware

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Carbon Black Cloud**<br>([ソリューションとして使用可能](sentinel-solutions-catalog.md#vmware)) | カスタム Logic Apps コネクタ<br><br>プレイブック | コミュニティ | エンドポイント エンリッチメント、 <br>エンドポイントの分離 |
|

## <a name="zendesk"></a>Zendesk

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Zendesk** | [マネージド Logic Apps コネクタ](/connectors/zendesk/)<br><br>プレイブック | Microsoft<br><br>コミュニティ | インシデントを同期する |
|

## <a name="zscaler"></a>Zscaler

| 製品 | 統合コンポーネント | サポートしているもの | シナリオ |
| --- | --- | --- | --- |
| **Zscaler** | プレイブック | Microsoft | URL の修復、 <br>インシデント エンリッチメント |
|

## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure Sentinel ソリューションの概要と、それらを見つけてデプロイする方法について説明しました。

- [Azure Sentinel ソリューション](sentinel-solutions.md)について理解を深める。
- [Azure Sentinel ソリューションを検索してデプロイする](sentinel-solutions-deploy.md)。
