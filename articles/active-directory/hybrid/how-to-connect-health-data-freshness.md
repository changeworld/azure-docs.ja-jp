---
title: Azure AD Connect Health - アラート "Health サービス データが最新ではありません" | Microsoft Docs
description: このドキュメントでは、アラート "Health サービス データが最新ではありません" の原因と、そのトラブルシューティングを実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: ca9f23703315424fcf08350ae3111a20dd94c160
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233227"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>アラート "Health サービス データが最新ではありません"

## <a name="overview"></a>概要
<li>Azure AD Connect Health では、2 時間の間にサーバーから一部のデータ ポイントが送られなかった場合に、このデータの更新状態についてのアラートが生成されます。 このアラートのタイトルは、「**Health サービス データが最新ではありません**」です。 </li>
<li>2 時間の間にサーバーから送信されたデータ要素の一部を Connect Health が受信しなかった場合に、**警告**状態通知が発生します。 警告状態通知では、テナント管理者への電子メール通知はトリガーされません。 </li>
<li>2 時間の間にサーバーから送信されたデータ要素を Connect Health がまったく受信しなかった場合に、**エラー**状態通知が発生します。 エラー状態通知では、テナント管理者への電子メール通知がトリガーされます。 </li>

>[!IMPORTANT] 
> このアラートは、Connect Health [データ リテンション期間ポリシー](reference-connect-health-user-privacy.md#data-retention-policy)に従います。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順 
* [要件セクション](how-to-connect-health-agent-install.md#requirements)を確認して、必ず要件に対応してください。
* [テスト接続ツール](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)を使用して接続の問題を検出します。
* HTTP プロキシを使用している場合は、[こちら](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)の構成手順に従ってください。 

### <a name="connect-health-for-sync"></a>Connect Health for Sync

| データ要素 | トラブルシューティングの手順 |
| --- | --- | 
| PerfCounter | - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [送信トラフィックの SSL 検査のフィルタリングまたは無効化](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [IE のセキュリティ強化が有効な場合の指定 Web サイトの許可](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) |
| AadSyncService-SynchronizationRules、 <br /> AadSyncService-Connectors、 <br /> AadSyncService-GlobalConfigurations、 <br /> AadSyncService-RunProfileResults、 <br /> AadSyncService-ServiceConfigurations、 <br /> AadSyncService-ServiceStatus | - IP アドレスに基づく送信接続、[Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページを参照してください。 <br /> - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health for ADFS

AD FS を検証し、[AD FS ヘルプ](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)のワークフローに従うための追加の手順です。

| データ要素 | トラブルシューティングの手順 |
| --- | --- | 
| PerfCounter、TestResult | - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [送信トラフィックの SSL 検査のフィルタリングまたは無効化](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [IE のセキュリティ強化が有効な場合の指定 Web サイトの許可](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) |
|  Adfs-UsageMetrics | IP アドレスに基づく送信接続、[Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページを参照してください。 | 

### <a name="connect-health-for-adds"></a>Connect Health for ADDS

| データ要素 | トラブルシューティングの手順 |
| --- | --- | 
| PerfCounter、Adds-TopologyInfo-Json、Common-TestData-Json | - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [送信トラフィックの SSL 検査のフィルタリングまたは無効化](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [IE のセキュリティ強化が有効な場合の指定 Web サイトの許可](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) <br />  - IP アドレスに基づく送信接続、[Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページを参照してください。  |


## <a name="next-steps"></a>次の手順
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.md)
