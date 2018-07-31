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
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240110"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Azure Monitor の Azure Active Directory のアクティビティ ログ (プレビュー)

Azure Monitor を使用して Azure AD のアクティビティ ログを独自のストレージ アカウントまたはイベント ハブにルートできるようになりました。 Azure Monitor の Azure Active Directory ログのパブリック プレビューでは、次のことを行うことができます。

* Azure ストレージ アカウント用の監査ログをアーカイブし、データを長時間保持できる
* 監査ログを Azure イベント ハブにストリームして、Splunk や QRadar などの一般的な SIEM ツールを使用して分析できる
* 監査ログをイベント ハブにストリーミングすることで、独自のカスタム ログ ソリューションと統合できる

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>サポートされるレポート

この機能を使用して、監査のアクティビティ ログやサインインのアクティビティ ログを Azure ストレージ アカウント、イベント ハブ、またはカスタム ソリューションにルートできます。 

* **監査ログ**: テナント内で実行されたすべてのタスクの履歴は、[監査ログ アクティビティ レポート](active-directory-reporting-activity-audit-logs.md)で把握できます。
* **サインイン**: 監査ログ レポートによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](active-directory-reporting-activity-sign-ins.md)で明らかにすることができます。

> [!NOTE]
> この時点では、B2C 関連の監査およびサインインのアクティビティ ログはサポートされません。
>

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure portal で Azure AD のログにアクセスするための Azure AD Free、Basic、Premium 1 または Premium 2 の[ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)。 

監査ログのデータをルートする場所によっては、次のいずれかが必要です。

* *ListKeys* に対するアクセス許可が付与された Azure ストレージ アカウント。 BLOB ストレージ アカウントではなく、一般的なストレージ アカウントを使用することをお勧めします。 ストレージの料金情報については、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=storage)を確認してください。 
* サードパーティ製のソリューションと統合するための Azure Event Hubs の名前空間。

> [!NOTE]
> Azure AD のアクティビティ ログにアクセスするには、Azure AD テナントの*グローバル管理者*または*セキュリティ管理者*である必要があります。
>

## <a name="cost-considerations"></a>コストに関する考慮事項

既に Azure AD のライセンスをお持ちの場合は、ストレージ アカウントとイベント ハブの設定に Azure サブスクリプションが必要です。 Azure サブスクリプションは無料で利用できますが、アーカイブに使用するストレージ アカウントや、ストリーミングに使用するイベント ハブなどの Azure リソースの利用には料金が発生します。 データ量とそれにより発生するコストは、テナントのサイズによって大幅に変わります。 

### <a name="storage-size-for-activity-logs"></a>アクティビティ ログのストレージ サイズ

すべての監査ログ イベントは、約 2 KB のデータ ストレージを使用します。 そのため、テナントに 100,000 人のユーザーがいる場合、1 日あたり約 150 万のイベントが発生するため、1 日あたり約 3 GB のデータ ストレージが必要になります。 書き込みは約 5 分間のバッチで発生するため、1 か月につき約 9,000 の書き込み操作が予測されます。 次の表は、1 年以上保持される米国西部の汎用 v2 ストレージ アカウントの料金の見積もりをテナントのサイズ別に示します。 アプリケーションで予測されるデータ量に基づくより正確な見積りを作成するには、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/details/storage/blobs/)を使用します。 すべての監査ログ イベントは、約 2 KB のデータ ストレージを使用します。 そのため、テナントに 100,000 人のユーザーがいる場合、1 日あたり約 150 万のイベントが発生するため、1 日あたり約 3 GB のデータ ストレージが必要になります。 書き込みは約 5 分間のバッチで発生するため、1 か月につき約 9,000 の書き込み操作が予測されます。 次の表は、1 年以上保持される米国西部の汎用 v2 ストレージ アカウントの料金の見積もりをテナントのサイズ別に示します。 アプリケーションで予測されるデータ量に基づくより正確な見積りを作成するには、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/details/storage/blobs/)を使用します。 

| ログのカテゴリ | ユーザーの数 | 1 日あたりのイベントの数 | 1 か月あたりのデータ量の概算 | 1 か月あたりの概算コスト | 1 年あたりの概算コスト |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100,000 | 150 万 | 90 GB | 1.93 ドル | 23.12 ドル |
| Audit | 1,000 | 15000 | 900 MB | 0.02 ドル | 0.24 ドル |
| サインイン | 1,000 | 34800 | 4 GB | 0.13 ドル | 1.56 ドル |
| サインイン | 100,000 | 1,500 万 | 1.7 TB | 35.41 ドル | 424.92 ドル | 


