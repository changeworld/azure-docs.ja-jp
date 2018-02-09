---
title: "IoT Hub のイベントを使用して Azure Logic Apps をトリガーする | Microsoft Docs"
description: "Azure Event Grid のイベント ルーティング サービスを使い、IoT Hub のイベントに基づいて Azure Logic Apps のアクションを実行する自動化されたプロセスを作成します。"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: f54db95b0dfe5dc39c8e2a85375e56a93d1562ee
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する

Azure Event Grid を使うと、ダウンストリームのビジネス アプリケーションのアクションをトリガーすることによって、IoT Hub のイベントに対応することができます。

この記事では、IoT Hub と Event Grid を使うサンプルを構成する手順について説明します。 最終的に、デバイスが IoT Hub に追加されるたびに通知メールを送信するように Azure Logic Apps が設定されます。 

## <a name="prerequisites"></a>前提条件

* Azure Logic Apps がサポートするメール プロバイダー (Office 365 Outlook、Outlook.com、Gmail など) のメール アカウント。 このメール アカウントは、イベント通知の送信に使われます。 サポートされている Logic App コネクタの完全な一覧については、「[コネクタの概要](https://docs.microsoft.com/connectors/)」をご覧ください
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、[無料アカウントを作成する](http://azure.microsoft.com/pricing/free-trial/)ことができます。
* Azure の IoT Hub。 まだ作成していない場合は、「[IoT Hub の概要](../iot-hub/iot-hub-csharp-csharp-getstarted.md)」のチュートリアルをご覧ください。 

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

最初にロジック アプリを作成し、仮想マシンのリソース グループを監視する Event Grid トリガーを追加します。 

### <a name="create-a-logic-app-resource"></a>ロジック アプリ リソースを作成する


1. [Azure Portal](https://portal.azure.com) で、**[新規作成]** > **[エンタープライズ統合]** > **[Logic App]** の順に選びます。

   ![ロジック アプリを作成する](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. ロジック アプリにサブスクリプション内で一意の名前を指定し、IoT Hub と同じサブスクリプション、リソース グループ、場所を選びます。 
3. 準備ができたら、**[ダッシュボードにピン留めする]**、**[作成]** の順に選択します。

   これで、ロジック アプリの Azure リソースが作成されました。 Azure によってロジック アプリがデプロイされたら、すばやく開始できるように Logic Apps デザイナーによって一般的なパターンのテンプレートが表示されます。

   > [!NOTE] 
   > **[ダッシュボードにピン留めする]** を選択すると、ロジック アプリが Logic Apps デザイナーで自動的に開きます。 開かない場合は、手動でロジック アプリを見つけて開くことができます。

4. ロジック アプリ デザイナーで、**[テンプレート]** の **[空のロジック アプリ]** を選ぶと、ロジック アプリを最初から作成できます。

## <a name="select-a-trigger"></a>トリガーを選択する

トリガーは、ロジック アプリを開始する特定のイベントです。 このチュートリアルでは、ワークフローを開始するトリガーは、HTTP 経由での要求の受信です。  

1. コネクタとトリガーの検索バーに、「**HTTP**」と入力します。
2. トリガーとして **[要求 - HTTP 要求の受信時]** を選びます。 

   ![HTTP 要求トリガーを選ぶ](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。 

   ![HTTP 要求トリガーを選ぶ](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. 次のサンプル JSON コードをテキスト ボックスに貼り付けて、**[完了]** を選びます。

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<IoT hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice",
       "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
       "opType": "DeviceCreated"
     },
     "dataVersion": "",
     "metadataVersion": "1"
   }]
   ```
5. "**application/json に設定されている Content-Type ヘッダーを要求に忘れずに含めてください**" というポップアップ通知を受け取る場合があります。 この指摘は無視しても安全なので、次のセクションに進みます。 


### <a name="create-an-action"></a>アクションを作成する

アクションは、トリガーがロジック アプリのワークフローを開始した後に発生する手順です。 このチュートリアルのアクションは、メール プロバイダーからのメール通知の送信です。 

1. **[新しいステップ]**、**[アクションの追加]** の順に選びます。 

   ![[新しいステップ]、[アクションの追加]](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. **[電子メール]** を検索します。 
3. 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 このチュートリアルでは、**Office 365 Outlook** を使います。 他のメール プロバイダーの手順も同様です。 

   ![メール プロバイダーのコネクタを選ぶ](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. **[電子メールの送信]** アクションを選びます。 
5. メッセージに従ってメール アカウントにサインインします。 
6. メール テンプレートを作成します。 
   * **[宛先]**: 通知メールを受信するメール アドレスを入力します。 このチュートリアルでは、テスト用にアクセスできるメール アカウントを使います。 
   * **[件名]** と **[本文]**: メールのテキストを記述します。 イベント データに基づく動的なコンテンツを含めるには、選択ツールから JSON プロパティを選びます。  

   メール テンプレートは次の例のようになります。

   ![メールの情報を入力する](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. ロジック アプリを保存し、 

### <a name="copy-the-http-url"></a>HTTP の URL をコピーする

Logic Apps デザイナーを終了する前に、ロジック アプリがトリガーをリッスンする URL をコピーします。 この URL を使って、Event Grid を構成します。 

1. **[HTTP 要求の受信時]** トリガー構成ボックスをクリックして展開します。 
2. **[HTTP POST の URL]** の横にあるコピー ボタンを選んで値をコピーします。 

   ![HTTP POST の URL をコピーする](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. 次のセクションで参照できるように、この URL を保存します。 

## <a name="publish-an-event-from-iot-hub"></a>IoT Hub からイベントを発行する

このセクションでは、発生したらイベントを発行するように IoT Hub を構成します。 

1. Azure Portal で、お使いの IoT ハブに移動します。 
2. **[イベント グリッド]** を選びます。

   ![Event Grid の詳細を表示する](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. **[イベント サブスクリプション]** を選びます。 

   ![新しいイベント サブスクリプションを作成する](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. 次の値でイベント サブスクリプションを作成します。 
   * **[名前]**: わかりやすい名前を指定します。
   * **[すべてのイベントの種類を購読します]**: チェック ボックスをオフにします。
   * **[イベントの種類]**: **[DeviceCreated]** を選びます。
   * **[サブスクライバーの種類]**: **[webhook]** を選びます。
   * **[サブスクライバー エンドポイント]**: ロジック アプリからコピーした URL を貼り付けます。 

   以上でイベント サブスクリプションを保存すると、IoT hub でデバイスが作成されるたびに通知を受け取るようになります。 しかし、このチュートリアルでは、特定のデバイスでフィルター処理をするためにオプションのフィールドを追加します。 

   * **[プレフィックス フィルター]**: ビル 1 でのデバイス イベントでフィルター処理するため、「`devices/Building1_`」と入力します。
   * **[サフィックス フィルター]**: 温度に関するデバイス イベントでフィルター処理するため、「`_Temperature`」と入力します。

   終了すると、フォームは次の例のようになります。 

   ![サンプルのイベント サブスクリプション フォーム](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. **[作成]** を選び、イベント サブスクリプションを保存します。

## <a name="create-a-new-device"></a>新しいデバイスを作成する

新しいデバイスを作成してイベント通知メールをトリガーすることで、ロジック アプリをテストします。 

1. IoT Hub から、**[IoT Devices]\(IoT デバイス\)** を選びます。 
2. **[追加]**を選択します。
3. **[デバイス ID]** に「`Building1_Floor1_Room1_Temperature`」と入力します。
4. **[保存]** を選択します。 
5. 異なるデバイス ID で複数のデバイスを追加し、イベント サブスクリプション フィルターをテストできます。 次の例を試してください。 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

IoT Hub にデバイスをいくつか追加した後、ロジック アプリをトリガーしたものをメールで確認します。 

## <a name="use-the-azure-cli"></a>Azure CLI の使用

Azure Portal を使う代わりに、Azure CLI を使って IoT Hub の手順を行うことができます。 詳しくは、[イベント サブスクリプションの作成](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription)と[IoT デバイスの作成](https://docs.microsoft.com/cli/azure/iot/device)に関する Azure CLI のページをご覧ください

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルでは、Azure サブスクリプションで料金が発生するリソースを使いました。 チュートリアルを試してテストを完了したら、残しておきたくないリソースを無効にするか削除します。 

アプリ ロジックでの作業を失いたくない場合は、削除ではなく無効にします。 

1. ロジック アプリに移動します。
2. **[概要]** ブレードで、**[削除]** または **[無効]** を選びます。 

各サブスクリプションで使うことができる無料 IoT Hub は 1 つです。 このチュートリアル用に無料のハブを作成した場合は、課金されないように削除する必要はありません。

1. IoT Hub に移動します。 
2. **[概要]** ブレードで **[削除]** を選びます。 

IoT Hub を残しておく場合でも、作成したイベント サブスクリプションを削除できます。 

1. IoT Hub で **[イベント グリッド]** を選びます。
2. 削除するイベント サブスクリプションを選びます。 
3. **[削除]**を選択します。 

## <a name="next-steps"></a>次の手順

「[Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md)」で詳細を確認します。

[Event Grid](overview.md) で他にできることについて確認します。


