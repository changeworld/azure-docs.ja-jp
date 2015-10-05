## パブリック IP アドレス
パブリック IP アドレス リソースは、予約されたパブリック IP アドレスか、動的なパブリック IP アドレスを提供します。パブリック IP アドレスをスタンドアロン オブジェクトとして作成できますが、実際にアドレスを使用するには別のオブジェクトに関連付ける必要があります。パブリック IP アドレスをロード バランサー、アプリケーション ゲートウェイ、または NIC に関連付けて、これらのリソースへのインターネット アクセスを提供できます。

|プロパティ|説明|値の例|
|---|---|---|
|**publicIPAllocationMethod**|IP アドレスが*静的*か*動的*かを定義します。|static、dynamic|
|**idleTimeoutInMinutes**|アイドル タイムアウトを定義します。|4 ～ 30 の任意の値|
|**ipAddress**|オブジェクトに割り当てられる IP アドレスです。これは、読み取り専用のプロパティです。|104\.42.233.77|

### DNS の設定
パブリック IP アドレスには、以下のプロパティを含む **dnsSettings** という名前の子オブジェクトがあります。

|プロパティ|説明|値の例|
|---|---|---|
|**domainNameLabel**|名前解決に使用されるホスト名です。|www、ftp、vm1|
|**reverseFqdn**|IP アドレスに解決される完全修飾ドメイン名であり、PTR レコードとして DNS に登録されます。|www.contoso.com|

JSON 形式でのパブリック IP アドレスのサンプル:

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

<!---HONumber=Sept15_HO4-->