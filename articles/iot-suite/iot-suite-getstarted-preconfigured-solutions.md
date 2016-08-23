<properties
	pageTitle="構成済みソリューションの使用 | Microsoft Azure"
	description="このチュートリアルに従って、Azure IoT Suite 事前構成済みソリューションのデプロイ方法について学習します。"
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# チュートリアル: 事前構成済みソリューションの使用

## はじめに

Azure IoT Suite の[事前構成済みソリューション][lnk-preconfigured-solutions]は、さまざまな Azure IoT サービスを組み合わせて、IoT のビジネス シナリオを満たすエンド ツー エンド ソリューションを提供します。"*リモート監視*" は、対象のデバイスに接続して監視する事前構成済みソリューションです。このソリューションを使用して、対象デバイスから流れてくるデータのストリームを分析し、そのデータのストリームに対してプロセスから自動的に応答が得られるようにすることで、ビジネスの成果を高めることができます。

このチュートリアルでは、リモート監視の事前構成済みソリューションをプロビジョニングする方法について説明します。また、リモート監視ソリューションの基本機能についても詳しく説明します。こうした機能の多くには、事前構成済みソリューションと一緒にデプロイされるソリューション ダッシュボードを通じてアクセスできます。

![Remote monitoring preconfigured solution dashboard][img-dashboard]

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

> [AZURE.NOTE]  アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk_free_trial]を参照してください。

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## ソリューション ダッシュボードの表示

ソリューションのダッシュボードでは、デプロイされたソリューションを管理できます。テレメトリの表示、デバイスの追加、ルールの構成などを行えます。

1.  プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** が表示されたら、**[起動]** をクリックし、新しいタブでリモート監視ソリューション ポータルを開きます。

    ![Launch the preconfigured solution][img-launch-solution]

2.  既定では、ソリューション ポータルに*ソリューション ダッシュボード*が表示されます。左側のメニューを使用して、他のビューを選択できます。

    ![Remote monitoring preconfigured solution dashboard][img-dashboard]

ダッシュボードには次の情報が表示されます。

- マップには、ソリューションに接続されている各デバイスの場所が表示されます。初めてサンプルを実行すると、シミュレートされた 4 つのデバイスがあります。シミュレートされたデバイスは、Azure WebJobs として実装されています。ソリューションは Bing Maps API を使用して、マップに情報をプロットします。
- **[テレメトリ履歴]** パネルには、選択したデバイスからの湿度と温度のテレメトリがほぼリアルタイムでプロットされ、最大値、最小値、平均湿度などの集計データが表示されます。
- **[アラーム履歴]** パネルには、テレメトリ値がしきい値を超えたときの最新のアラーム イベントが表示されます。構成済みソリューションによって作成された例に加えて、独自のアラームを定義することもできます。

## デバイス一覧の表示

デバイス一覧には、ソリューションの登録されたすべてのデバイスが表示されます。デバイス メタデータを表示、編集したり、デバイスを追加または取り外したり、コマンドをデバイスに送信したりできます。

1.  左側のメニューで **[デバイス]** をクリックし、このソリューションの*デバイス一覧*を表示します。

    ![Device list in dashboard][img-devicelist]

2.  デバイス一覧には、プロビジョニング プロセスで作成された 4 つのシミュレートされたデバイスが表示されています。

3.  デバイス一覧で任意のデバイスをクリックすると、デバイスの詳細が表示されます。

    ![Device details in dashboard][img-devicedetails]

**[デバイスの詳細]** パネルには、3 つのセクションが含まれています。

- **[アクション]** セクションには、デバイスに対して実行できるアクションが一覧表示されます。デバイスを無効にした場合、そのデバイスはテレメトリの送信やコマンドの受信ができなくなります。デバイスを無効にした場合は、再度有効にすることができます。テレメトリ値がしきい値を超えた場合にアラームをトリガーする、デバイスに関連付けられたルールを追加することができます。デバイスにコマンドを送信することもできます。デバイスは初めてソリューションに接続したときに、応答できるコマンドをソリューションに伝えます。
- **[デバイスのプロパティ]** セクションには、デバイス メタデータが一覧表示されます。このメタデータにはデバイス自体から生成されたもの (製造元など) と、ソリューションによって生成されたもの (作成日時など) があります。ここからデバイスのメタデータを編集できます。
- **[認証キー]** セクションには、デバイスがソリューションで認証に使用できるキーが一覧表示されます。

