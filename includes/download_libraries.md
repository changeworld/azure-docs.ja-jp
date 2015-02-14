#Azure SDK for Java のダウンロード

##Azure Client Libraries for Java - 手動ダウンロード

Azure Libraries for Java are は、[Apache License Version 2.0][license] に準拠して配布されます。ライブラリの ZIP ファイルおよびすべての依存関係については、[ここ][zip-download]をクリックしてください。これは Microsoft Open Technologies, Inc. によって公開されています。ライセンスおよびその他の情報については、ZIP 内の license.txt と ThirdPartyNotices.txt ファイルを参照してください。

##Azure Libraries for Java - Maven

プロジェクトが既に Maven を使用してビルドする設定になっている場合には、pom.xml ファイルに次の依存関係を追加します。

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>



`<version>` 要素内の  *n.n.n* を有効なバージョン番号に置き換えてください。このバージョン番号は、[Maven の Azure ライブラリ リポジトリ](http://go.microsoft.com/fwlink/?LinkID=286274)から入手できます。

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]:  http://go.microsoft.com/fwlink/?LinkId=253887
<!--HONumber=42-->
