---
title: "構成済みソリューションの使用 | Microsoft Docs"
description: "このチュートリアルに従って、Azure IoT Suite 事前構成済みソリューションのデプロイ方法について学習します。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>チュートリアル: 事前構成済みソリューションの使用
## <a name="introduction"></a>はじめに
Azure IoT Suite の[事前構成済みソリューション][lnk-preconfigured-solutions]は、さまざまな Azure IoT サービスを組み合わせて、IoT のビジネス シナリオを満たすエンド ツー エンド ソリューションを提供します。 " *リモート監視* " は、対象のデバイスに接続して監視する事前構成済みソリューションです。 このソリューションを使用して、対象デバイスから流れてくるデータのストリームを分析し、そのデータのストリームに対してプロセスから自動的に応答が得られるようにすることで、ビジネスの成果を高めることができます。

このチュートリアルでは、リモート監視の事前構成済みソリューションをプロビジョニングする方法について説明します。 また、事前構成済みソリューションの基本機能についても詳しく説明します。 こうした機能の多くには、事前構成済みソリューションの一部としてデプロイされるソリューション ダッシュボードからアクセスできます。

![Remote monitoring preconfigured solution dashboard][img-dashboard]

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

> [!NOTE]
> アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk_free_trial]をご覧ください。
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>ソリューション ダッシュボードの表示
ソリューションのダッシュボードでは、デプロイされたソリューションを管理できます。 テレメトリの表示、デバイスの追加、ルールの構成などを行えます。

1. プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** が表示されたら、**[起動]** をクリックし、新しいタブでリモート監視ソリューション ポータルを開きます。
   
   ![Launch the preconfigured solution][img-launch-solution]
2. 既定では、ソリューション ポータルに *ソリューション ダッシュボード*が表示されます。 左側のメニューを使用して、他のビューを選択できます。
   
   ![Remote monitoring preconfigured solution dashboard][img-dashboard]

ダッシュボードには次の情報が表示されます。

* マップには、ソリューションに接続されている各デバイスの場所が表示されます。 初めてサンプルを実行すると、シミュレートされた&4; つのデバイスがあります。 シミュレートされたデバイスは、Azure WebJobs として実装されています。ソリューションは Bing Maps API を使用して、マップに情報をプロットします。
* **[テレメトリ履歴]** パネルには、選択したデバイスからの湿度と温度のテレメトリがほぼリアルタイムでプロットされ、最大値、最小値、平均湿度などの集計データが表示されます。
* **[アラーム履歴]** パネルには、テレメトリ値がしきい値を超えたときの最新のアラーム イベントが表示されます。 構成済みソリューションによって作成された例に加えて、独自のアラームを定義することもできます。
* **[ジョブ]** パネルには、スケジュールされたジョブに関する情報が表示されます。 自分のジョブは、**[管理ジョブ]** ページでスケジュールできます。

## <a name="view-the-device-list"></a>デバイス一覧の表示
"*デバイス一覧*" には、ソリューションの登録されたすべてのデバイスが表示されます。 デバイス一覧で、デバイス メタデータの表示と編集、デバイスの追加または取り外し、デバイスのメソッドの呼び出しを行うことができます。

1. このソリューションのデバイス一覧を表示するには、左側のメニューで **[デバイス]** をクリックします。
   
   ![Device list in dashboard][img-devicelist]
2. デバイス一覧には、まず、プロビジョニング プロセスで作成された&4; つのシミュレートされたデバイスが表示されます。 ソリューションには、シミュレートされたデバイスや物理デバイスを追加できます。
3. デバイス一覧に表示される情報をカスタマイズするには、**[列エディター]** をクリックします。 報告されたプロパティとタグの値を表示する列を追加したり削除したりすることができます。 また、列を並べ替えたり、列の名前を変更したりすることもできます。
   
   ![ダッシュボードの列エディター][img-columneditor]
4. デバイスの詳細を表示するには、デバイス一覧でデバイスをクリックします。
   
   ![Device details in dashboard][img-devicedetails]

**[デバイスの詳細]** パネルには、次の&6; つのセクションが含まれています。

