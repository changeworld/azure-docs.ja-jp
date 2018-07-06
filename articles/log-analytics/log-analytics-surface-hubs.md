---
title: Azure Log Analytics を使用した Surface Hub の監視 | Microsoft Azure
description: Surface Hub ソリューションを使用して、Surface Hub の正常性を追跡し、Surface Hub がどのように使用されているかを理解します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: dfbcdce293d6d47267892487d0760410665af94a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130772"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Log Analytics を使用して Surface Hub を監視し、その正常性を追跡する

![Surface Hub シンボル](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

この記事では、Log Analytics の Surface Hub ソリューションを使って、Microsoft Surface Hub デバイスを監視する方法について説明します。 Log Analytics は、Surface Hub の正常性を追跡するとともに、それらがどのように使用されているかを理解するために役立ちます。

各 Surface Hub には、Microsoft Monitoring Agent がインストールされます。 そのエージェントを通して、Surface Hub から Log Analytics にデータが送信されます。 Surface Hub からログ ファイルが読み込まれた後、Log Analytics に送信されます。 サーバーのオフライン状態、予定表の非同期、デバイス アカウントで Skype にログインできないなどの問題が、Log Analytics の Surface Hub ダッシュボードに表示されます。 ダッシュボードに表示されるデータを利用して、実行されていないデバイスや問題が発生しているデバイスを識別し、検出された問題に対して修正プログラムを適用できる可能性があります。

## <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。 Log Analytics で Surface Hub を管理するには、次のものが必要です。

* 監視する台数のデバイスをサポートする [Log Analytics サブスクリプション](https://azure.microsoft.com/pricing/details/log-analytics/) レベル。 Log Analytics の価格は、登録されるデバイスの台数とデータの処理量によって決まります。 Surface Hub の展開を計画する際は、この点を考慮してください。

次に、既存の Log Analytics ワークスペースを追加するか、新しいワークスペースを作成します。 これらの手順の詳細については、「[Log Analytics の起動と開始](log-analytics-get-started.md)」を参照してください。 Log Analytics ワークスペースを構成した後、Surface Hub デバイスを登録するには 2 つの方法があります。

* Intune を通して自動で
* Surface Hub デバイスの **[設定]** を通して手動で

## <a name="set-up-monitoring"></a>監視を設定する
Log Analytics を使って、Surface Hub の正常性とアクティビティを監視できます。 Surface Hub の登録は、Intune を使うか、Surface Hub で **[設定]** を使ってローカルに実行できます。

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Intune を使って Surface Hub を Log Analytics に接続する
Surface Hub を管理する Log Analytics ワークスペースのワークスペース ID とワークスペース キーが必要です。 これらは、Azure Portal でのワークスペースの設定から取得できます。

Intune は、1 つまたは複数のデバイスに適用される Log Analytics 構成設定の一元管理を可能にする Microsoft 製品です。 Intune を通してデバイスを構成するには、次の手順に従います。

1. Intune にサインインします。
2. **[設定]** > **[接続されたソース]** の順に移動します。
3. Surface Hub テンプレートに基づいてポリシーを作成するか編集します。
4. ポリシーの OMS (Azure Operational Insights) セクションに移動し、Log Analytics の "*ワークスペース ID*" と "*ワークスペース キー*" をポリシーに追加します。
5. ポリシーを保存します。
6. ポリシーを適切なデバイス グループに関連付けます。

   ![Intune ポリシー](./media/log-analytics-surface-hubs/intune.png)

その後、Intune によって、Log Analytics の設定がターゲット グループ内のデバイスと同期され、デバイスが Log Analytics ワークスペースに登録されます。

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>設定を通して Surface Hub を Log Analytics に接続する
Surface Hub を管理する Log Analytics ワークスペースのワークスペース ID とワークスペース キーが必要です。 これらは、Azure Portal での Log Analytics ワークスペースに対する設定から取得できます。

環境の管理に Intune を使用しない場合は、各 Surface Hub の **[設定]** を通して手動でデバイスを登録できます。

1. Surface Hub から **[設定]** を開きます。
2. 情報を求めるメッセージが表示されたら、デバイス管理者の資格情報を入力します。
3. **[This device (このデバイス)]** をクリックし、**[監視]** の下の **[OMS 設定の構成]** をクリックします。
4. **[監視を有効にする]** を選択します。
5. [OMS の設定] ダイアログで、Log Analytics の **[ワークスペース ID]** と **[ワークスペース キー]** を入力します。  
   ![設定](./media/log-analytics-surface-hubs/settings.png)
6. **[OK]** をクリックして、構成を完了します。

デバイスに構成が正常に適用されたかどうかを示す確認メッセージが表示されます。 成功した場合は、エージェントが Log Analytics に正常に接続されたことを示すメッセージが表示されます。 その後、デバイスが Log Analytics へのデータの送信を開始します。Log Analytics に表示されるデータを確認し、それに基づいてアクションをとることができます。

## <a name="monitor-surface-hubs"></a>Surface Hub を監視する
Log Analytics を使用した Surface Hub の監視は、その他の登録済みデバイスの監視と似ています。

1. Azure ポータルにサインインします。
2. Log Analytics ワークスペースに移動して、**[概要]** を選びます。
2. [Surface Hub] タイルをクリックします。
3. デバイスの正常性が表示されます。

   ![Surface Hub ダッシュボード](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

既存またはカスタムのログ検索に基づく[アラート](log-analytics-alerts.md)を作成できます。 Log Analytics が Surface Hub から収集するデータを使って、デバイス用に定義した条件に該当する問題とアラートを検索できます。

## <a name="next-steps"></a>次の手順
* [Log Analytics のログ検索](log-analytics-log-searches.md)を使用して、Surface Hub の詳細データを表示します。
* Surface Hub で問題が発生した場合に通知する[アラート](log-analytics-alerts.md)を作成します。
