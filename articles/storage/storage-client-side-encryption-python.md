<properties
	pageTitle="Python による Microsoft Azure Storage のクライアント側の暗号化 | Microsoft Azure"
	description="Python 用 Azure Storage クライアント ライブラリはクライアント側の暗号化を支援して、Azure Storage アプリケーションのセキュリティを最大限に高めます。"
	services="storage"
	documentationCenter="python"
	authors="dineshmurthy"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="dineshm;robinsh"/>


# Python による Microsoft Azure Storage のクライアント側の暗号化

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## Overview

[Python 用 Azure Storage クライアント ライブラリ](https://pypi.python.org/pypi/azure-storage)は、Azure Storage にアップロードする前にクライアント アプリケーション内のデータを暗号化し、クライアントにダウンロードするときにデータを復号化する作業を支援します。

>[AZURE.NOTE] Azure Storage Python ライブラリはプレビュー段階です。

## エンベロープ手法による暗号化と復号化
暗号化と復号化のプロセスは、エンベロープ手法に倣います。

### エンベロープ手法による暗号化
エンベロープ手法による暗号化は、次の方法で機能します。

1.	Azure ストレージ クライアント ライブラリは、1 回使用の対称キーであるコンテンツ暗号化キー (CEK) を生成します。

2.	ユーザー データは、この CEK を使用して暗号化されます。

3.	CEK は、キー暗号化キー (KEK) を使用してラップ (暗号化) されます。KEK は、キー識別子によって識別され、非対称キー ペアまたは対称キーのどちらでもよく、ローカルで管理することができます。ストレージ クライアント ライブラリ自体が KEK にアクセスすることはありません。ライブラリは、KEK によって提供されるキー ラップ アルゴリズムを呼び出すだけです。ユーザーは、必要な場合は、キー ラップ/ラップ解除にカスタム プロバイダーを使用できます。

4.	暗号化されたデータは、Azure Storage サービスにアップロードされます。ラップされたキーは追加の暗号化メタデータと共にメタデータとして (BLOB に) 格納されるか、暗号化されたデータ (キュー メッセージやテーブル エンティティ) によって補間されます。

### エンベロープ手法による復号化
エンベロープ手法による復号化は、次の方法で機能します。

1.	クライアント ライブラリでは、ユーザーがキー暗号化キー (KEK) をローカルで管理することを前提としています。ユーザーは、暗号化に使用された特定のキーを把握しておく必要はありません。代わりに、さまざまなキー識別子をキーに解決するキー リゾルバーを設定、使用できます。

2.	クライアント ライブラリは、暗号化されたデータおよびサービスに格納されている暗号化に関する情報 (ある場合) をダウンロードします。

3.	次に、ラップされたコンテンツ暗号化キー (CEK) が、キー暗号化キー (KEK) によりラップ解除 (復号化) されます。ここでも、クライアント ライブラリが KEK にアクセスすることはありません。これは、単にカスタム プロバイダーのラップ解除アルゴリズムを呼び出すだけです。

4.	次に、コンテンツ暗号化キー (CEK) を使用して、暗号化されたユーザー データを復号化します。

## 暗号化メカニズム  
ストレージ クライアント ライブラリは [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) を使用して、ユーザー データを暗号化します。具体的には、AES で[暗号ブロック チェーン (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) モードを使用します。サービスごとに動作が多少異なるため、以下でそれぞれについて説明します。

### BLOB
現在、クライアント ライブラリでは BLOB 全体の暗号化のみがサポートされています。具体的には、**create*** メソッドを使用する場合に暗号化がサポートされます。ダウンロードについては、完全ダウンロードと一部の範囲のダウンロードの両方がサポートされています。また、アップロードとダウンロードの両方の並列処理が可能です。

暗号化中、クライアント ライブラリは 16 バイトのランダムな初期化ベクトル (IV) と 32 バイトのランダムなコンテンツ暗号化キー (CEK) を生成し、この情報を使用して BLOB データのエンベロープ暗号化を実行します。ラップされた CEK と一部の追加暗号化メタデータが、サービスの暗号化された BLOB と共に、BLOB メタデータとして格納されます。

>[AZURE.WARNING] BLOB のメタデータを編集またはアップロードする場合は、このメタデータを保持している必要があります。このメタデータなしで新しいメタデータをアップロードした場合、ラップされた CEK、IV、およびその他のメタデータは失われ、BLOB コンテンツが再度取得可能になることはありません。

暗号化された BLOB のダウンロードには、便利なメソッド **get*** を使用した BLOB 全体のコンテンツの取得も含まれます。ラップされた CEK はラップ解除され、復号化されたデータをユーザーに返すために IV (この場合 BLOB メタデータとして格納された) と共に使用されます。

暗号化された BLOB での任意の範囲 (範囲パラメーターが渡された **get*** メソッド) のダウンロードでは、ユーザーが指定した範囲が調整されます。これは、少量の追加データを取得して、要求された範囲を正常に復号化するためです。

このスキームを使用して暗号化/復号化できるのは、ブロック BLOB とページ BLOB だけです。現在、追加 BLOB の暗号化はサポートされていません。

### キュー
キュー メッセージの書式は一定でないため、クライアント ライブラリでは、メッセージ テキストで初期化ベクトル (IV) と暗号化されたコンテンツ暗号化キー (CEK) を含むカスタム書式が定義されます。

暗号化中、クライアント ライブラリは 16 バイトのランダムな IV と 32 バイトのランダムな CEK を生成し、この情報を使用してキュー メッセージ テキストのエンベロープ暗号化を実行します。次に、ラップされた CEK と追加の暗号化メタデータのいくつかが、暗号化されたキュー メッセージに追加されます。この変更されたメッセージ (下記参照) は、サービスに格納されます。

	<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

復号化中、ラップされたキーがキュー メッセージから抽出され、ラップ解除されます。また、IV もキュー メッセージから抽出され、これをラップ解除されたキーと共に使用して、キュー メッセージ データが復号化されます。暗号化メタデータは小さいため (500 バイト未満)、キュー メッセージの 64 KB という上限を考慮したとき、影響が小さくて済みます。

### テーブル
クライアント ライブラリでは、挿入および置換操作のエンティティ プロパティの暗号化がサポートされます。

>[AZURE.NOTE] 現在、マージはサポートされていません。別のキーを使用してプロパティのサブセットが以前に暗号化されている場合、新しいプロパティをマージしたり、メタデータを更新したりするとデータ損失が発生します。マージでは、追加のサービス呼び出しをして既存のエンティティをサービスから読み込むか、プロパティごとに新しいキーを使用する必要があります。いずれの方法も、パフォーマンス上の理由でお勧めできません。

テーブル データの暗号化は、次のように機能します。

1.	暗号化するプロパティをユーザーが指定します。

2.	クライアント ライブラリは 16 バイトのランダムな初期化ベクトル (IV) と 32 バイトのランダムなコンテンツ暗号化キー (CEK) を各エンティティごとに生成し、プロパティごとに新しい IV を派生させることで暗号化する個々のプロパティでエンベロープ暗号化を実行します。暗号化されたプロパティは、バイナリ データとして格納されます。

3.	次に、ラップされた CEK と追加の暗号化メタデータが、2 つの追加の予約済みプロパティとして格納されます。最初の予約済みプロパティ (\_ClientEncryptionMetadata1) は文字列プロパティで、IV、バージョン、およびラップされたキーに関する情報を保持します。2 番目の予約済みプロパティ (\_ClientEncryptionMetadata2) はバイナリ プロパティで、暗号化されたプロパティに関する情報を保持します。この 2 番目のプロパティ (\_ClientEncryptionMetadata2) 内の情報自体が暗号化されます。

4.	これらの暗号化に必要な追加の予約済みプロパティにより、ユーザーが所有できるカスタム プロパティは 252 ではなく、250 個になります。エンティティの合計サイズは、1 MB 未満である必要があります。

	暗号化できるのは、文字列プロパティのみであることに注意してください。他の種類のプロパティを暗号化する必要がある場合は、文字列に変換する必要があります。暗号化された文字列はバイナリ プロパティとしてサービスで保存され、復号化された後、文字列 (EdmType.STRING 型の EntityProperties ではない、生の文字列) に変換されて戻されます。

	テーブルの場合、暗号化ポリシーに加え、ユーザーは暗号化するプロパティを指定する必要があります。そのためには、これらのプロパティを TableEntity オブジェクトに格納し、型を EdmType.STRING に、暗号化を true に設定するか、tableservice オブジェクトで encryption\_resolver\_function を設定します。暗号化リゾルバーは、パーティション キー、行キー、プロパティ名を取得し、プロパティを暗号化するかどうかを示すブール値を返します。暗号化時、クライアント ライブラリはこの情報を使用して、ネットワークへの書き込み時にプロパティを暗号化するかどうかを決定します。また、デリゲートは、プロパティの暗号化方法に関するロジックを使用する可能性にも備えます。(X の場合、プロパティ A を暗号化し、それ以外の場合はプロパティ A および B を暗号化するなど。) エンティティの読み込み中、またはクエリの実行中は、この情報を指定する必要はありません。

### バッチ操作
バッチ内のすべての行に同じ暗号化ポリシーが適用されます。クライアント ライブラリは、バッチ内の行ごとに 1 つの新しいランダム IV とランダム CEK を内部的に生成します。ユーザーは、暗号化リゾルバーでこの動作を定義することで、バッチの各操作で個別のプロパティを暗号化することも選択できます。バッチを tableservice batch() メソッドを通じてコンテキスト マネージャーとして作成した場合、そのバッチには tableservice の暗号化ポリシーが自動的に適用されます。コンストラクターを呼び出すことによって明示的に作成したバッチの場合、暗号化ポリシーはパラメーターとして渡し、バッチが完了するまで変更されないようにする必要があります。なお、エンティティの暗号化が行われるのは、バッチの暗号化ポリシーを使用してバッチにエンティティを挿入するときであり、tableservice の暗号化ポリシーを使用してバッチをコミットするときではありませんので注意してください。

### クエリ
クエリ操作を実行するには、結果セット内のすべてのキーを解決できる Key Resolver を指定する必要があります。クエリの結果に含まれたエンティティをプロバイダーに解決できない場合、クライアント ライブラリでエラーがスローされます。クエリでサーバー側のプロジェクションが実行される場合、クライアント ライブラリは既定で、特別な暗号化メタデータ プロパティ (\_ClientEncryptionMetadata1 および \_ClientEncryptionMetadata2) を選択した列に追加します。

>[AZURE.IMPORTANT] クライアント側の暗号化を使用する場合は、次の重要な点に注意してください。
>
>- 暗号化された BLOB を読み書きするときは、完全 BLOB アップロード コマンドと範囲/完全 BLOB ダウンロード コマンドを使用してください。Put Block、Put Block List、Write Pages、Clear Pages などのプロトコル操作で暗号化された BLOB に書き込む行為は避けてください。暗号化された BLOB が壊れたり、読み取り不可能になったりすることがあります。
>
>- テーブルの場合、同様の制約があります。暗号化メタデータを更新せずに暗号化されたプロパティを更新する行為は避けてください。
>
>- 暗号化された BLOB にメタデータを設定する場合、メタデータの設定は付加的には行われないため、復号化に必要な暗号化関連メタデータが上書きされる可能性があります。これはスナップショットにも該当します。暗号化された BLOB のスナップショットを作成するときにメタデータを指定しないでください。メタデータを設定する必要がある場合は、最初に **get\_blob\_metadata** メソッドを呼び出し、現在の暗号化メタデータを取得してください。メタデータの設定中に、同時書き込みは行わないでください。
>
>- 暗号化されたデータのみを処理する必要のあるユーザーについては、サービス オブジェクトで **require\_encryption** フラグを有効にします。詳細については、以下をご覧ください。

ストレージ クライアント ライブラリでは、指定された KEK とキー リゾルバーによって、次のインターフェイスが実装されることを想定しています。Python KEK を管理するための [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のサポートはまだ提供されていませんが、このライブラリに統合される予定です。


## クライアント API / インターフェイス
ストレージ サービス オブジェクト (blockblobservice) が作成されたら、暗号化ポリシーを構成するフィールド key\_encryption\_key、key\_resolver\_function、require\_encryption に値を割り当てることができます。KEK のみ、リゾルバーのみ、またはその両方を指定できます。key\_encryption\_key は、キー識別子を使用して識別されるキーの基本型で、ラップ/ラップ解除のロジックを提供します。key\_resolver\_function は、復号化の処理中にキーを解決するために使用され、特定のキー識別子に有効な KEK を返します。これは、複数の場所で管理されている複数のキーの中から選択するための機能を提供します。

KEK で正常にデータを暗号化するには、次のメソッドを実装する必要があります。
- wrap\_key(cek): ユーザーが選択したアルゴリズムを使用して、指定された CEK (バイト) をラップし、ラップされたキーを返します。
- get\_key\_wrap\_algorithm(): キーをラップするためのアルゴリズムを返します。
- get\_kid(): この KEK の文字列キー ID を返します。KEK で正常にデータの暗号化を解除するには、次のメソッドを実装する必要があります。
- unwrap\_key(cek, algorithm): 文字列指定アルゴリズムを使用して、指定された CEK のラップされていない形式を返します。
- get\_kid(): この KEK の文字列キー ID を返します。

キー リゾルバーでは少なくとも、上記のインターフェイスを実装する、指定されたキー ID に対応する KEK を返すメソッドを実装する必要があります。このメソッドだけが、サービス オブジェクトの key\_resolver\_function プロパティに割り当てられます。

- 暗号化では、キーは常に使用され、キーがないとエラーが発生します。
- 復号化の場合:
	- キーを取得するよう指定した場合にキー リゾルバーが起動します。リゾルバーが指定されていても、キー識別子のマッピングがない場合、エラーがスローされます。
	- リゾルバーが指定されていない場合にキーが指定されると、そのキーの識別子が必須キー識別子と一致すると、そのキーが使用されます。識別子が一致しなければ、エラーがスローされます。

	  BLOB、キュー、およびテーブルの詳細なエンド ツー エンド シナリオについては、azure.storage.samples の暗号化のサンプルを参照してください。KEK とキー リゾルバーのサンプル実装は、それぞれ KeyWrapper と KeyResolver としてサンプル ファイルに用意されています。

### RequireEncryption モード
すべてのアップロードとダウンロードを暗号化する必要がある場合、オプションで操作のモードを有効にすることができます。このモードでは、暗号化ポリシーを設定せずにデータをアップロードしようとしたり、サービスで暗号化されていないデータをダウンロードしようとしたりすると、クライアントで失敗します。この動作は、サービス オブジェクトの **require\_encryption** フラグによって制御されます。

### BLOB サービス暗号化
blockblobservice オブジェクトで暗号化ポリシー フィールドを設定します。その他の操作はすべて、クライアント ライブラリが内部的に処理します。

	# Create the KEK used for encryption.
	# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
	kek = KeyWrapper('local:key1') # Key identifier

	# Create the key resolver used for decryption.
	# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
	key_resolver = KeyResolver()
	key_resolver.put_key(kek)

	# Set the KEK and key resolver on the service object.
	my_block_blob_service.key_encryption_key = kek
	my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

	# Upload the encrypted contents to the blob.
	my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

	# Download and decrypt the encrypted contents from the blob.
	blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### Queue サービス暗号化
queueservice オブジェクトで暗号化ポリシー フィールドを設定します。その他の操作はすべて、クライアント ライブラリが内部的に処理します。

	# Create the KEK used for encryption.
	# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
	kek = KeyWrapper('local:key1') # Key identifier

	# Create the key resolver used for decryption.
	# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
	key_resolver = KeyResolver()
	key_resolver.put_key(kek)

	# Set the KEK and key resolver on the service object.
	my_queue_service.key_encryption_key = kek
	my_queue_service.key_resolver_funcion = key_resolver.resolve_key

	# Add message
	my_queue_service.put_message(queue_name, content)

	# Retrieve message
	retrieved_message_list = my_queue_service.get_messages(queue_name)

### Table サービス暗号化
暗号化ポリシーを作成し、要求オプションにそれを設定するだけでなく、**encryption\_resolver\_function** を **tableservice** に指定するか、EntityProperty に暗号化属性を設定する必要があります。

### リゾルバーの使用

	# Create the KEK used for encryption.
	# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
	kek = KeyWrapper('local:key1') # Key identifier

	# Create the key resolver used for decryption.
	# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
	key_resolver = KeyResolver()
	key_resolver.put_key(kek)

	# Define the encryption resolver_function.
	def my_encryption_resolver(pk, rk, property_name):
			if property_name == 'foo':
					return True
			return False

	# Set the KEK and key resolver on the service object.
	my_table_service.key_encryption_key = kek
	my_table_service.key_resolver_funcion = key_resolver.resolve_key
	my_table_service.encryption_resolver_function = my_encryption_resolver

	# Insert Entity
	my_table_service.insert_entity(table_name, entity)

	# Retrieve Entity
	# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
	my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### 属性の使用
前述のように、プロパティを EntityProperty オブジェクトに格納して暗号化フィールドを設定することで、そのプロパティを暗号化としてマークすることができます。

	encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## 暗号化とパフォーマンス
ストレージ データを暗号化すると、パフォーマンスのオーバーヘッドが増えることに注意してください。コンテンツ キーと IV を生成する必要があり、コンテンツ自体を暗号化する必要があります。また、追加のメタデータをフォーマットおよびアップロードする必要もあります。このオーバーヘッドは、暗号化されるデータの量によって異なります。開発中に、アプリケーションのパフォーマンスを常にテストすることをお勧めします。

## 次のステップ

- [Azure Storage Client Library for Java の PyPi パッケージ](https://pypi.python.org/pypi/azure-storage) をダウンロードする
- GitHub から [Azure Storage Client Library for Python のソースコード](https://github.com/Azure/azure-storage-python)をダウンロードする

<!---HONumber=AcomDC_0921_2016-->