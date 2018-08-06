---
title: Azure Monitor の Azure Active Directory のアクティビティ ログ (プレビュー) | Microsoft Docs
description: Azure Monitor の Azure Active Directory のアクティビティ ログ (プレビュー) の概要
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0d88aef46761e8c7d8217f04befec88816587c03
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358273"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Azure Monitor の Azure AD アクティビティ ログ (プレビュー)

Azure Monitor を使用して、Azure Active Directory (Azure AD) アクティビティ ログを独自のストレージ アカウントまたはイベント ハブにルーティングできるようになりました。 Azure Monitor の Azure Active Directory ログのパブリック プレビューでは、次のことを行うことができます。

* Azure ストレージ アカウント用の監査ログをアーカイブし、データを長時間保持できます。
* 監査ログを Azure イベント ハブにストリーム配信して、Splunk や QRadar などの一般的なセキュリティ情報およびイベント管理 (SIEM) ツールを使用して分析することができます。
* 監査ログをイベント ハブにストリーミングすることで、独自のカスタム ログ ソリューションと統合することができます。

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>サポートされるレポート

この機能を使用して、監査のアクティビティ ログやサインインのアクティビティ ログを Azure ストレージ アカウント、イベント ハブ、またはカスタム ソリューションにルーティングすることができます。 

* **監査ログ**: テナント内で実行されたすべてのタスクの履歴は、[監査ログ アクティビティ レポート](active-directory-reporting-activity-audit-logs.md)で把握できます。
* **サインイン ログ**: 監査ログによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](active-directory-reporting-activity-sign-ins.md)で判断することができます。

> [!NOTE]
> 現時点では、B2C 関連の監査およびサインインのアクティビティ ログはサポートされません。
>

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure portal で Azure AD のログにアクセスするための Azure AD Free、Basic、Premium 1 または Premium 2 の[ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)。 

監査ログのデータをルーティングする場所によっては、次のいずれかが必要です。

* *ListKeys* アクセス許可が付与された Azure ストレージ アカウント。 BLOB ストレージ アカウントではなく、一般的なストレージ アカウントを使用することをお勧めします。 ストレージの料金情報については、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=storage)を参照してください。 
* サードパーティ製のソリューションと統合するための Azure Event Hubs の名前空間。

> [!NOTE]
> Azure AD のアクティビティ ログにアクセスするには、Azure AD テナントの*全体管理者*または*セキュリティ管理者*である必要があります。
>

## <a name="cost-considerations"></a>コストに関する考慮事項

既に Azure AD のライセンスをお持ちの場合は、ストレージ アカウントとイベント ハブの設定に Azure サブスクリプションが必要です。 Azure サブスクリプションは無料で利用できますが、アーカイブに使用するストレージ アカウントや、ストリーミングに使用するイベント ハブなどの Azure リソースの利用には料金が発生します。 データ量とそれにより発生するコストは、テナントのサイズによって大幅に変わる場合があります。 

### <a name="storage-size-for-activity-logs"></a>アクティビティ ログのストレージ サイズ

すべての監査ログ イベントは、約 2 KB のデータ ストレージを使用します。 テナントに 100,000 人のユーザーがいる場合、1 日あたり約 150 万のイベントが発生するため、1 日あたり約 3 GB のデータ ストレージが必要になります。 書き込みは約 5 分間のバッチで発生するため、1 か月につき約 9,000 の書き込み操作が予測されます。 

次の表は、1 年以上保持される米国西部の汎用 v2 ストレージ アカウントの料金の見積もりをテナントのサイズ別に示します。 アプリケーションで予測されるデータ量に基づくより正確な見積りを作成するには、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/details/storage/blobs/)を使用します。 

| ログのカテゴリ | ユーザーの数 | 1 日あたりのイベント | 1 か月あたりのデータ量 (概算) | 1 か月あたりのコスト (概算) | 1 年あたりのコスト (概算) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100,000 | 150 万&nbsp; | 90 GB | 1.93 ドル | 23.12 ドル |
| Audit | 1,000 | 15,000 | 900 MB | 0.02 ドル | 0.24 ドル |
| サインイン | 1,000 | 34,800 | 4 GB | 0.13 ドル | 1.56 ドル |
| サインイン | 100,000 | 1,500 万&nbsp; | 1.7 TB | 35.41 ドル | 424.92 ドル | 


### <a name="event-hub-messages-for-activity-logs"></a>アクティビティ ログのイベント ハブのメッセージ

