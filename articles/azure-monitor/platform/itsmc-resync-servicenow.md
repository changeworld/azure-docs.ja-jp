---
title: ServiceNow の同期に関する問題を手動で修正する方法
description: ServiceNow への接続をリセットし、Microsoft Azure のアラートで ServiceNow を再び呼び出せるようにします
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 3e836873219bde3836f2863e328b0b6f5b89addc
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507287"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>ITSM Connector での問題のトラブルシューティング

この記事では、ITSM Connector の一般的な問題とそのトラブルシューティングの方法について説明します。

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 管理者は、その通知を見て、システムのユーザーが問題に気付く前に問題を識別して対処できます。 アラートの詳細については、「Microsoft Azure のアラートの概要」を参照してください。
お客様は、メール、SMS、Webhook を使用するか、ソリューションを自動化するかどうかにかかわらず、アラートを通知する方法を選択できます。 通知を受けるもう 1 つのオプションは、ITSM を使用するものです。
ITSM には、ServiceNow などの外部のチケット発行システムにアラートを送信するオプションが用意されています。

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>インシデントと変更要求データを視覚化および分析する

ITSMC では、接続を設定したときの構成に応じて、最大 120 日分のインシデントと変更要求データを同期できます。 このデータのログ レコードのスキーマについては、この記事の[追加情報のセクション](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#additional-information)をご覧ください。

インシデントと変更要求データは、ITSMC ダッシュボードを使用して視覚化できます。

![ITSMC ダッシュボードを示すスクリーンショット。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

ダッシュボードでは、コネクタの状態に関する情報も提供されます。これは、接続の問題を分析する際の開始点として利用できます。

Service Map で、影響を受けたコンピューターに対して同期されたインシデントを視覚化することもできます。

サービス マップは、Windows と Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、重要なサービスを提供する相互接続されたシステムとしてサーバーを表示できます。 Service Map には、TCP 接続の任意のアーキテクチャ全体にわたるサーバー、プロセス、ポートの間の接続が表示されます。 エージェントのインストール以外に構成は必要ありません。 詳細については、[Service Map の使用](../insights/service-map.md)に関するページを参照してください。

Service Map を使用している場合は、次に示すように、ITSM ソリューションで作成されたサービス デスク項目を表示できます。

![Log Analytics の画面を示すスクリーンショット。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>ServiceNow の同期に関する問題を手動で修正する方法

Azure Monitor は、サードパーティの IT Service Management (ITSM) プロバイダーに接続できます。 ServiceNow は、これらのプロバイダーの 1 つです。

セキュリティ上の理由から、ServiceNow による接続に使用する認証トークンの更新が必要になる場合があります。
次の同期プロセスを使用して、接続を再アクティブ化し、トークンを更新します。


1. 上部の検索バナーでソリューションを検索し、関連するソリューションを選択します

    ![上部の検索バナーと、関連するソリューションを選択する場所を示すスクリーンショット。](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. ソリューション画面で、サブスクリプション フィルターで [すべてを選択] を選択し、"ServiceDesk" でフィルター処理します

    ![[すべてを選択] を選択する場所と、ServiceDesk によってフィルター処理する場所を示すスクリーンショット。](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. ITSM 接続のソリューションを選択します。
1. 左側のバナーで [ITSM connection]\(ITSM 接続\) を選択します。

    ![ITSM 接続を選択する場所を示すスクリーンショット。](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 各コネクタを一覧から選択します。 
    1. 構成するにはコネクタ名をクリックします
    1. 使用されなくなったコネクタを削除します

    1. パートナーの種類に基づいて、[これらの定義](./itsmc-connections.md)に従ってフィールドを更新します

    1. [同期] をクリックします

       ![[同期] ボタンが強調表示されているスクリーンショット。](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. [保存] をクリックします

        ![新しい接続](media/itsmc-resync-servicenow/save-8bit.png)

f.    通知を確認して、プロセスが正常に終了したかどうかを確認します

## <a name="troubleshoot-itsm-connections"></a>ITSM 接続のトラブルシューティング

- 接続対象ソースの UI からの接続が失敗し、"**接続の保存中にエラーが発生しました**" というメッセージが表示される場合は、次の手順を実行してください。
   - ServiceNow、Cherwell、Provance の接続の場合:  
     - 各接続のユーザー名、パスワード、クライアント ID、クライアント シークレットが正しく入力されていることを確認します。  
     - 対応する ITSM 製品で、接続を作成するための十分な特権を持っていることを確認します。  
   - Service Manager 接続の場合:  
     - Web アプリが正常にデプロイされていること、およびハイブリッド接続が作成されていることを確認します。 オンプレミスの Service Manager コンピューターとの接続が正常に確立されていることを確認するには、[ハイブリッド接続](./itsmc-connections.md#configure-the-hybrid-connection)の作成に関するドキュメントで説明しているように Web アプリの URL に移動します。  

- ServiceNow のデータが Log Analytics に同期されていない場合は、ServiceNow インスタンスがスリープ状態になっていことを確認します。 ServiceNow dev インスタンスは、長時間アイドル状態になっているとスリープ状態に移行することがあります。 それが起きているのでなければ、問題を報告してください。
- Log Analytics アラートが発生しても ITSM 製品内に作業項目が作成されない場合、構成項目が作成されなかったり作業項目にリンクされなかったりする場合、その他の情報が必要な場合は、以下のリソースを参照してください。
   -  ITSMC: このソリューションには、接続、作業項目、コンピューターなどの概要が表示されます。 **[コネクタの状態]** というラベルのタイルを選択します。 そうすると、適切なクエリを使用して **ログ検索** が行われます。 詳細については、`LogType_S` が `ERROR` になっているログ レコードを参照してください。
   - **[ログ検索]** ページ:クエリ `*ServiceDeskLog_CL*` を使用して、エラーと関連情報を直接表示します。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web アプリのデプロイのトラブルシューティング

-   Web アプリのデプロイに関する問題がある場合は、サブスクリプションでリソースを作成/デプロイするためのアクセス許可があることを確認します。
-   [スクリプト](itsmc-service-manager-script.md)の実行時に "**オブジェクト参照がオブジェクトのインスタンスに設定されていません**" というエラーが発生する場合は、 **[ユーザー構成]** セクションに有効な値を入力したことを確認します。
-   Service Bus Relay 名前空間を作成できない場合は、必要なリソース プロバイダーがサブスクリプションに登録されていることを確認します。 登録されていない場合は、Azure portal で、Service Bus Relay 名前空間を手動で作成します。 これは、Azure portal で[ハイブリッド接続を作成する](./itsmc-connections.md#configure-the-hybrid-connection)ときに作成することもできます。

## <a name="next-steps"></a>次の手順

[IT Service Management 接続](itsmc-connections.md)の詳細を確認する
