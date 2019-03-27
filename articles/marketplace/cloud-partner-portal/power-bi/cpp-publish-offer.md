---
title: Power BI アプリ オファーを発行する - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace に Power BI アプリ オファーを発行します。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665713"
---
# <a name="publish-power-bi-app-offer"></a>Power BI アプリ オファーを発行する

ポータルでオファーを定義し、関連する技術資産を作成した後の最後のステップでは、オファーを発行するために送信します。  このプロセスを始めるには、**[新しいプラン]** ウィンドウで垂直メニューの **[発行]** ボタンをクリックします。  詳しくは、「[Azure Marketplace および AppSource のオファーを発行する](../manage-offers/cpp-publish-offer.md)」をご覧ください。


## <a name="publishing-steps"></a>発行手順

次の図では、"稼働" させるための発行プロセスの主な手順を示します。

![Power BI アプリの発行プロセスの手順](./media/publishing-process-steps.png)

次の表では、これらのステップについて説明し、完了するのに要する最大推定時間を示します。

|   発行手順            |   Time     |   説明                                                                  |
| --------------------         |------------| ----------------                                                               |
| 前提条件の検証       | 15 分     | プラン情報とプラン設定が有効化されます。                            |
| 認定                | 1 ～ 7 日間   | Power BI の認定チームがオファーを分析します。 提供されたインストール URL を使用してアプリをインストールすることにより、Power BI アプリで手動検証テストを実行します。 アプリ認定プロセスの一環として、主要な検証が行われます。以下をご覧ください。         |
| 梱包                    | 1 時間以内  | オファーの技術資産が、顧客用にパッケージ化されます。                        |
| リード生成の登録 | 1 時間以内  | リード システムを構成して展開します。                                      |
| 発行元のサインオフ            | \-         | オファーが稼働状態になる前に、最終的な発行元のレビューと確認が行われます。 また、オファリングのプレビューへのリンクも表示されます。 プレビューの表示に問題がなければ、**[状態]** タブの **[Go Live]\(ライブに移行\)** ボタンをクリックします。このアクションにより、AppSource へのアプリの掲載要求がオンボード チームに送信されます。    |
| ライブ                         | \< 3 時間 | オファーは AppSource 上のリストに公開され ("ライブ")、顧客は Power BI のサブスクリプションでアプリを表示して展開できるようになります。 確認の電子メールも届きます。 どの時点でも、**[すべてのプラン]** タブをクリックすると、右側の列にすべてのプランの状態が表示されます。 **[状態]** タブをクリックすると、オファーの発行フローの状態の詳細を確認できます。 |
|   |   |

このプロセスの完了には、最大で 8 日かかります。 以上の発行手順が済むと、Power BI アプリのオファーは [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) の Power BI アプリ セクションに掲載されます。


### <a name="app-certification-process"></a>アプリ認定プロセス

Microsoft のオンボード チームは、以下のプロセスを使用して、Power BI オファーの提出を検証します。

1. 法的ドキュメントおよびヘルプ リンクが確認されます。
2. サポートの連絡先情報が検証されます。
3. インストーラー URL を使用して、適切なインストールが確認されます。 
4. アプリで、マルウェアや他の悪意のあるコンテンツがスキャンされます。 
5. 表示されるコンテンツがアプリの説明と一致することの検証が実行されます。
6. Power BI でアプリ関連の操作が期待どおりに動作することが確認されます (サンプル データを使用してレポートとダッシュ ボードを開く、カスタム データ ソースに接続する、更新する、など)。

何らかの問題が見つかった場合、認定チームはフィードバックを提供します。  Power BI アプリの要件について詳しくは、[Power BI アプリのドキュメント](https://go.microsoft.com/fwlink/?linkid=2028636)をご覧ください。


## <a name="next-steps"></a>次の手順

[AppSource Marketplace](https://appsource.microsoft.com) でアプリを定期的に監視することをお勧めします。  さらに、自分の Marketplace の顧客と利用状況について分析情報を提供するには、[Cloud パートナー ポータル](https://cloudpartner.azure.com/#insights)の [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) の機能を使用する必要があります。  [オファーに対して特定の更新](./cpp-update-existing-offer.md)を実行することもできます。
