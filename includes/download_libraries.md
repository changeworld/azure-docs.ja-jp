## Azure Client Libraries for Java - 手動ダウンロード
Azure Libraries for Java は、[Apache License Version 2.0][license] に準拠して配布されます。ライブラリの ZIP ファイルおよびすべての依存関係については、[ここ][zip-download]をクリックしてください。これは Microsoft Open Technologies, Inc. によって公開されています。ライセンスおよびその他の情報については、ZIP 内の license.txt と ThirdPartyNotices.txt ファイルを参照してください。

## Azure Libraries for Java - Maven
プロジェクトが既に Maven を使用してビルドする設定になっている場合には、pom.xml ファイルに次の依存関係を追加します。注: Java 用 Azure ライブラリを使用する Maven プロジェクトを Eclipse で作成する方法については、「[Java 用 Azure 管理ライブライの概要][maven-getting-started]」を参照してください。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>0.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>0.6.0</version>
    </dependency>


この例の `<version>` 要素内のバージョン番号を、有効なバージョン番号に置き換えてください。有効なバージョン番号は、[Maven の Azure ライブラリ リポジトリ](http://go.microsoft.com/fwlink/?LinkID=286274)から入手できます。

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

<!---HONumber=Oct15_HO3-->