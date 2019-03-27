---
title: Azure AD Connect Health - アラート "Health サービス データが最新ではありません" | Microsoft Docs
description: このドキュメントでは、アラート "Health サービス データが最新ではありません" の原因と、そのトラブルシューティングを実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429017"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>アラート "Health サービス データが最新ではありません"

## <a name="overview"></a>概要
Azure AD Connect Health で監視されているオンプレミスのマシン上のエージェントによって、Azure AD Connect Health サービスにデータが定期的にアップロードされます。 サービスでエージェントからのデータが受信されない場合、ポータルに表示される情報が古くなります。 問題を強調するため、サービスで "**Health サービス データが最新ではありません**" アラートが発生します。 これは、サービスで過去 2 時間以内にデータが受信されていない場合に生成されます。  

* 2 時間の間にサーバーから送信されたデータ要素の一部を Connect Health が受信しなかった場合に、**警告**状態通知が発生します。 警告状態通知では、テナント管理者への電子メール通知はトリガーされません。
* 2 時間の間にサーバーから送信されたデータ要素を Connect Health がまったく受信しなかった場合に、**エラー**状態通知が発生します。 エラー状態通知では、テナント管理者への電子メール通知がトリガーされます。


## <a name="troubleshooting-steps"></a>トラブルシューティングの手順 

> [!IMPORTANT] 
> このアラートは、Connect Health [データ リテンション期間ポリシー](reference-connect-health-user-privacy.md#data-retention-policy)に従います。

* Azure AD Connect Health エージェント サービスが、マシンで実行されていることを確認します。 たとえば、Connect Health for AD FS には 3 つのサービスが必要です。  
  ![Verify Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* [要件セクション](how-to-connect-health-agent-install.md#requirements)を確認して、必ず要件に対応してください。
* [テスト接続ツール](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)を使用して接続の問題を検出します。
* HTTP プロキシを使用する場合は、こちらの[構成手順](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)に従います。 

アラート詳細ブレードに、欠落しているサーバーからのデータ要素が一覧表示されます。 次の表は、問題をさらに絞り込むのに役立ちます。 
### <a name="connect-health-for-sync"></a>Connect Health for Sync

| データ要素 | トラブルシューティングの手順 |
| --- | --- | 
| PerfCounter | - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [送信トラフィックの SSL 検査のフィルタリングまたは無効化](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules、 <br /> AadSyncService-Connectors、 <br /> AadSyncService-GlobalConfigurations、 <br /> AadSyncService-RunProfileResults、 <br /> AadSyncService-ServiceConfigurations、 <br /> AadSyncService-ServiceStatus | - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health for ADFS

AD FS を検証し、[AD FS ヘルプ](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)のワークフローに従うための追加の手順です。

| データ要素 | トラブルシューティングの手順 |
| --- | --- | 
| PerfCounter、TestResult | - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [送信トラフィックの SSL 検査のフィルタリングまたは無効化](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | IP アドレスに基づく送信接続、[Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページを参照してください。 | 

### <a name="connect-health-for-adds"></a>Connect Health for ADDS

| データ要素 | トラブルシューティングの手順 |
| --- | --- | 
| PerfCounter、Adds-TopologyInfo-Json、Common-TestData-Json | - [Azure サービス エンドポイントに対する送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [エージェントを実行するサーバー上のファイアウォール ポート](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>次の手順
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.md)