* デバイス アイコンのカスタマイズ、デバイスの無効化、ルールの追加、メソッドの呼び出し、またはコマンドの送信を行うためのリンクのコレクション。 コマンド (device-to-cloud メッセージ) とメソッド (直接メソッド) の比較については、「[cloud-to-device 通信に関するガイダンス][lnk-c2d-guidance]」を参照してください。
* **[デバイス ツイン] - [タグ]** セクションでは、デバイスのタグ値を編集することができます。 デバイス一覧でタグ値を表示し、タグ値を使用してデバイス一覧をフィルター処理することができます。
* **[デバイス ツイン] - [必要なプロパティ]** セクションでは、デバイスに送信されるプロパティ値を設定することができます。
* **[デバイス ツイン] - [報告されたプロパティ]** セクションでは、デバイスから送信されたプロパティ値が表示されます。
* **[デバイスのプロパティ]** セクションでは、デバイス ID や認証キーなど、ID レジストリの情報が表示されます。
* **[最近のジョブ]** セクションでは、このデバイスを最近対象にしたジョブに関する情報が表示されます。

## <a name="customize-the-device-icon"></a>デバイス アイコンのカスタマイズ

デバイス一覧に表示されるデバイス アイコンは、**[デバイスの詳細]** パネルで、次のようにカスタマイズできます。

1. デバイスの **[イメージの編集]** パネルを開くには、鉛筆アイコンをクリックします。
   
   ![デバイスのイメージ エディターを開く][img-startimageedit]
2. 新しいイメージをアップロードするか、既存のイメージのいずれかを使用して、**[保存]** をクリックします。
   
   ![デバイスのイメージ エディターで編集する][img-imageedit]
3. 選択したイメージが、デバイスの **[アイコン]** 列に表示されるようになりました。

> [!NOTE]
> イメージは、BLOB ストレージに格納されます。 デバイス ツインのタグには、BLOB ストレージ内のイメージへのリンクが含まれています。
> 
> 

## <a name="invoke-a-method-on-a-device"></a>デバイスのメソッドの呼び出し
**[デバイスの詳細]** パネルで、デバイスのメソッドを呼び出すことができます。 デバイスは初めて起動したときに、サポートしているメソッドに関する情報をソリューションに送信します。

1. 選択したデバイスの **[デバイスの詳細]** パネルで、**[方法]** をクリックします。
   
   ![ダッシュボードでのデバイス メソッド][img-devicemethods]
2. メソッドの一覧で **[再起動]** を選択します。
3. **[メソッドの呼び出し]** をクリックします。
4. メソッドの履歴で、メソッドの呼び出しの状態を確認できます。
   
   ![ダッシュボードでのメソッドの状態][img-pingmethod]

ソリューションは、呼び出した各メソッドの状態を追跡します。 デバイスがメソッドを完了すると、メソッドの履歴テーブルに新しいエントリが表示されます。

いくつかのメソッドは、デバイスで非同期ジョブを起動します。 たとえば、**InitiateFirmwareUpdate** メソッドは、更新を実行するために非同期タスクを開始します。 デバイスは、ファームウェアの更新の進行状態について報告するために、報告されたプロパティを使用します。

## <a name="send-a-command-to-a-device"></a>デバイスへのコマンドの送信
**[デバイスの詳細]** パネルで、デバイスにコマンドを送信することができます。 デバイスは初めて起動したときに、ソリューションに対してサポートしているコマンドに関する情報を送信します。

1. 選択したデバイスの **[デバイスの詳細]** パネルで **[コマンド]** をクリックします。
   
   ![Device commands in dashboard][img-devicecommands]
2. コマンドの一覧から **[PingDevice]** を選択します。
3. **[コマンドの送信]** をクリックします。
4. [コマンドの履歴] にコマンドの状態が表示されます。
   
   ![Command status in dashboard][img-pingcommand]

ソリューションは、送信した各コマンドの状態を追跡します。 最初は結果が **[保留中]**になっています。 コマンドを実行したことがデバイスから報告されると、結果が **[成功]**に設定されます。

## <a name="add-a-new-simulated-device"></a>新しいシミュレートされたデバイスの追加
事前構成済みソリューションをデプロイすると、4 つのサンプル デバイスが自動的にプロビジョニングされ、デバイス一覧に表示されます。 これらは "*シミュレートされたデバイス*" であり、Azure WebJob で実行されています。 シミュレーション デバイスを使用することで、事前構成済みソリューションを簡単にテストすることができます。実際に物理デバイスをデプロイする必要はありません。 実際のデバイスをソリューションに接続する方法については、チュートリアル「[デバイスをリモート監視構成済みソリューションに接続する][lnk-connect-rm]」をご覧ください。

次の手順は、シミュレートされたデバイスをソリューションに追加する方法を示しています。

