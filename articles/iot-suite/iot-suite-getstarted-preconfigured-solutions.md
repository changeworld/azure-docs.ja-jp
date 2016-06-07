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
     ms.date="05/25/2016"
     ms.author="dobett"/>

# チュートリアル: 事前構成済みソリューションの使用

## はじめに

Azure IoT Suite の[事前構成済みソリューション][lnk-preconfigured-solutions]は、さまざまな Azure IoT サービスを組み合わせて、IoT のビジネス シナリオを満たすエンド ツー エンド ソリューションを提供します。

このチュートリアルでは、*リモート監視*の事前構成済みソリューションをプロビジョニングする方法について説明します。また、リモート監視の事前構成済みソリューションの基本機能についても詳しく説明します。

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要です。

> [AZURE.NOTE]  アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk_free_trial]を参照してください。

## リモート監視の事前構成済みソリューションのプロビジョニング

1.  Azure アカウント資格情報を使用して [azureiotsuite.com][lnk-azureiotsuite] にログオンし、**[+]** をクリックして新しいソリューションを作成します。

    > [AZURE.NOTE] ソリューションのプロビジョニングに必要なアクセス許可でお困りの場合は、詳細について「[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]」を参照してください。

2.  **[リモート監視]** タイルで **[選択]** タイルを選択します。

3.  リモート監視の事前構成済みソリューションの **[ソリューション名]** を入力します。

4.  このソリューションをプロビジョニングするために使用する **[リージョン]** と **[サブスクリプション]** を選択します。

5.  **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。通常、プロセスの実行までに数分かかります。

## プロビジョニング プロセスが完了するまで待機します。

1. **プロビジョニング**の状態を表示する、目的のソリューションのタイルをクリックします。
 
2. Azure サービスが Azure サブスクリプションにデプロイされたら、**プロビジョニングの状態**を確認します。

3. プロビジョニングが完了すると、ステータスが**準備完了**に変わります。

4. タイルをクリックし、右側のウィンドウでソリューションの詳細を確認します。

> [AZURE.NOTE] 構成済みソリューションのデプロイの問題が発生している場合は、「[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]」と [FAQ][lnk-faq] を参照してください。それでも問題が解決しない場合は、[ポータル][lnk-portal]でサービス チケットを作成してください。