## デバイスへのコマンドの送信

[デバイスの詳細] ペインには、特定のデバイスがサポートしているすべてのコマンドが表示されます。また、デバイスにコマンドを送信することもできます。デバイスは初めて起動したときに、ソリューションに対してサポートしているコマンドに関する情報を送信します。

1.  選択したデバイスの [デバイスの詳細] ペインで **[コマンド]** をクリックします。

    ![Device commands in dashboard][img-devicecommands]

2.  コマンドの一覧から **[PingDevice]** を選択します。

3.  **[コマンドの送信]** をクリックします。

4.  [コマンドの履歴] にコマンドの状態が表示されます。

    ![Command status in dashboard][img-pingcommand]

ソリューションは、送信した各コマンドの状態を追跡します。最初は結果が **[保留中]** になっています。コマンドを実行したことがデバイスから報告されると、結果が **[成功]** に設定されます。

## 新しいシミュレートされたデバイスの追加

事前構成済みソリューションをデプロイすると、4 つのサンプル デバイスが自動的にプロビジョニングされ、デバイス一覧に表示されます。これらは "*シミュレーション デバイス* "であり、Azure WebJob で実行されています。シミュレーション デバイスを使用することで、事前構成済みソリューションを簡単にテストすることができます。実際に物理デバイスをデプロイする必要はありません。実際のデバイスをソリューションに接続する方法については、[リモート監視の事前構成済みソリューションへのデバイスの接続][lnk-connect-rm]に関するチュートリアルを参照してください。

次の手順は、シミュレートされたデバイスをソリューションに追加する方法を示しています。

1.  デバイス一覧に戻ります。

2.  左下隅の **[+ デバイスの追加]** をクリックし、デバイスを追加します。

    ![Add a device to the preconfigured solution][img-adddevice]

3.  **[シミュレートされたデバイス]** タイルで **[新規追加]** をクリックします。

    ![Set new device details in dashboard][img-addnew]
    
    新しいシミュレートされたデバイスを作成することに加えて、**カスタム デバイス**を作成することを選択した場合、物理デバイスを追加することもできます。ソリューションへの物理デバイスの接続に関する詳細については、[IoT Suite リモート監視構成済みソリューションへのデバイスの接続][lnk-connect-rm]に関する記事を参照してください。

4.  **[自分で自分のデバイス ID を定義する]** を選択し、**mydevice\_01** など一意のデバイス ID 名を追加します。

5.  **[作成]** をクリックします。

    ![Save a new device][img-definedevice]

6. 「**新しいシミュレートされたデバイスの追加**」の手順 3. で **[完了]** をクリックすると、デバイス一覧に戻ります。

7. デバイス一覧でデバイスが **[実行中]** であることを確認できます。

    ![View new device in device list][img-runningnew]

8. 新しいデバイスからシミュレートされたテレメトリをダッシュボードに表示することもできます。

    ![View telemetry from new device][img-runningnew-2]

## デバイス メタデータの編集

デバイスは、初めてソリューションに接続するとき、そのメタデータをソリューションに送信します。ソリューション ダッシュボードを通じてデバイス メタデータを編集すると、新しいメタデータ値がデバイスに送信され、ソリューションの DocumentDB データベースに新しい値が格納されます。詳細については、「[デバイス ID レジストリと DocumentDB][lnk-devicemetadata]」を参照してください。

1.  デバイス一覧に戻ります。

2.  **[Devices List (デバイスの一覧)]** で新しいデバイスを選択し、**[編集]** をクリックして、**[デバイスのプロパティ]** を編集します。

    ![Edit device metadata][img-editdevice]

3. 下へスクロールし、緯度と経度の値を変更します。**[変更をデバイス レジストリに保存]** をクリックします。

    ![Edit device metadata][img-editdevice2]

4. ダッシュボードに戻ると、マップのデバイスの場所が変更されています。

    ![Edit device metadata][img-editdevice3]

## 新しいデバイスのルールの追加