1. デバイス一覧に戻ります。
2. デバイスを追加するには、左下隅の **[+ デバイスの追加]** をクリックします。
   
   ![Add a device to the preconfigured solution][img-adddevice]
3. **[Simulated Device (シミュレートされたデバイス)]** タイルで **[Add New (新規追加)]** をクリックします。
   
   ![Set new device details in dashboard][img-addnew]
   
   新しいシミュレートされたデバイスを作成することに加えて、 **カスタム デバイス**を作成することを選択した場合、物理デバイスを追加することもできます。 ソリューションへの物理デバイスの接続に関する詳細については、[IoT Suite リモート監視構成済みソリューションへのデバイスの接続][lnk-connect-rm]に関する記事をご覧ください。
4. **[Let me define my own Device ID (自分で自分のデバイス ID を定義する)]** を選択し、**mydevice_01** など一意のデバイス ID 名を入力します。
5. **[作成]**をクリックします。
   
   ![Save a new device][img-definedevice]
6. 「**新しいシミュレートされたデバイスの追加**」の手順 3. で **[完了]** をクリックすると、デバイス一覧に戻ります。
7. デバイス一覧でデバイスが **[実行中]** であることを確認できます。
   
    ![View new device in device list][img-runningnew]
8. 新しいデバイスからシミュレートされたテレメトリをダッシュボードに表示することもできます。
   
    ![View telemetry from new device][img-runningnew-2]

## <a name="device-properties"></a>デバイスのプロパティ
リモート監視の構成済みソリューションは、[デバイス ツイン][lnk-device-twin]を使用して、デバイスとソリューション バックエンドの間でデバイス メタデータを同期します。 デバイス ツインは、個々のデバイスのプロパティ値を格納している IoT Hub に格納されている JSON ドキュメントです。 デバイスは、メタデータを "*報告されるプロパティ*" としてソリューション バックエンドに定期的に送信します。このメタデータは、デバイス ツインに格納されます。 ソリューション バックエンドは、デバイス ツインの "*必要なプロパティ*" を、メタデータの更新をデバイスに送信するように設定できます。 報告されるプロパティは、デバイスから送信された最新のメタデータ値を示します。 詳細については、「[Device identity registry, device twin, and DocumentDB (デバイス ID レジストリ、デバイス ツイン、DocumentDB)][lnk-devicemetadata]」を参照してください。

> [!NOTE]
> ソリューションは、コマンドとメソッドに関連するデバイス固有のデータを格納するために、DocumentDB データベースも使用します。
> 
> 

1. デバイス一覧に戻ります。
2. **デバイス一覧**で新しいデバイスを選択し、**[編集]** をクリックして、**[デバイス ツイン] の [必要なプロパティ]** を編集します。
   
   ![デバイスの必要なプロパティを編集する][img-editdevice]
3. **[必要なプロパティ名]** を **[緯度]** に設定し、値を **47.639521** に設定します。 **[Save Changes to Device Registry (変更をデバイス レジストリに保存)]** をクリックします。
   
    ![デバイスの必要なプロパティを編集する][img-editdevice2]
4. **[デバイスの詳細]** パネルで、新しい緯度の値が最初に必要なプロパティとして表示され、古い緯度の値が報告されるプロパティとして表示されます。
   
    ![報告されるプロパティのビュー][img-editdevice3]
5. 現時点では、構成済みソリューション内のシミュレートされたデバイスは、必要なプロパティである **Desired.Config.TemperatureMeanValue** と **Desired.Config.TelemetryInterval** のみを処理します。 実際のデバイスは、すべての必要なプロパティを IoT ハブから読み取り、その構成を変更し、新しい値を "報告されるプロパティ" としてハブに報告する必要があります。

**[デバイスの詳細]** パネルで、**[デバイス ツイン] の [タグ]** を編集することもできます。方法は、**[デバイス ツイン] の [必要なプロパティ]** の編集と同じです。 ただし、必要なプロパティとは異なり、タグはデバイスと同期しません。 タグは、IoT ハブのデバイス ツインにのみ存在します。 タグは、デバイス一覧でカスタム フィルターを構築する場合に便利です。

## <a name="sort-the-device-list"></a>デバイス一覧の並べ替え

デバイス一覧を並べ替えるには、列見出しの横をクリックします。 最初のクリックでは昇順に並べ替えられ、2 回目のクリックでは降順に並べ替えられます。

![デバイス一覧の並べ替え][img-sortdevices]

## <a name="filter-the-device-list"></a>デバイス一覧のフィルター処理

