<properties 
   pageTitle="ロジック アプリでの SharePoint コネクタの使用" 
   description="ロジック アプリでの SharePoint コネクタの使用" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# ロジック アプリでの SharePoint コネクタの使用

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。Microsoft SharePoint コネクタを使用すると、Microsoft SharePoint Server または SharePoint Online に接続して、ドキュメントやリスト項目を管理できます。さらに、ドキュメントやリスト項目の作成、更新、取得、削除などの操作を実行できます。オンプレミスの SharePoint サーバーの場合は、コネクタの構成の一部として Service Bus 接続文字列を入力し、サーバーに接続するオンプレミスのリスナー エージェントをインストールすることができます。

SharePoint Online コネクタおよび SharePoint Server コネクタ ギャラリー アプリでは、SharePoint とやり取りするメカニズムとしてトリガーとアクションを使用できます。

## ロジック アプリ用の SharePoint Online コネクタの作成

SharePoint Online コネクタを使用するには、まず SharePoint Online コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1. Azure ポータルの右下にある [+ 新規] オプションを使用して Azure Marketplace を開きます。

2. [Web + モバイル]、[API アプリ] の順に移動して、"SharePoint Online コネクタ" を検索します。

3. SharePoint Online コネクタを構成し、[作成] をクリックします。コネクタを作成するには、次のパラメーターを入力する必要があります。

	<table>
	  <tr>
	    <td><b>名前</b></td>
	    <td><b>必須</b></td>
	    <td><b>説明</b></td>
	  </tr>
	  <tr>
	    <td>サイトの URL</td>
	    <td>あり</td>
	    <td>SharePoint Web サイトの完全な URL を指定します。例: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>ドキュメント ライブラリ/リストの相対 URL</td>
	    <td>あり</td>
	    <td>コネクタが変更を加えることが許可されるドキュメント ライブラリまたはリストの URL を、SharePoint サイトの URL からの相対位置で指定します。例:Lists/Task、Shared Documents'。</td>
	  </tr>
	</table>
	![][1]


4. 上記の手順を完了すると、同じリソース グループ内に SharePoint Online コネクタを使用するロジック アプリを作成できます。

## ロジック アプリ用の SharePoint Server コネクタの作成

SharePoint Server コネクタを使用するには、まず SharePoint Server コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1. Azure ポータルの右下にある [+ 新規] オプションを使用して Azure Marketplace を開きます。

2. [Web + モバイル]、[API アプリ] の順に移動して、"SharePoint Server コネクタ" を検索します。

3. SharePoint Server コネクタを構成し、[作成] をクリックします。コネクタを作成するには、次のパラメーターを入力する必要があります。

	<table>
	  <tr>
	    <td><b>名前</b></td>
	    <td><b>必須</b></td>
	    <td><b>説明</b></td>
	  </tr>
	  <tr>
	    <td>サイトの URL</td>
	    <td>あり</td>
	    <td>SharePoint Web サイトの完全な URL を指定します。例: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>認証モード</td>
	    <td>あり</td>
	    <td>SharePoint サイトへの接続に使用する認証モードを指定します。許可される値は、次のとおりです。<br><br>
			使用します<br>
			OAuth2<br>
			WindowsAuthentication<br>
			FormBasedAuthentication<br><br>
	
	[既定] 資格情報を選択した場合は、SharePoint マイクロサービスが実行されている既定の資格情報が使用されます。この場合、ユーザー名とパスワードは必要ありません。その他の種類の認証の場合、[ユーザー名] と [パスワード] は必須のフィールドになります。 <br><br>注: 匿名認証はサポートされていません。</td>
	  </tr>
	  <tr>
	    <td>ユーザー名</td>
	    <td>いいえ</td>
	    <td>認証モードが [既定] または [OAuth2] でない場合は、SharePoint サイトへの接続に使用する有効なユーザー名を指定します。</td>
	  </tr>
	  <tr>
	    <td>パスワード</td>
	    <td>いいえ</td>
	    <td>認証モードが [既定] または [OAuth2] でない場合は、SharePoint サイトへの接続に使用する有効なパスワードを指定します。</td>
	  </tr>
	  <tr>
	    <td>ドキュメント ライブラリ/リストの相対 URL</td>
	    <td>あり</td>
	    <td>コネクタが変更を加えることが許可されるドキュメント ライブラリまたはリストの URL を、SharePoint サイトの URL からの相対位置で指定します。例:Lists/Task、Shared Documents'。</td>
	  </tr>
	  <tr>
	    <td>Service Bus の接続文字列</td>
	    <td>いいえ</td>
	    <td>有効な Service Bus 名前空間の接続文字列である必要があります。<br><br>
	
	SharePoint サーバーにアクセスできるサーバーにリスナー エージェントをインストールする必要があります。 <br>API アプリの概要ページに移動し、 'Hybrid Connection' をクリックしてエージェントをインストールできます。</td>
	  </tr>
	</table>


	![][2]