追加した新しいデバイスにはルールがありません。このセクションでは、新しいデバイスによって報告された温度が 47 度を超えるとアラームをトリガーするルールを追加します。始める前に、ダッシュボードで新しいデバイスのテレメトリ履歴に、デバイスの温度が 45 度を超えたことがないと示されていることに注意します。

1.  デバイス一覧に戻ります。

2.  **[Devices List (デバイスの一覧)]** で新しいデバイスを選択し、**[Add rule (ルールの追加)]** をクリックして、デバイスのルールを追加します。

3. データ フィールドとして **[温度]** を使用し、温度が 47 度を超えたときの出力として **[AlarmTemp]** を使用するルールを作成します。

    ![Add a device rule][img-adddevicerule]

4. **[ルールを保存して表示]** をクリックして変更を保存します。

5.  新しいデバイスの [デバイスの詳細] ペインで **[コマンド]** をクリックします。

    ![Add a device rule][img-adddevicerule2]

6.  コマンド リストから **[ChangeSetPointTemp]** を選択し、**[SetPointTemp]** を 45 に設定します。**[コマンドの送信]** をクリックします。

    ![Add a device rule][img-adddevicerule3]

7.  ソリューションのダッシュボードに戻ります。しばらくして、新しいデバイスによって報告される温度が 47 度のしきい値を超えると、**[アラーム履歴]** ペインに新しいエントリが表示されます。

    ![Add a device rule][img-adddevicerule4]

8. ダッシュボードの **[ルール]** ページですべてのルールを確認して編集できます。

    ![List device rules][img-rules]

9. ダッシュボードの **[アクション]** ページで、ルールへの対応として実行可能なすべてのアクションを確認して編集できます。

    ![List device actions][img-actions]

> [AZURE.NOTE] ルールへの対応として電子メール メッセージや SMS を送信したり、[ロジック アプリ][lnk-logic-apps]経由で基幹業務システムに統合したりするアクションを定義できます。詳細については、「[ロジック アプリを Azure IoT Suite リモート監視構成済みソリューションに接続する][lnk-logicapptutorial]」を参照してください。

## その他の機能

ソリューション ポータルを使用すると、モデル番号などの具体的な特性でデバイスを検索できます。

![Search for a device][img-search]

デバイスを無効にし、無効になったデバイスを削除することができます。

![Disable and remove a device][img-disable]

## バックグラウンド処理

事前構成済みソリューションをデプロイすると、デプロイ プロセスにより、選択した Azure サブスクリプションに複数のリソースが作成されます。これらのリソースを Azure [ポータル][lnk-portal]で表示できます。デプロイ プロセスにより、事前構成済みソリューション用に選択した名前に基づいた名前で、**リソース グループ**が作成されます。

![Preconfigured solution in the Azure portal][img-portal]

各リソースの設定を表示するには、リソース グループ内のリソースのリストでそれを選択します。

また、事前構成済みソリューションのソース コードを表示することもできます。リモート監視の事前構成済みソリューションのソース コードは、[azure-iot-remote-monitoring][lnk-rmgithub] GitHub リポジトリにあります。

- **DeviceAdministration** フォルダーには、ダッシュボードのソース コードが格納されています。
- **Simulator** フォルダーには、シミュレートされたデバイスのソース コードが格納されています。
- **EventProcessor** フォルダーには、受信テレメトリを処理するバックエンド プロセスのソース コードが格納されています。

完了したら、[azureiotsuite.com][lnk-azureiotsuite] で事前構成済みソリューションを Azure サブスクリプションから削除できます。このサイトでは、構成済みのソリューションを作成したときにプロビジョニングされていたすべてのリソースを簡単に削除することができます。

> [AZURE.NOTE] 事前構成済みソリューションに関連するすべてのものを確実に削除するには、ポータルでリソース グループを単に削除するのではなく、[azureiotsuite.com][lnk-azureiotsuite] サイトで事前構成済みソリューションを削除してください。

## 次のステップ

これで、事前構成済みの実際のソリューションをデプロイできました。引き続き IoT Suite の概要について学習するには、次の記事を参照してください。

- [リモート監視の事前構成済みソリューションのチュートリアル][lnk-rm-walkthrough]
- [デバイスをリモート監視の事前構成済みソリューションに接続する][lnk-connect-rm]
- [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md

<!---HONumber=AcomDC_0817_2016-->