デバイス一覧では、ハブに接続されているデバイスのカスタマイズされた一覧を表示するために、フィルターの作成、保存、再読み込みを行うことができます。 フィルターを作成する手順は、次のとおりです。

1. デバイスの一覧の上にあるフィルター編集アイコンをクリックします。
   
   ![フィルター エディターを開く][img-editfiltericon]
2. **[エディターのフィルター]** で、デバイス一覧をフィルター処理するためのフィールド、演算子、値を追加します。 フィルターの精度を上げるために、複数の句を追加することができます。 **[フィルター]** をクリックして、フィルターを適用します。
   
   ![フィルターを作成する][img-filtereditor]
3. この例では、一覧が製造元とモデル番号でフィルター処理されます。
   
   ![フィルター処理された一覧][img-filterelist]
4. カスタム名でフィルターを保存するには、**[保存]** アイコンをクリックします。
   
   ![フィルターを保存する][img-savefilter]
5. 前に保存したフィルターを再適用するには、**[保存済みのフィルターを開く]** アイコンをクリックします。
   
   ![フィルターを開く][img-openfilter]

デバイス ID、デバイスの状態、必要なプロパティ、報告されるプロパティ、タグに基づいて、フィルターを作成することができます。

> [!NOTE]
> **[エディターのフィルター]** では、**[詳細ビュー]** を使用して、クエリ テキストを直接編集することができます。
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>新しいデバイスのルールの追加
追加した新しいデバイスにはルールがありません。 このセクションでは、新しいデバイスによって報告された温度が 47 度を超えるとアラームをトリガーするルールを追加します。 始める前に、ダッシュボードで新しいデバイスのテレメトリ履歴に、デバイスの温度が 45 度を超えたことがないと示されていることに注意します。

1. デバイス一覧に戻ります。
2. デバイスのルールを追加するには、**デバイス一覧**で新しいデバイスを選択し、**[ルールの追加]** をクリックします。
3. データ フィールドとして **[Temperature (温度)]** を使用し、温度が 47 度を超えたときの出力として **[AlarmTemp]** を使用するルールを作成します。
   
    ![Add a device rule][img-adddevicerule]
4. 変更を保存するには、**[ルールを保存して表示]** をクリックします。
5. 新しいデバイスの [デバイスの詳細] ペインで **[コマンド]** をクリックします。
   
   ![Add a device rule][img-adddevicerule2]
6. コマンド リストから **[ChangeSetPointTemp]** を選択し、**[SetPointTemp]** を 45 に設定します。 **[コマンドの送信]**をクリックします。
   
   ![Add a device rule][img-adddevicerule3]
7. ソリューションのダッシュボードに戻ります。 しばらくして、新しいデバイスによって報告される温度が 47 度のしきい値を超えると、 **[アラーム履歴]** ペインに新しいエントリが表示されます。
   
   ![Add a device rule][img-adddevicerule4]
8. ダッシュボードの **[ルール]** ページですべてのルールを確認して編集できます。
   
    ![List device rules][img-rules]
9. ダッシュボードの **[アクション]** ページで、ルールへの対応として実行可能なすべてのアクションを確認して編集できます。
   
    ![List device actions][img-actions]

> [!NOTE]
> ルールへの対応として電子メール メッセージや SMS を送信したり、[ロジック アプリ][lnk-logic-apps]経由で基幹業務システムに統合したりするアクションを定義できます。 詳細については、[Azure IoT Suite リモート監視構成済みソリューションへのロジック アプリの接続][lnk-logicapptutorial]に関するページをご覧ください。
> 
> 

## <a name="disable-and-remove-devices"></a>デバイスの無効化と削除
デバイスを無効にし、無効になったデバイスを削除することができます。

![Disable and remove a device][img-disable]