4. 作業が完了すると、同じリソース グループ内に SharePoint Server コネクタを使用するロジック アプリを作成できます。
5. SharePoint サーバーにアクセスできるサーバーにリスナー エージェントをインストールする必要があります。API アプリの概要ページに移動し、 'Hybrid Connection' をクリックしてエージェントをインストールできます。

## ロジック アプリでの SharePoint コネクタの使用

API アプリを作成すると、ロジック アプリのトリガーやアクションとして SharePoint コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1. 新しいロジック アプリを作成し、SharePoint コネクタが含まれるリソース グループを選択します。

2. [トリガーとアクション] を開き、ロジック アプリ デザイナーを開いてフローを構成します。右側のギャラリーの [最近使用した項目] セクションに、SharePoint コネクタが表示されます。それを選択します。

3. ロジック アプリの先頭で SharePoint コネクタを選択した場合、SharePoint コネクタはトリガーとして動作します。それ以外の場合は、コネクタを使用する SharePoint アカウントに対するアクションとして動作します。 

4. SharePoint Online コネクタを使用する場合や SharePoint Server コネクタの認証モードが OAuth2 の場合、認証および承認ロジック アプリを使用して自動的に操作を実行することが必要になる場合があります。承認を開始するには SharePoint コネクタの [承認] をクリックします。 

	![][3]

5. [承認] をクリックすると SharePoint の認証ダイアログ ボックスが開きます。操作を実行する SharePoint アカウントのログイン詳細を指定します。 

	![][4]
6. 代わりに操作を行わせるために、アカウントにロジック アプリのアクセス権を付与します。 

	![][5]

7. SharePoint コネクタがトリガーとして構成されている場合はトリガーが表示され、それ以外の場合はアクションの一覧が表示されます。実行する適切な操作を選択できます。  

	![][6]

	<b>ドキュメント ライブラリ用に構成された相対 URL</b><br><br>

	![][7]

	<b>ドキュメント リスト用に構成された相対 URL</b>

	<b>注:</b> 次のトリガーでは、ユーザーがコネクタのパッケージ設定で  'Shared Documents, Lists/Task' を指定したものと想定しています。ここで、 'Shared Documents' はドキュメント ライブラリ、 'Lists/Task' はリストです。 

##  トリガー
ロジック アプリを開始する場合は、トリガーを使用します。 

### 1.	Shared Documents 内の新しいドキュメント (JSON)
このトリガーは、 'Shared Documents' 内で新しいドキュメントが利用可能になると発生します。 

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ビュー名</td>
    <td>いいえ</td>
    <td>取得するドキュメントをフィルター選択するための有効なビューを指定します。例:  'Approved Orders'。既存のすべてのドキュメントを処理するには、このフィールドを空のままにします。 </td>
  </tr>
  <tr>
    <td>アーカイブの場所</td>
    <td>いいえ</td>
    <td>処理されたドキュメントのアーカイブ先の有効なフォルダー URL を、SharePoint サイトの相対位置で指定します。 </td>
  </tr>
  <tr>
    <td>アーカイブで上書きする</td>
    <td>いいえ</td>
    <td>アーカイブのパスにファイルが既に存在する場合にファイルを上書きするには、このオプションをオンにします。 </td>
  </tr>
  <tr>
    <td>CAML クエリ</td>
    <td>いいえ。詳細設定</td>
    <td>ドキュメントをフィルター選択するための、有効な CAML クエリを指定します。例:<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**出力:**
<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>名前  </td>
    <td>ドキュメントの名前。</td>
  </tr>
  <tr>
    <td>コンテンツ</td>
    <td>ドキュメントのコンテンツ。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>メッセージのコンテンツ転送エンコード。("none"|"base64")</
  </tr>
</table>


注:ドキュメント項目のすべての列が  'Advanced' 出力プロパティに表示されます。


###2.Tasks の新しい項目 (JSON)
このトリガーは、 'Tasks' リストに新しい項目が追加されると発生します。

