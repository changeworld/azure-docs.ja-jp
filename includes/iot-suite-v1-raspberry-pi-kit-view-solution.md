## <a name="view-the-solution-dashboard"></a>ソリューション ダッシュボードの表示

ソリューションのダッシュボードでは、デプロイされたソリューションを管理できます。 テレメトリの表示、デバイスの追加、メソッドの呼び出しなどを行えます。

1. プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** が表示されたら、**[起動]** を選択し、新しいタブでリモート監視ソリューション ポータルを開きます。

    ![Launch the preconfigured solution][img-launch-solution]

1. 既定では、ソリューション ポータルに "*ダッシュボード*" が表示されます。 ソリューション ポータルの他の領域に移動するには、ページの左側にあるメニューを使用します。

    ![Remote monitoring preconfigured solution dashboard][img-menu]

## <a name="add-a-device"></a>デバイスを追加する

デバイスが構成済みソリューションに接続するには、有効な資格情報を使用して IoT Hub に対してデバイス自身の ID を証明する必要があります。 デバイスの資格情報は、ソリューション ダッシュボードから取得できます。 このチュートリアルの後半で、クライアント アプリケーションにデバイスの資格情報を含めます。

まだこれを行っていない場合は、カスタム デバイスをリモート監視ソリューションに追加します。 ソリューション ダッシュボードで次の手順を完了します。

1. ダッシュボードの左下隅にある **[デバイスの追加]**をクリックします。

   ![デバイスを追加する][1]

1. **[カスタム デバイス]** パネルで、**[新規追加]** をクリックします。

   ![カスタム デバイスを追加する][2]

1. **[デバイス ID を自分で定義する]** を選択します。 **rasppi** などのデバイス ID を入力します。**[ID の確認]** をクリックして、その名前をまだソリューションで使用していないことを確認し、**[作成]** をクリックしてデバイスをプロビジョニングします。

   ![デバイス ID を追加する][3]

1. デバイスの資格情報 (**デバイス ID**、**IoT Hub ホスト名**、**デバイス キー**) を書き留めておきます。 Raspberry Pi のクライアント アプリケーションがリモート監視ソリューションに接続する際に、この値が必要になります。 次に、 **[Done]**をクリックします。

    ![デバイスの資格情報を表示する][4]

1. ソリューション ダッシュボードのデバイスの一覧でデバイスを選択します。 次に、**[デバイスの詳細]** パネルで、**[デバイスの有効化]** をクリックします。 現在、デバイスの状態は **[実行中]** です。 リモート監視ソリューションはデバイスからテレメトリを受信し、デバイス上でメソッドを呼び出すことができます。

[img-launch-solution]: media/iot-suite-v1-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite3.png