## <a name="run-jobs"></a>ジョブの実行
ジョブをスケジュールして、デバイスに対する一括操作を行うことができます。 デバイスの一覧に対して、ジョブを作成します。 この一覧には、すべてのデバイスを含めることができます。また、デバイス一覧で[フィルター ツール](#filter-the-device-list)を使用して作成した、フィルター処理された一覧にすることもできます。 ジョブは、一覧のすべてのデバイスに対してメソッドを呼び出すことができます。また、デバイス一覧の各デバイスのデバイス ツインを更新することもできます。

### <a name="create-a-job-to-invoke-a-method"></a>メソッドを呼び出すためのジョブを作成する

次の手順は、一覧のすべてのデバイスに対してファームウェア更新メソッドを呼び出すジョブを作成する方法を示しています。 メソッドは、そのメソッドをサポートしているデバイスに対してのみ呼び出されます。

1. ファームウェア更新の対象となるデバイスの一覧を作成するには、デバイス一覧に対してフィルター ツールを使用します。
   
   ![フィルター エディターを開く][img-editfiltericon]
2. フィルター処理された一覧で、**[ジョブ スケジューラ]** をクリックします。
   
   ![ジョブ スケジューラを開く][img-clickjobscheduler]
3. **[ジョブのスケジュール設定]** パネルで **[メソッドの呼び出し]** をクリックします。
4. **[メソッドの呼び出し]** ページで、呼び出すメソッドの詳細を入力し、**[スケジュール]** をクリックします。
   
   ![メソッドのジョブを構成する][img-invokemethodjob]

**InitiateFirmwareUpdate** メソッドは、デバイスでタスクを非同期的に開始し、すぐに制御を返します。 その後、ファームウェア更新プロセスは報告されるプロパティを使用して、実行中に更新プロセスについて報告します。

### <a name="create-a-job-to-edit-the-device-twin"></a>デバイス ツインを編集するジョブを作成する

次の手順は、一覧のすべてのデバイスでデバイス ツインを編集するジョブの作成方法を示しています。

1. デバイス ツインの編集の対象となるデバイスの一覧を作成するには、デバイス一覧に対してフィルター ツールを使用します。
   
   ![フィルター エディターを開く][img-editfiltericon]
2. フィルター処理された一覧で、**[ジョブ スケジューラ]** をクリックします。
   
   ![ジョブ スケジューラを開く][img-clickjobscheduler]
3. **[ジョブのスケジュール設定]** パネルで **[デバイス ツインの編集]** をクリックします。
4. **[デバイス ツインの編集]** ページで、編集する **[必要なプロパティ]** と **[タグ]** の詳細を入力し、**[スケジュール]** をクリックします。
   
   ![メソッドのジョブを構成する][img-edittwinjob]

### <a name="monitor-the-job"></a>ジョブの監視
スケジュールしたジョブの状態は、**[管理ジョブ]** ページで監視できます。 **[ジョブの詳細]** パネルには、選択したジョブに関する情報が表示されます。
   
   ![ジョブの状態を見る][img-jobstatus]

ジョブに関する情報は、**ダッシュボード**で見ることもできます。
   
   ![ダッシュボードでジョブを見る][img-jobdashboard]


## <a name="behind-the-scenes"></a>バックグラウンド処理
事前構成済みソリューションをデプロイすると、デプロイ プロセスにより、選択した Azure サブスクリプションに複数のリソースが作成されます。 これらのリソースを [Azure ポータル][lnk-portal]で表示できます。 デプロイ プロセスにより、事前構成済みソリューション用に選択した名前に基づいた名前で、 **リソース グループ** が作成されます。

![Preconfigured solution in the Azure portal][img-portal]

各リソースの設定を表示するには、リソース グループ内のリソースのリストでそれを選択します。

また、事前構成済みソリューションのソース コードを表示することもできます。 リモート監視の事前構成済みソリューションのソース コードは、[azure-iot-remote-monitoring][lnk-rmgithub] GitHub リポジトリにあります。

* **DeviceAdministration** フォルダーには、ダッシュボードのソース コードが格納されています。
* **Simulator** フォルダーには、シミュレートされたデバイスのソース コードが格納されています。
* **EventProcessor** フォルダーには、受信テレメトリを処理するバックエンド プロセスのソース コードが格納されています。

完了したら、[azureiotsuite.com][lnk-azureiotsuite] で事前構成済みソリューションを Azure サブスクリプションから削除できます。 このサイトでは、構成済みのソリューションを作成したときにプロビジョニングされていたすべてのリソースを簡単に削除することができます。

> [!NOTE]
> 事前構成済みソリューションに関連するすべてのものを確実に削除するには、ポータルでリソース グループを削除するのではなく、[azureiotsuite.com][lnk-azureiotsuite] サイトで事前構成済みソリューションを削除してください。
> 
> 

## <a name="next-steps"></a>次のステップ
これで、事前構成済みの実際のソリューションをデプロイできました。引き続き IoT Suite の概要について学習するには、次の記事を参照してください。

* [リモート監視の事前構成済みソリューションのチュートリアル][lnk-rm-walkthrough]
* [デバイスをリモート監視の事前構成済みソリューションに接続する][lnk-connect-rm]
* [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
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
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
