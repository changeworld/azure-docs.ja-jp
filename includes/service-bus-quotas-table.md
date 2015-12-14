次の表は、Service Bus メッセージングに固有のクォータ情報の一覧です。この表には Event Hubs の制限が含まれていますが、Event Hubs の詳細については、「[Event Hubs 料金](http://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。Service Bus の料金やその他のクォータについては、「[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)」の概要を参照してください。

|クォータ名|スコープ|型|超過したときの動作|値|
|---|---|---|---|---|
| Azure サブスクリプションごとの名前空間の最大数|名前空間|静的|追加の名前空間に関する後続の要求はポータルで拒否されます。|100|
|キュー/トピック サイズ|エンティティ|キューおよびトピック作成時に定義|受信メッセージが拒否され、呼び出し元のコードが例外を受け取ります。|1、2、3、4、または 5 GB<br /><br />[パーティション分割](service-bus-partitioning.md)が有効な場合、キュー/トピックの最大サイズは 80 GB です。|
|名前空間の同時接続数|名前空間|静的|追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。REST 操作は、TCP 同時接続数に加算されません。|NetMessaging: 1,000<br /><br />AMQP: 5,000|
|キュー/トピック/サブスクリプション エンティティの同時接続数|エンティティ|静的|追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。REST 操作は、TCP 同時接続数に加算されません。|名前空間ごとの同時接続数が上限です。|
|キュー/トピック/サブスクリプション エンティティの同時受信要求数|エンティティ|静的|以後の受信要求が拒否され、呼び出し元のコードが例外を受け取ります。このクォータは、1 つのトピックのすべてのサブスクリプションの同時受信操作の合計数に適用されます。|5,000|
|リレーの同時リスナー数|エンティティ|静的|追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。|25|
|同時リレー リスナー数|システム全体|静的|追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。|2,000|
|サービス名前空間に含まれるすべてのリレー エンドポイントごとの同時リレー接続数|システム全体|静的|-|5,000|
|サービス名前空間ごとのリレー エンドポイント数|システム全体|静的|-|10,000|
|サービス名前空間ごとのトピック/キュー数|システム全体|静的|サービス名前空間での以降の新しいトピックまたはキューの作成要求は拒否されます。その結果、[Azure クラシック ポータル][]で構成されている場合は、エラー メッセージが生成されます。管理 API から呼び出される場合は、呼び出し元のコードが例外を受け取ります。|10,000<br /><br />1 つのサービス名前空間に含まれるトピックとキューの合計数は 10,000 以下にする必要があります。|
|サービス名前空間ごとのパーティション分割されたトピック/キューの数|システム全体|静的|サービス名前空間での以降の新しいパーティション分割されたトピックまたはキューの作成要求は拒否されます。その結果、[Azure クラシック ポータル][]で構成されている場合は、エラー メッセージが生成されます。管理 API で呼び出される場合は、呼び出し元のコードが **QuotaExceededException** 例外を受け取ります。|100<br /><br />パーティション分割された各キューまたはトピックは、名前空間ごとに 10,000 個のエンティティ クォータに加算されます。|
|メッセージング エンティティ名の最大サイズ: 名前空間、キュー、トピック、サブスクリプション、Event Hubs|エンティティ|静的|-|50 文字|
|Event Hubs イベントの最大サイズ|システム全体|静的|-|256 KB| 
|キュー/トピック/サブスクリプション エンティティのメッセージ サイズ|システム全体|静的|これらのクォータを超過した受信メッセージは拒否され、呼び出し元のコードが例外を受け取ります。|最大メッセージ サイズ: 256 KB<br /><br />**注** システム オーバーヘッドにより、通常、この上限は 256 KB をわずかに下回ります。<br /><br />ヘッダーの最大サイズ: 64 KB<br /><br />プロパティ バッグ内のヘッダー プロパティの最大数: **MaxValue**<br /><br />プロパティ バッグ内のプロパティの最大サイズ: 明示的な上限なし。ヘッダーの最大サイズによって制限されます。|
|[NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) と [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) リレーのメッセージ サイズ|システム全体|静的|これらのクォータを超過した受信メッセージは拒否され、呼び出し元のコードが例外を受け取ります。|64 KB 
|[HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) と [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) リレーのメッセージ サイズ|システム全体|静的|-|無制限|
|キュー/トピック/サブスクリプション エンティティのメッセージ プロパティ サイズ|システム全体|静的|**SerializationException** 例外が生成されます。|各プロパティのメッセージ プロパティの最大サイズは 32 K です。すべてのプロパティの合計サイズが 64 K を超えることはできません。これは [BrokeredMessage](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.aspx) のヘッダー全体に適用されます。これには、ユーザー プロパティとシステム プロパティ ([SequenceNumber](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sequencenumber.aspx)、[Label](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.label.aspx)、[MessageId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) など) の両方が含まれます。|
|トピックごとのサブスクリプション数|システム全体|静的|トピックのサブスクリプションの追加作成に関する後続の要求が拒否されます。その結果、ポータルで構成されている場合は、エラー メッセージが表示されます。管理 API で呼び出される場合は、呼び出し元のコードが例外を受け取ります。|2,000|
|トピックごとの SQL フィルター数|システム全体|静的|トピックのフィルターの以降の追加作成要求が拒否され、呼び出し元のコードが例外を受け取ります。|2,000|
|トピックごとの相関フィルター数|システム全体|静的|トピックのフィルターの以降の追加作成要求が拒否され、呼び出し元のコードが例外を受け取ります。|100,000|
|SQL フィルター/アクションのサイズ|システム全体|静的|以降の追加のフィルター作成要求が拒否され、呼び出し元のコードが例外を受け取ります。|フィルター条件の文字列の最大長: 1024 (1K)<br /><br />ルール アクションの文字列の最大長: 1024 (1K)<br /><br />ルール アクションごとの式の最大数: 32|

[Azure クラシック ポータル]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->