**入力:**
<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
 </tr>
  <tr>
    <td>ビュー名</td>
    <td>いいえ</td>
    <td>リスト内の項目をフィルター選択するための有効なビューを指定します。例:  'Approved Orders'。すべての新しい項目を処理するには、このフィールドを空のままにします。 </td>
  </tr>
  <tr>
    <td>アーカイブの場所</td>
    <td>いいえ</td>
    <td>処理されたリスト項目のアーカイブ先の有効なフォルダー URL を、SharePoint サイトの相対位置で指定します。 </td>
  </tr>
  <tr>
    <td>CAML クエリ</td>
    <td>いいえ。詳細設定</td>
    <td>リスト項目をフィルター選択するための、有効な CAML クエリを指定します。例:<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>リスト内の列は、動的に設定され、出力パラメーターに表示されます。</td>
    <td> </td>
  </tr>

</table>


###3.Shared Documents 内の新しいドキュメント (XML)

このトリガーは、 'Shared Documents' 内で新しいドキュメントが利用可能になると発生します。新しいドキュメントが XML メッセージとして返されます。

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ビュー名</td>
    <td>いいえ</td>
    <td>取得するドキュメントをフィルター選択するための有効なビューを指定します。例:  'Approved Orders'。既存のすべてのドキュメントを処理するには、このフィールドを空のままにします。 </td>
  </tr>
  <tr>
    <td>アーカイブの場所</td>
    <td>いいえ</td>
    <td>処理されたドキュメントのアーカイブ先の有効なフォルダー URL を、SharePoint サイトの相対位置で指定します。 </td>
  </tr>
  <tr>
    <td>アーカイブで上書きする</td>
    <td>いいえ</td>
    <td>アーカイブのパスにファイルが既に存在する場合にファイルを上書きするには、このオプションをオンにします。 </td>
  </tr>
  <tr>
    <td>CAML クエリ</td>
    <td>いいえ。詳細設定</td>
    <td>ドキュメントをフィルター選択するための、有効な CAML クエリを指定します。例:<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>コンテンツ</td>
    <td>ドキュメントのコンテンツ。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>メッセージのコンテンツ転送エンコード。("none"|"base64")</td>
  </tr>
</table>


###4.Tasks の新しい項目 (XML)

このトリガーは、 'Tasks' リストに新しい項目が追加されると発生します。新しいリスト項目が XML メッセージとして返されます。

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ビュー名</td>
    <td>いいえ</td>
    <td>リスト内の項目をフィルター選択するための有効なビューを指定します。例:  'Approved Orders'。すべての新しい項目を処理するには、このフィールドを空のままにします。 </td>
  </tr>
  <tr>
    <td>アーカイブの場所</td>
    <td>いいえ</td>
    <td>処理されたリスト項目のアーカイブ先の有効なフォルダー URL を、SharePoint サイトの相対位置で指定します。 </td>
  </tr>
  <tr>
    <td>CAML クエリ</td>
    <td>いいえ。詳細設定</td>
    <td>リスト項目をフィルター選択するための、有効な CAML クエリを指定します。例:<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>コンテンツ</td>
    <td>ドキュメントのコンテンツ。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>メッセージのコンテンツ転送エンコード。("none"|"base64")</td>
  </tr>
</table>


##  操作
次のアクションでは、ユーザーがコネクタのパッケージ設定で  'Shared Documents, Lists/Task' を指定したものと想定しています。ここで、 'Shared Documents' はドキュメント ライブラリ、 'Lists/Task' はリストです。 

###1.Shared Documents にアップロード (JSON)

このアクションは、新しいドキュメントを  'Shared Documents' にアップロードします。入力は、ドキュメント ライブラリのすべての列フィールドを含む、厳密に型指定された JSON オブジェクトです。

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
 </tr>
  <tr>
    <td>名前</td>
    <td>あり</td>
    <td>ドキュメントの名前。</td>
  </tr>
  <tr>
    <td>コンテンツ</td>
    <td>あり</td>
    <td>ドキュメントのコンテンツ。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>あり</td>
    <td>メッセージのコンテンツ転送エンコード。("none"|"base64")</td>
  </tr>
  <tr>
    <td>強制的に上書きする</td>
    <td>あり</td>
    <td>TRUE に設定した場合、指定された名前のドキュメントが存在したときにドキュメントが上書きされます。</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>あり</td>
    <td>ドキュメント ライブラリにドキュメントを追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>あり</td>
    <td>ドキュメント ライブラリにドキュメントを追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>いいえ。詳細設定</td>
    <td>ドキュメント ライブラリにドキュメントを追加するためのオプション パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>いいえ。詳細設定</td>
    <td>ドキュメント ライブラリにドキュメントを追加するためのオプション パラメーターの 1 つです。</td>
  </tr>
