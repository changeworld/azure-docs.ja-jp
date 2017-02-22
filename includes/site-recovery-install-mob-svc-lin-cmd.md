1. 保護するサーバーのローカル フォルダー (たとえば、/tmp) にインストーラーをコピーし、ターミナル ウィンドウから次のコマンドを実行します。
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. これで、次のコマンド ラインを使用してモビリティ サービスをインストールできます。

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>モビリティ サービスのインストーラーのコマンド ライン引数

|パラメーター|型|説明|指定できる値|
|-|-|-|-|
|-t |必須|エージェントの種類<br>(次のリリースで廃止予定)|*both*|
|-a |必須|エージェントの構成<br>(次のリリースで廃止予定) |*host*|
|-R |省略可能|エージェントの役割|エージェント<br>MasterTarget|
|-d |省略可能|モビリティ サービスをインストールする場所|/usr/local/ASR|
|-i |必須|構成サーバーの IP アドレス。|任意の有効な IP アドレス|
|-p |必須|構成サーバーが着信接続をリッスンするポート|443|
|-s |必須|インストールが正常に完了した後にサービスを開始します<br>(次のリリースで廃止予定)|*y*|
|-c |必須|エージェントとプロセス サーバーの間の通信モード<br>(次のリリースで廃止予定) |*https*|
|-P |必須|構成サーバーのパスフレーズ|任意の有効な UNC またはローカル ファイル パス|


#### <a name="sample-usage"></a>使用例
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```


<!--HONumber=Jan17_HO3-->