ソリューションの一覧に予測どおりに表示されない詳細がありますか? [ユーザーの声](https://feedback.azure.com/forums/321918-azure-iot)で機能に関する提案をお寄せください。

## リモート監視ソリューションのダッシュボードの表示

ソリューションのダッシュボードでは、デプロイされたソリューションを管理できます。テレメトリの表示、デバイスの追加、ルールの構成などを行えます。

1.  プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** が表示されたら、**[起動]** をクリックし、新しいタブでリモート監視ソリューション ポータルを開きます。

    ![][img-launch-solution]

2.  既定では、ソリューション ポータルに*ソリューション ダッシュボード*が表示されます。左側のメニューを使用して、他のビューを選択できます。

    ![][img-dashboard]

ダッシュボードには次の情報が表示されます。

- マップには、ソリューションに接続されている各デバイスの場所が表示されます。初めてサンプルを実行すると、シミュレートされた 4 つのデバイスがあります。シミュレートされたデバイスは、Azure WebJobs として実装されています。ソリューションは Bing Maps API を使用して、マップに情報をプロットします。
- **[テレメトリ履歴]** パネルは、ほぼリアルタイムで、選択したデバイスから湿度と温度のテレメトリをプロットして、最大、最小、および平均湿度などの集計データを表示します。
- **[アラーム履歴]** パネルは、テレメトリ値がしきい値を超えたときに最新のアラーム イベントを表示します。構成済みソリューションによって作成された例に加えて、独自のアラームを定義することもできます。

## ソリューションのデバイス一覧の表示

デバイス一覧には、ソリューションの登録されたすべてのデバイスが表示されます。デバイス メタデータを表示、編集したり、デバイスを追加または取り外したり、コマンドをデバイスに送信したりできます。

1.  左側のメニューで **[デバイス]** をクリックし、このソリューションの*デバイス一覧*を表示します。

    ![][img-devicelist]

2.  デバイス一覧には、プロビジョニング プロセスで作成された 4 つのシミュレートされたデバイスが表示されています。

3.  デバイス一覧で任意のデバイスをクリックすると、デバイスの詳細が表示されます。

    ![][img-devicedetails]

**[デバイスの詳細]** パネルには、3 つのセクションが含まれています。

- **[アクション]** セクションには、デバイスに対して実行できるアクションが一覧表示されます。デバイスを無効にした場合、そのデバイスのテレメトリの送信やコマンドの受信ができなくなります。デバイスを無効にした場合は、再度有効にすることができます。テレメトリ値がしきい値を超えた場合にアラームをトリガーする、デバイスに関連付けられたルールを追加することができます。デバイスにコマンドを送信することもできます。デバイスは初めてソリューションに接続したときに、それが応答できるコマンドをソリューションに伝えます。
- **[デバイスのプロパティ]** セクションには、デバイス メタデータが一覧表示されます。このメタデータにはデバイス自体から生成されたもの (製造元など) と、ソリューションによって生成されたもの (作成日時など) があります。ここからデバイスのメタデータを編集できます。
- **[認証キー]** セクションには、デバイスがソリューションで認証に使用できるキーが一覧表示されます。

## デバイスへのコマンドの送信

デバイスの詳細ペインには、特定のデバイスがサポートしているすべてのコマンドが表示されます。また、デバイスにコマンドを送信することもできます。デバイスは初めて起動したときに、ソリューションに対してサポートしているコマンドに関する情報を送信します。

1.  選択したデバイスの [デバイスの詳細] ペインで **[コマンド]** をクリックします。

    ![][img-devicecommands]

2.  コマンドの一覧から **[PingDevice]** を選択します。

3.  **[コマンドの送信]** をクリックします。

4.  [コマンドの履歴] にコマンドの状態が表示されます。

    ![][img-pingcommand]

ソリューションは、送信した各コマンドの状態を追跡します。最初の結果は **[保留中]** です。デバイスがコマンドを正常に実行したことを報告すると、結果が **[成功]** に設定されます。

## 新しいシミュレートされたデバイスの追加

1.  デバイス一覧に戻ります。

2.  左下隅の **[+ デバイスの追加]** をクリックし、新しいデバイスを追加します。

    ![][img-adddevice]

3.  **[シミュレートされたデバイス]** タイルで **[新規追加]** をクリックします。

    ![][img-addnew]
    
    新しいシミュレートされたデバイスを作成することに加えて、**[カスタム デバイス]** を作成することを選択した場合、物理デバイスを追加することもできます。この詳細については、「[デバイスを IoT Suite リモート監視構成済みソリューションに接続する][lnk-connecting-devices]」を参照してください。

4.  **[自分で自分のデバイス ID を定義する]** を選択し、**mydevice\_01** など一意のデバイス ID 名を追加します。

5.  **[作成]** をクリックします。

    ![][img-definedevice]

6. **[シミュレートされたデバイスの追加]** の手順 3. で **[完了]** をクリックすると、デバイス一覧に戻ります。

7. デバイス一覧でデバイスが **[実行中]** であることを確認できます。

    ![][img-runningnew]

8. 新しいデバイスからシミュレートされたテレメトリをダッシュボードに表示することもできます。

    ![][img-runningnew-2]

## デバイス メタデータの編集

1.  デバイス一覧に戻ります。

2.  **[デバイスの一覧]** で新しいデバイスを選択し、**[編集]** をクリックして、**[デバイスのプロパティ]** を編集します。

    ![][img-editdevice]

3. 下へスクロールし、緯度と経度の値を変更します。**[デバイスのレジストリに変更を保存]** をクリックします。

    ![][img-editdevice2]

4. ダッシュボードに戻ると、マップのデバイスの場所が変更されています。

    ![][img-editdevice3]

## 新しいデバイスのルールの追加

追加した新しいデバイスにはルールがありません。このセクションでは、新しいデバイスによって報告された温度が 47 度を超えるとアラームをトリガーするルールを追加します。始める前に、ダッシュボードで新しいデバイスのテレメトリ履歴に、デバイスの温度が 45 度を超えたことがないと示されていることに注意します。

1.  デバイス一覧に戻ります。

2.  **[デバイスの一覧]** で新しいデバイスを選択し、**[ルールの追加]** をクリックして、デバイスの新しいルールを追加します。

3. データ フィールドとして **[温度]** を使用し、温度が 47 度を超えたときの出力として **[AlarmTemp]** を使用するルールを作成します。

    ![][img-adddevicerule]

4. **[ルールの保存と表示]** をクリックして変更を保存します。

5.  新しいデバイスの [デバイスの詳細] ペインで **[コマンド]** をクリックします。

    ![][img-adddevicerule2]

6.  コマンド リストから **[ChangeSetPointTemp]** を選択し、**[SetPointTemp]** を 45 に設定します。**[コマンドの送信]** をクリックします。

    ![][img-adddevicerule3]

7.  ソリューションのダッシュボードに戻ります。しばらくして、新しいデバイスによって報告される温度が 47 度のしきい値を超えると、**[アラーム履歴]** ペインに新しいエントリが表示されます。

    ![][img-adddevicerule4]

8. ダッシュボードの **[ルール]** ページですべてのルールを確認して編集できます。

    ![][img-rules]

9. ダッシュボードの **[アクション]** ページで、ルールへの対応として実行可能なすべてのアクションを確認して、編集できます。

    ![][img-actions]

> [AZURE.NOTE] ルールへの対応として電子メール メッセージや SMS を送信したり、[ロジック アプリ][lnk-logic-apps]経由で基幹業務システムに統合したりするアクションを定義できます。

## バックグラウンド処理

事前構成済みソリューションをデプロイすると、デプロイ プロセスにより、選択した Azure サブスクリプションに複数のリソースが作成されます。これらのリソースを Azure [ポータル][lnk-portal]で表示できます。デプロイ プロセスにより、事前構成済みソリューションに選択した名前に基づいた名前で、**リソース グループ**が作成されます。

![][img-portal]

各リソースの設定を表示するには、リソース グループ内のリソースのリストでそれを選択します。上のスクリーン ショットには、事前構成済みソリューションで使用される IoT hub の設定を示しています。

また、事前構成済みソリューションのソース コードを表示することもできます。リモート監視の事前構成済みソリューションのソース コードは、[azure-iot-remote-monitoring][lnk-rmgithub] にあります。

- **DeviceAdministration** フォルダーには、ダッシュボードのソース コードが格納されています。
- **Simulator** フォルダーには、シミュレートされたデバイスのソース コードが格納されています。
- **EventProcessor** フォルダーには、受信テレメトリを処理するバックエンド プロセスのソース コードが格納されています。

完了したら、[azureiotsuite.com][lnk-azureiotsuite] サイト上の Azure サブスクリプションから事前構成済みソリューションを削除できます。こうすることで、事前構成済みソリューションの作成時にプロビジョニングされたすべてのリソースを簡単に削除できます。

> [AZURE.NOTE] 事前構成済みソリューションに関連したすべてのものを確実に削除するには、ポータルでリソース グループを単に削除するだけではなく、[azureiotsuite.com][lnk-azureiotsuite] ポータルからそれを削除します。

## 次のステップ

これで、機能する事前構成済みソリューションを構築しましたので、次のチュートリアルに進むことができます。

-   [事前構成済みソリューションのカスタマイズに関するガイダンス][lnk-customize]
-   [予測的なメンテナンスの構成済みソリューションの概要][lnk-predictive]

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

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: iot-suite-faq.md

<!---HONumber=AcomDC_0601_2016-->