</table>

<b>注:</b> ドキュメント ライブラリのすべてのパラメーターは動的に設定されます。必須パラメーターは表示されていますが、オプションのパラメーターは詳細設定セクションに配置されています。


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>ドキュメント ライブラリに追加されたドキュメントの ItemId。</td>
  </tr>
  <tr>
    <td>ステータス</td>
    <td>ドキュメントが正常にアップロードされると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


 

###2.Shared Documents から取得 (JSON)
このアクションは、相対 URL (フォルダー構造) で指定されたドキュメントをドキュメント ライブラリから取得します。


**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ドキュメントの相対 URI</td>
    <td>いいえ</td>
    <td>ドキュメントの URL を  'Shared Documents' の相対位置で指定します。例: myspec1,myfolder/orders</td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>コンテンツ</td>
    <td>ドキュメントのコンテンツ</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>メッセージのコンテンツ転送エンコード。("none"|"base64")</td>
  </tr>
  <tr>
    <td>ステータス</td>
    <td>アクションが正常に実行されると、状態コード 200 (OK) が返されます。</td>
  </tr>
  <tr>
    <td>Param1*</td>
    <td>ドキュメント ライブラリ内のドキュメントの必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>Param2*</td>
    <td>ドキュメント ライブラリ内のドキュメントの必須パラメーターの 1 つです。</td>
  </tr>
</table>

<b>注:</b> ドキュメント ライブラリのすべてのパラメーターは動的に設定されます。これらのパラメーターは詳細設定セクションに配置されています。

 

###3.Shared Documents から削除

このアクションは、相対 URL (フォルダー構造) で指定されたドキュメントをドキュメント ライブラリから削除します。

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ドキュメントの相対 URI</td>
    <td>いいえ</td>
    <td>ドキュメントの URL を  'Shared Documents' の相対位置で指定します。例: myspec1,myfolder/orders</td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ステータス</td>
    <td>アクションが正常に実行されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


###4.Tasks への挿入 (JSON)

このアクションは、項目リストに項目を追加します。

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>あり</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>あり</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>いいえ。詳細設定</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>いいえ。詳細設定</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
</table>


<b>注:</b>  'List' のすべてのパラメーターは動的に設定されます。必須パラメーターは表示されていますが、オプションのパラメーターは詳細設定セクションに配置されています。

 
**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>追加されたリスト項目の ItemId。</td>
  </tr>
  <tr>
    <td>ステータス</td>
    <td>リスト項目が正常に挿入されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


###5.Tasks を更新 (JSON)

このアクションは、項目リストの項目を更新します。

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>あり</td>
    <td>リスト項目の ItemId。</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>いいえ</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>いいえ</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>いいえ。詳細設定</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>いいえ。詳細設定</td>
    <td>リストに項目を追加するための必須パラメーターの 1 つです。</td>
  </tr>
</table>

<b>注:</b>  'List' のすべてのパラメーターは動的に設定されます。必須パラメーターは表示されていますが、オプションのパラメーターは詳細設定セクションに配置されています。


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ステータス  </td>
    <td>リスト項目が正常に更新されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


###6.Tasks から項目を取得 (JSON)

このアクションは、項目リストから項目を取得します。


**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>あり</td>
    <td>リスト項目の ItemId。</td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>Column1*</td>
    <td>リストのパラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>Column2*</td>
    <td>リストのパラメーターの 1 つです。</td>
  </tr>
  <tr>
    <td>ステータス</td>
    <td>アクションが正常に実行されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>

<b>注:</b> リスト内の列は、動的に設定され、出力パラメーターに表示されます。


###7.Tasks から項目を削除

このアクションは、項目リストから項目を削除します。

 
**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>あり</td>
    <td>リスト項目の ItemId。</td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ステータス  </td>
    <td>リスト項目が正常に削除されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


###8.Shared Documents 内のドキュメントの一覧を取得 (JSON)

