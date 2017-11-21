## <a name="view-the-telemetry"></a>テレメトリを表示する

これで、Raspberry Pi からリモート監視ソリューションにテレメトリが送信されるようになりました。 テレメトリはソリューション ダッシュボードで確認できます。 また、ソリューション ダッシュボードから Raspberry Pi にメッセージを送信することもできます。

- ソリューション ダッシュボードに移動します。
- **[表示するデバイス:]** ボックスの一覧からデバイスを選択します。
- Raspberry Pi からのテレメトリがダッシュボードに表示されます。

![Raspberry Pi からのテレメトリを表示する][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>ファームウェアの更新を開始する

ファームウェアの更新プロセスでは、デバイス クライアント アプリケーションの更新バージョンを Raspberry Pi にダウンロードおよびインストールします。 ファームウェアの更新プロセスの詳細については、「[IoT Hub を使用したデバイス管理の概要][lnk-update-pattern]」でファームウェア更新パターンの説明を参照してください。

ファームウェア更新プロセスを開始するには、デバイスでメソッドを呼び出します。 このメソッドは非同期で、更新プロセスの開始直後に制御を返します。 デバイスは、報告されるプロパティを使用して、ソリューションに更新の進行状況を通知します。

ソリューション ダッシュボードから、Raspberry Pi でメソッドを呼び出します。 Raspberry Pi は初めてリモート監視ソリューションに接続したときに、サポートしているメソッドに関する情報を送信します。 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
