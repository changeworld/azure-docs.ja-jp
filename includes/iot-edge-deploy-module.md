Azure IoT Edge の主要な機能の 1 つは、クラウドから IoT Edge デバイスにモジュールをデプロイできることです。 IoT Edge モジュールは、コンテナーとして実装されている実行可能ファイルのパッケージです。 このセクションでは、シミュレートされたデバイスのテレメトリを生成するモジュールをデプロイします。 

1. Azure Portal で、お使いの IoT ハブに移動します。
1. **[IoT Edge (preview)]\(IoT Edge \(プレビュー\)\)** に移動し、IoT Edge デバイスを選びます。
1. **[Set Modules] \(モジュールの設定)** を選択します。
1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。
1. **[名前]** フィールドに「`tempSensor`」と入力します。 
1. **[イメージの URI]** フィールドに「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。 
1. 他の設定はそのままにして、**[保存]** を選びます。

   ![名前とイメージの URI を入力した後に IoT Edge モジュールを保存します](./media/iot-edge-deploy-module/name-image.png)

1. **[Add modules]\(モジュールの追加\)** ステップに戻り、**[次へ]** を選びます。
1. **[Specify routes]\(ルートの指定\)** ステップで、**[次へ]** を選びます。
1. **[Review template]\(テンプレートのレビュー\)** ステップで、**[Submit]\(送信\)** を選びます。
1. デバイスの詳細ページに戻り、**[更新]** を選びます。 新しい tempSensor モジュールが IoT Edge ランタイムと一緒に実行されていることが表示されます。 

   ![デプロイ済みモジュールのリストに表示された tempSensor][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png