このアクションは、ドキュメント ライブラリ内のすべてのドキュメントの一覧を取得します。View または CAML クエリを使用してドキュメントをフィルター選択できます。  

 
**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ビュー名</td>
    <td>いいえ</td>
    <td>取得するドキュメントをフィルター選択するための有効なビューを指定します。例:  'Approved Orders'。既存のすべてのドキュメントを処理するには、このフィールドを空のままにします。 </td>
  </tr>
  <tr>
    <td>CAML クエリ</td>
    <td>いいえ</td>
    <td>ドキュメントをフィルター選択するための、有効な CAML クエリを指定します。例:<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ドキュメント</td>
    <td>すべてのドキュメントの配列。それぞれのドキュメントには、次のフィールドがあります。 <br><br>
	ドキュメント []<br>
	名前<br>
	項目 ID<br>
	項目の完全な URL<br>
	高度<br>
	項目編集 URL<br>
	リスト名<br>
	リストの完全な URL<br>
	</td>
  </tr>
  <tr>
    <td>ステータス  </td>
    <td>リスト項目が正常に挿入されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


###9.Shared Documents にアップロード (XML)

このアクションは、新しいドキュメントを  'Shared Documents' にアップロードします。入力ドキュメントは XML ペイロードである必要があります。アクションの応答は XML ペイロードになります。
 

**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>名前</td>
    <td>あり</td>
    <td>ドキュメントの名前。</td>
  </tr>
  <tr>
    <td>コンテンツ</td>
    <td>あり</td>
    <td>ドキュメントのコンテンツ。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>あり</td>
    <td>メッセージのコンテンツ転送エンコード。("none"|"base64")</td>
  </tr>
  <tr>
    <td>強制的に上書きする</td>
    <td>あり</td>
    <td>TRUE に設定した場合、指定された名前のドキュメントが存在したときにドキュメントが上書きされます。</td>
  </tr>
</table>
 

**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>出力 XML</td>
    <td>アップロード アクションの応答 (XML 形式)。</td>
  </tr>
  <tr>
    <td>ステータス  </td>
    <td>ドキュメントが正常にアップロードされると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>

###10.Shared Documents から取得 (XML)

このアクションは、相対 URL (フォルダー構造) で指定されたドキュメントをドキュメント ライブラリから取得します。

 
**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ドキュメントの相対 URI</td>
    <td>いいえ</td>
    <td>ドキュメントの URL を  'Shared Documents' の相対位置で指定します。例: myspec1,myfolder/orders</td>
  </tr>
  <tr>
    <td>ファイルの種類</td>
    <td>あり</td>
    <td>ファイルがバイナリ ファイルであるかまたはテキスト ファイルであるかを指定します。</td>
  </tr>
</table>


**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>出力 XML</td>
    <td>ドキュメントのコンテンツ。</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>メッセージのコンテンツ転送エンコード。("none"|"base64")</td>
  </tr>
  <tr>
    <td>ステータス</td>
    <td>アクションが正常に実行されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>

###11.Tasks への挿入 (XML)

このアクションは、項目リストに項目を追加します。入力は XML ペイロードである必要があります。

** 入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>入力 XML</td>
    <td>あり</td>
    <td>挿入するリスト項目のフィールドの値が含まれた XML メッセージ。変換 API アプリを使用して、XML メッセージを生成できます。</td>
  </tr>
</table>
 
<b>注:</b>  'List' のすべてのパラメーターは動的に設定されます。必須パラメーターは表示されていますが、オプションのパラメーターは詳細設定セクションに配置されています。

 
**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>追加されたリスト項目の ItemId。</td>
  </tr>
  <tr>
    <td>ステータス  </td>
    <td>リスト項目が正常に挿入されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


###12.Tasks を更新 (XML)

このアクションは、項目リストの項目を更新します。入力は XML ペイロードである必要があります。


**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>あり</td>
    <td>リスト項目の ItemId。</td>
  </tr>
  <tr>
    <td>入力 XML</td>
    <td>あり</td>
    <td>挿入するリスト項目のフィールドの値が含まれた XML メッセージ。変換 API アプリを使用して、XML メッセージを生成できます。</td>
  </tr>
</table>

<b>注:</b>  'List' のすべてのパラメーターは動的に設定されます。必須パラメーターは表示されていますが、オプションのパラメーターは詳細設定セクションに配置されています。

 
**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ステータス  </td>
    <td>リスト項目が正常に更新されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


###13.Tasks から項目を取得 (XML)

このアクションは、項目リストから項目を取得します。


**入力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>あり</td>
    <td>リスト項目の ItemId。</td>
  </tr>
</table>

**出力:**

<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>出力 XML</td>
    <td>選択されたリスト項目のフィールドの値が含まれた XML メッセージ。 </td>
  </tr>
  <tr>
    <td>ステータス  </td>
    <td>アクションが正常に実行されると、状態コード 200 (OK) が返されます。</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=49-->