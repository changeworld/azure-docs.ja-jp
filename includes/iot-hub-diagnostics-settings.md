### <a name="enable-logging-with-diagnostics-settings"></a>診断設定を使用してログ記録を有効にする

1. [Azure ポータル][lnk-portal]にサインインし、IoT Hub に移動します。
1. **[診断設定]** を選択します。
1. **[診断を有効にする]** を選択します。

   ![診断の有効化][1]

1. 診断設定の名前を付けます。
1. ログの送信先を選択します。 3 つのオプションを自由に組み合わせて選択できます。
   * ストレージ アカウントへのアーカイブ
   * イベント ハブへのストリーミング
   * Log Analytics への送信
1. 監視する操作を選択し、それらの操作のログを有効にします。 診断設定では、次の操作をレポートできます。
   * 接続
   * デバイス テレメトリ
   * クラウドからデバイスへのメッセージ
   * デバイス ID の操作
   * ファイルのアップロード
   * メッセージ ルーティング
   * クラウドからデバイスへのツイン操作
   * デバイスからクラウドへのツイン操作
   * ツイン操作
   * ジョブ操作
   * ダイレクト メソッド  
1. 新しい設定を保存します。 

PowerShell を使用して診断設定を有効にする場合は、次のコードを使用します。

```
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

新しい設定は、10 分ほどで有効になります。 その後、構成したアーカイブ ターゲットのログが **[診断設定]** ブレードに表示されます。 診断の構成の詳細については、「[Azure リソースからのログ データの収集と使用][lnk-diagnostics-settings]」を参照してください。

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
