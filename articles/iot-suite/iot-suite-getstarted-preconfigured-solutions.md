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
     ms.date="12/03/2015"
     ms.author="dobett"/>

# チュートリアル: IoT 事前構成済みソリューションの使用

## はじめに

Azure IoT Suite の[事前構成済みソリューション][lnk-preconfigured-solutions]は、さまざまな Azure IoT サービスを組み合わせて、IoT のビジネス シナリオを満たすエンド ツー エンド ソリューションを提供します。

このチュートリアルでは、*リモート監視*の事前構成済みソリューションをプロビジョニングする方法について説明します。また、リモート監視の事前構成済みソリューションの基本機能についても詳しく説明します。

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要です。

> [AZURE.NOTE]  アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk_free_trial]を参照してください。

## リモート監視の事前構成済みソリューションのプロビジョニング

1.  Azure アカウント資格情報を使用して [azureiotsuite.com][lnk-azureiotsuite] にログオンし、**[+]** をクリックして新しいソリューションを作成します。

2.  **[リモート監視]** タイルで **[選択]** タイルを選択します。

3.  リモート監視の事前構成済みソリューションの **[ソリューション名]** を入力します。

4.  このソリューションをプロビジョニングするために使用する **[リージョン]** と **[サブスクリプション]** を選択します。

5.  **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。通常、プロセスの実行までに数分かかります。

## リモート監視ソリューションのダッシュボードの表示

1.  プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** が表示されたら、**[起動]** をクリックし、新しいタブでリモート監視ソリューション ポータルを開きます。

    ![][img-launch-solution]

2.  既定では、ソリューション ポータルに*ソリューション ダッシュボード*が表示されます。左側のメニューを使用して、他のビューを選択できます。

    ![][img-dashboard]

## ソリューションのデバイス一覧の表示

1.  左側のメニューで **[デバイス]** をクリックし、このソリューションの*デバイス一覧*を表示します。

    ![][img-devicelist]

2.  デバイス一覧には、プロビジョニング プロセスで作成された 4 つのシミュレートされたデバイスが表示されています。

3.  デバイス一覧で任意のデバイスをクリックすると、デバイスの詳細が表示されます。

    ![][img-devicedetails]

## デバイスへのコマンドの送信

1.  選択したデバイスの [デバイスの詳細] ペインで **[コマンド]** をクリックします。

    ![][img-devicecommands]

2.  コマンドの一覧から **[PingDevice]** を選択します。

3.  **[コマンドの送信]** をクリックします。

4.  [コマンドの履歴] にコマンドの状態が表示されます。

    ![][img-pingcommand]

## 新しいシミュレートされたデバイスの追加

1.  デバイス一覧に戻ります。

2.  左下隅の **[+ デバイスの追加]** をクリックし、新しいデバイスを追加します。

    ![][img-adddevice]

3.  **[シミュレートされたデバイス]** タイルで **[新規追加]** をクリックします。

    ![][img-addnew]

4.  **[自分で自分のデバイス ID を定義する]** を選択し、**mydevice\_01** など一意のデバイス ID 名を追加します。

5.  **[作成]** をクリックします。

    ![][img-definedevice]

6. **[シミュレートされたデバイスの追加]** の手順 3. で **[完了]** をクリックすると、デバイス一覧に戻ります。

7.  デバイス一覧でデバイスが **[実行中]** であることを確認できます。

    ![][img-runningnew]

## ソリューションの規則の表示と編集

1.  ソリューション ダッシュボードに戻り、**[アラーム履歴]** タイルを表示します。

    ![][img-alarmhistory]

2.  これらのアラームは、規則 **AlarmTemp** によってトリガーされます。

3.  左側のメニューで **[規則]** をクリックすると、このソリューションの規則が表示されます。

    ![][img-rules]

4.  この構成済みソリューションでは、2 つの規則がプロビジョニングされます。

5.  規則の一覧で規則 **Temperature** をクリックすると、規則のプロパティが表示されます。

6.  規則のプロパティ ペインで **[編集]** をクリックします。

    ![][img-displayrule]

7.  **[しきい値]** を 30 に変更して、その他のすべてのプロパティは同じままにします。

8.  **[規則の保存と表示]** をクリックします。

    ![][img-editrule]

9.  **ソリューション ダッシュボード**の **[アラーム履歴]** テーブルに戻り、規則を更新したことで動作が変更されていることを確認します。

    ![][img-newhistory]
    
完了したら、[azureiotsuite.com][lnk-azureiotsuite] サイト上の Azure サブスクリプションから事前構成済みソリューションを削除してください。こうすることで、事前構成済みソリューションの作成時にプロビジョニングしたすべてのリソースを簡単に削除できます。

## 次のステップ

これで、機能する事前構成済みソリューションを構築しましたので、次のシナリオに進むことができます。

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
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0211_2016-->