### <a name="event-hub-messages-for-activity-logs"></a>アクティビティ ログのイベント ハブのメッセージ

イベントは約 5 分間隔でバッチ処理され、その期間のすべてのイベントが含まれる単一のメッセージとして送信されます。 イベント ハブ内のメッセージの最大サイズは 256 KB で、その期間内のすべてのメッセージのサイズがそれを超える場合、複数のメッセージが送信されます。 

たとえば、ユーザーが 100,000 人を超える大規模なテナントでは、一般的に 1 秒で約 18 のイベント、5 分間で 5400 のイベントが発生します。 監査ログは 1 つのイベントにつき 2 KB であるため、データの大きさは 10.8 MB になり、その 5 分間で 43 のメッセージがイベント ハブに送信されます。 次の表は、イベント データの量に基づく、米国西部の基本的なイベント ハブのコストの見積もりを示します。 アプリケーションで予測されるデータ量に基づく正確な見積りを作成するには、[イベント ハブの料金計算ツール](https://azure.microsoft.com/pricing/details/event-hubs/)を使用します。

| ログのカテゴリ | ユーザーの数 | 1 秒あたりのイベントの数 | 5 分間隔あたりのイベントの数 | 間隔あたりの量 | 間隔あたりのメッセージの数 | 1 か月あたりのメッセージの数 | 1 か月あたりの概算コスト |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100,000 | 18 | 5400 | 10.8 MB | 43 | 371,520 | 10.83 ドル |
| Audit | 1,000 | 0.1 | 52 | 104 KB | 1 | 8640 | 10.8 ドル |
| サインイン | 1,000 | 178 | 53400 | 106.8 MB | 418 | 3,611,520 | 11.06 ドル |  


## <a name="frequently-asked-questions"></a>よく寄せられる質問

このセクションでは、Azure Monitor の Azure Active Directory のログに関するよく寄せられる質問と既知の問題を紹介します。

**Q: どこから開始すればよいですか。** 

**A:** まずは[概要](reporting-azure-monitor-diagnostics-overview.md)から開始し、この機能をデプロイするために必要なものを確認してください。 前提条件を把握したら、構成および Event Hubs へのログのルートに役立つチュートリアルを確認してください。

---

**Q: どのようなログが含まれますか。**

**A:** この機能を使用してサインインと監査のログの両方をルートできます。ただし、現時点では B2C に関連する監査イベントは含まれません。 現在サポートされているログの種類や機能ベースのログについては、[監査ログ スキーマ](reporting-azure-monitor-diagnostics-audit-log-schema.md)と[サインイン ログ スキーマ](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)に関する記事をご覧ください。 

---

**Q: アクション後、対応するログが Event Hubs に表示されるまでどれくらいの時間がかかりますか。**

**A:** ログはアクション実行後、2 分から 5 分後に Event Hubs に表示されます。 Event Hubs について詳しくは、[Event Hubs の概要](/azure/event-hubs/event-hubs-what-is-event-hubs.md)に関する記事をご覧ください。

---

**Q: アクション後、対応するログがストレージ アカウントに表示されるまでどれくらいの時間がかかりますか。**

**A:** Azure ストレージ アカウントの場合、待ち時間はアクション実行後 5 分から 15 分です。

---

**Q: データを格納する料金はどれくらいかかりますか。**

**A:** ストレージの料金は、ログのサイズや選択した保持期間によって変わります。 生成されたログの量に基づくテナントの料金のおおよその見積もりについては、[概要](reporting-azure-monitor-diagnostics-overview.md)を確認してください。

---

**Q: Event Hubs にデータをストリーミングする料金はどれくらいかかりますか。**

**A:** ストリーミングの料金は、1 分あたりに受信するメッセージの数によって異なります。 料金の計算方法、およびメッセージの数に基づくおおよその見積もりについて詳しくは、[概要](reporting-azure-monitor-diagnostics-overview.md)をご覧ください。 

---

**Q: 現時点ではどの SIEM ツールがサポートされてますか。** 

**A:** 現在、Azure Monitor は Splunk、QRadar、および Sumologic でサポートされています。 ただし、Azure Active Directory ログをサポートする SIEM ツールは Splunk のみです。 コネクタの仕組みについて詳しくは、「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)」をご覧ください。

---

**Q: 外部の SIEM ツールを使用せずにイベント ハブからデータにアクセスできますか。** 

**A:** はい、[Event Hub API](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) を使用してお使いのカスタム アプリケーションからログにアクセスできます。 

---


## <a name="next-steps"></a>次の手順

* [アクティビティ ログをストレージ アカウントにアーカイブする](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [アクティビティ ログをイベント ハブにルートする](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Azure 診断ログの詳細を確認する](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)