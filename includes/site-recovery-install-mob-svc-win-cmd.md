1. 保護するサーバーのローカル フォルダー (たとえば、C:\Temp) にインストーラーをコピーし、管理者特権でコマンド プロンプトから次のコマンドを実行します。

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. これで、次のコマンド ラインを使用してモビリティ サービスをインストールできます。

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>モビリティ サービスのインストーラーのコマンド ライン引数

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | パラメーター|型|説明|指定できる値|
  |-|-|-|-|
  |/Role|必須|モビリティ サービスをインストールするかどうかを指定します。|エージェント </br> MasterTarget|
  |/InstallLocation|必須|モビリティ サービスをインストールする場所|コンピューター上の任意のフォルダー|
  |/CSIP|必須|構成サーバーの IP アドレス| 任意の有効な IP アドレス|
  |/PassphraseFilePath|必須|パスフレーズを格納する場所 |任意の有効な UNC またはローカル ファイル パス|
  |/LogGilePath|省略可能|インストール ログの場所|コンピューター上の任意の有効なフォルダー|

#### <a name="sample-usage"></a>使用例

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->


