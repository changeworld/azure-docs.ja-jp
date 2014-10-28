# Azure SDK for Java のダウンロード

## Azure Client Libraries for Java - 手動ダウンロード

Azure Libraries for Java は、[Apache License Version 2.0][Apache License Version 2.0] に準拠して配布されます。ライブラリの ZIP ファイルおよびすべての依存関係については、[ここ][ここ]をクリックしてください。これは Microsoft Open Technologies, Inc. によって公開されています。ライセンスおよびその他の情報については、ZIP 内の license.txt と ThirdPartyNotices.txt ファイルを参照してください。

## Azure Libraries for Java - Maven

プロジェクトが既に Maven を使用してビルドする設定になっている場合には、pom.xml ファイルに次の依存関係を追加します。

    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

`<version>` 要素内の *n.n.n* を有効なバージョン番号に置き換えてください。このバージョン番号は、[Maven の Azure ライブラリ リポジトリ][Maven の Azure ライブラリ リポジトリ]から入手できます。

  [Apache License Version 2.0]: http://www.apache.org/licenses/LICENSE-2.0.html
  [ここ]: http://go.microsoft.com/fwlink/?LinkId=253887
  [Maven の Azure ライブラリ リポジトリ]: http://go.microsoft.com/fwlink/?LinkID=286274
