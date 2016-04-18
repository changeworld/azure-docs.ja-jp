<properties 
   pageTitle="センサーまたはその他のシステムから受信したデータをユーザーに通知する | Microsoft Azure"
   description="Event Hubs を使用してセンサーのデータをユーザーに通知する方法について説明します。"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/08/2016"
   ms.author="spyros;sethm" />

# センサーまたはその他のシステムから受信したデータをユーザーに通知する

リアルタイムでデータを監視、またはスケジュールに従ってレポートを作成するアプリケーションがあるとします。そのようなリアルタイムのグラフまたはレポートが表示された Web サイトを見ると、アクションを必要とする何かが表示されている場合があります。Web サイトの確認を忘れずに行うことでアクションが必要な状況を把握するのでなく、そのような状況に対してアラートが発生するようにする必要がある場合はどうすればよいのでしょうか。 温室に植物育成ライトが設置されていて、そのライトが消えたかどうかをすぐに判断する必要があるとします。そのためには、温室内に光センサーを取り付けて、ライトが消えた場合に電子メールが届くようにするという方法があります。

![][1]

もう 1 つのシナリオとして、ペット宿泊施設を運営していて、供給品の在庫レベルが低下した場合にアラートが発生するようにする必要があるとします。たとえば、倉庫にあるドッグフードの在庫が重大レベルまで減少した場合に ERP システムからテキスト メッセージが届くようにする方法が考えられます。

![][2]

問題は、静的なレポートを確認するための時間ができたときではなく、特定の条件が満たされたときに、重要な情報をいかに取得するかということです。[Azure Event Hub][] または [Azure IoT Hub][] を使用して、[Dynamics AX][] などのデバイスまたはエンタープライズ アプリケーションからデータを受信する場合、それらのデータを処理する方法としていくつかのオプションがあります。受信したデータは Web サイトで確認できるほか、分析し、格納することができ、さらに、それらのデータを使用して適切な作業を行うコマンドをトリガーすることもできます。それには、[Azure Websites][]、[SQL Azure][]、[HDInsight][]、[Cortana Intelligence Suite][]、[IoT Suite][]、[Logic Apps][]、[Azure Notification Hubs][] など強力なツールを使用することができます。ただし、そのデータを誰かに最小限のオーバーヘッドで送信することが最も重要である場合があります。ほんの少しのコードでそれを行う方法を示すために、[AppToNotifyUsers][] という新しいサンプルを用意しました。オプションには、電子メール (SMTP)、SMS、電話などがあります。

## アプリケーション構造

アプリケーションは C# で記述されています。このサンプルの readme ファイルには、アプリケーションを変更、ビルド、および発行するために必要なすべての情報が含まれています。次のセクションでは、アプリケーションの動作の概要を説明します。

Azure Event Hub または IoT Hub にプッシュする重大なイベントがあるという想定で説明を始めます。ハブへのアクセス権を有し、接続文字列がわかっている限り、どのハブも使用できます。

Event Hub または IoT Hub をまだお持ちでない場合は、[Connect The Dots](https://github.com/Azure/connectthedots) プロジェクトにある手順に従って、Arduino シールドおよび Raspberry Pi でテスト ベッドを簡単にセットアップすることができます。Arduino シールドの光センサーは Pi を通して光のレベルを [Azure Event Hub][] (**ehdevices**) に送信します。[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ジョブは受信した光のレベルが重大レベルを下回っている場合、2 番目のイベント ハブ (**ehalerts**) に通知をプッシュします。

**AppToNotify** は起動すると、構成ファイル (App.config) を読み取って、アラートを受信する Event Hub の URL と資格情報を取得します。次に、その Event Hub を継続的に監視して、それを介して渡されたメッセージがないかを確認するプロセスを生成します。Event Hub または IoT Hub の URL および有効な資格情報にアクセスできる限り、この Event Hubs リーダー コードは何が入ってきたかを継続的に読み取ることができます。アプリケーションはまた、起動中に、使用されるメッセージング サービス (電子メール、SMS、電話) の URL と資格情報、送信者の名前とアドレス、および受信者のリストを読み取ります。

Event Hub モニターは、メッセージを検出すると、構成ファイル内に指定されている方法でメッセージを送信するプロセスをトリガーします。この場合、検出されたすべてのメッセージが送信されます。1 秒あたり 10 個のメッセージを受信する Event Hub を指すようにモニターを設定した場合、センダーは 1 秒あたり 10 個のメッセージを送信します (1 秒あたり 10 の電子メール、1 秒あたり 10 の SMS メッセージ、1 秒あたり 10 の電話)。そのため、必ず、センサーまたはアプリケーションからのすべての生データを受信する Event Hub ではなく、送信される必要があるアラートのみを受信する Event Hub を監視します。

## 適用性

このサンプルのコードでは、アプリケーションに追加する場合に適用できる、Event Hubs を監視する方法と、外部のメッセージング サービスを呼び出す方法を示すのみです。このソリューションは DIY、すなわち、開発者向けに限定した例となります。冗長性、フェールオーバー、障害発生時の再起動など、企業の要件には対応していません。より包括的な運用環境向けのソリューションについては、次を参照してください。

- [Azure Logic Apps](../app-service-logic/app-service-logic-connectors-list.md) サービスでコネクタまたはプッシュ通知を使用する。
- [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx) を使用する。ブログ「[Broadcast push notifications to millions of mobile devices using Azure Notification Hubs (Azure Notification Hubs を使用して何百万ものモバイル デバイスにプッシュ通知をブロードキャストする)](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs)」を参照。 

## 次のステップ

受信者に電子メールまたはテキスト メッセージを送信したり、受信者を呼び出したりして、Event Hub または IoT Hub で受信したデータを中継するシンプルな通知サービスを作成することは簡単です。これらのハブで受信したデータに基づいてユーザーに通知するソリューションをデプロイするには、[AppToNotifyUsers][] にアクセスしてください。

これらのハブの詳細については、次の記事を参照してください。

- [Azure Event Hubs]
- [Azure IoT Hub]
- [Event Hubs の使用]
- [Event Hubs を使用する完全なサンプル アプリケーション]
- Service Bus キューを使用する[キューに格納されたメッセージング ソリューション]

これらのハブで受信したデータに基づいてユーザーに通知するソリューションをデプロイするには、

- [AppToNotifyUsers にアクセスしてください。][]

[Event Hubs の使用]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT Hub]: https://azure.microsoft.com/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/services/event-hubs/
[Azure Event Hub]: https://azure.microsoft.com/services/event-hubs/
[Event Hubs を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[キューに格納されたメッセージング ソリューション]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[AppToNotifyUsers にアクセスしてください。]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Azure Websites]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Intelligence Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Logic Apps]: https://azure.microsoft.com/services/app-service/logic/
[Azure Notification Hubs]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_0406_2016-->