イベントは約 5 分間隔でバッチ処理され、その期間のすべてのイベントが含まれる単一のメッセージとして送信されます。 イベント ハブ内のメッセージの最大サイズは 256 KB で、その期間内のすべてのメッセージの合計サイズがそれを超える場合、複数のメッセージが送信されます。 

たとえば、ユーザーが 100,000 人を超える大規模なテナントでは、通常は 1 秒で約 18 のイベントが発生します。これは、5 分間で 5,400 のイベントに相当します。 監査ログはイベントごとに約 2 KB なので、これは 10.8 MB のデータに相当します。 そのため、その 5 分の間隔で 43 のメッセージがイベント ハブに送信されます。 

次の表は、イベント データの量に基づく、米国西部の基本的なイベント ハブの 1 か月のコストの見積もりを示します。 アプリケーションで予測されるデータ量の正確な見積りを作成するには、[Event Hubs の料金計算ツール](https://azure.microsoft.com/pricing/details/event-hubs/)を使用します。

| ログのカテゴリ | ユーザーの数 | 1 秒あたりのイベント数 | 5 分間隔あたりのイベント数 | 間隔あたりの量 | 間隔あたりのメッセージ数 | 1 か月あたりのメッセージ数 | 1 か月あたりのコスト (概算) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100,000 | 18 | 5,400 | 10.8 MB | 43 | 371,520 | 10.83 ドル |
| Audit | 1,000 | 0.1 | 52 | 104 KB | 1 | 8,640 | 10.80 ドル |
| サインイン | 1,000 | 178 | 53,400 | 106.8&nbsp;MB | 418 | 3,611,520 | 11.06 ドル |  


## <a name="frequently-asked-questions"></a>よく寄せられる質問

このセクションでは、Azure Monitor の Azure AD のログに関するよく寄せられる質問に回答し、既知の問題について説明します。

**Q: どこから開始すればよいですか。** 

**A**: この記事では、この機能をデプロイするために必要なものについて説明します。 前提条件を満たしたら、ログの構成とイベント ハブへのルーティングを行うチュートリアルに進みます。

---

**Q: どのようなログが含まれますか。**

**A:** この機能を使用してサインイン アクティビティ ログと監査のログの両方をルーティングすることができます。ただし、現時点では B2C に関連する監査イベントは含まれません。 現在サポートされているログの種類や機能ベースのログについて調べるには、[監査ログ スキーマ](reporting-azure-monitor-diagnostics-audit-log-schema.md)と[サインイン ログ スキーマ](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)に関するページを参照してください。 

---

**Q: アクション後、対応するログがイベント ハブに表示されるまでに、どれくらいの時間がかかりますか。**

**A:** ログは、アクション実行後、2 分から 5 分後にイベント ハブに表示されます。 Event Hubs の詳細については、「[Azure Event Hubs とは](../event-hubs/event-hubs-about.md)」を参照してください。

---

**Q: アクション後、対応するログがストレージ アカウントに表示されるまでに、どれくらいの時間がかかりますか。**

**A:** Azure ストレージ アカウントの場合、待ち時間はアクション実行後 5 分から 15 分です。

---

**Q: データを格納する料金はどれくらいかかりますか。**

**A:** ストレージの料金は、ログのサイズと選択した保持期間によって変わります。 生成されたログの量に依存する、テナントの推定コストの一覧については、[アクティビティ ログのストレージ サイズ](https://review.docs.microsoft.com/en-us/azure/active-directory/reporting-azure-monitor-diagnostics-overview?branch=pr-en-us-47660#storage-size-for-activity-logs)に関するセクションを参照してください。

---

**Q: データをイベント ハブにストリーミングする料金はどれくらいかかりますか。**

**A:** ストリーミングの料金は、1 分あたりに受信するメッセージの数によって異なります。 この記事では、コストの計算方法を示し、メッセージ数に基づくコスト見積もりの一覧を示します。 

---

**Q: 現時点ではどの SIEM ツールがサポートされてますか。** 

**A:** 現在、Azure Monitor は Splunk、QRadar、および Sumo Logic でサポートされています。 ただし、Azure AD ログでサポートされている SIEM ツールは Splunk のみです。 コネクタのしくみの詳細については、「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)」を参照してください。

---

**Q: 外部の SIEM ツールを使用せずにイベント ハブからデータにアクセスできますか。** 

**A:** はい。 カスタム アプリケーションからログにアクセスするには、[Event Hubs API](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) を使用します。 

---


## <a name="next-steps"></a>次の手順

* [アクティビティ ログをストレージ アカウントにアーカイブする](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [アクティビティ ログをイベント ハブにルーティングする](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Azure 診断ログの詳細を確認する](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
