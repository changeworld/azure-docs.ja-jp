<properties 
    pageTitle="MongoDB のプロトコル対応の DocumentDB アカウントに接続する | Microsoft Azure" 
    description="現在プレビューとして提供されている MongoDB のプロトコル対応の DocumentDB アカウントに接続する方法について説明します。 接続には、既存の MongoDB 接続文字列を使用します。" 
    keywords="mongodb 接続文字列"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>


# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>MongoDB のプロトコル対応の DocumentDB アカウントに接続する方法

MongoDB の標準的な接続文字列の URI 形式を使用して、MongoDB のプロトコル対応の Azure DocumentDB アカウントに接続する方法について説明します。  

## <a name="get-the-account's-connection-string-information"></a>アカウントの接続文字列情報を取得する

1. 新しいウィンドウで、 [Azure ポータル](https://portal.azure.com)にサインインします。
2. [アカウント] ブレードの**左側のナビゲーション** バーで、**[接続文字列]** をクリックします。 **[アカウント]** ブレードに移動するには、ジャンプバーで **[その他のサービス]** をクリックし、**[DocumentDB (NoSQL)]** をクリックして、MongoDB のプロトコル対応の DocumentDB アカウントを選択します。

    ![Screen shot of the All Settings blade](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. **[接続文字列情報]** ブレードが表示され、MongoDB のドライバーを使用してこのアカウントに接続するために必要なすべての情報 (あらかじめ構築された接続文字列も含む) が表示されます。

    ![Screen shot of the connection string blade](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>接続文字列の要件

DocumentDB は、MongoDB の標準的な接続文字列の URI 形式をサポートしていますが、特殊な要件がいくつかあることに注意してください。たとえば DocumentDB アカウントには、SSL による安全な通信と認証が要求されます。  そのため接続文字列は次の形式となります。

    mongodb://username:password@host:port/[database]?ssl=true

この文字列の値は、先ほど示した [接続文字列] ブレードで確認できます。

- ユーザー名 (必須)
    - DocumentDB アカウント名
- パスワード (必須)
    - DocumentDB アカウントのパスワード
- ホスト (必須)
    - DocumentDB アカウントの FQDN
- ポート (必須)
    - 10250
- データベース (省略可)
    - 対応する接続で使用される既定のデータベース
- ssl=true (必須)

たとえば、上の [接続文字列情報] に表示されているアカウントの場合、  有効な接続文字列は次のようになります。
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c#-driver-for-mongodb"></a>MongoDB の C# ドライバーでの接続
既に述べたように、すべての DocumentDB アカウントには、SSL による安全な通信と認証が要求されます。 MongoDB 接続文字列の URI 形式は、ssl=true のクエリ文字列パラメーターをサポートしていますが、MongoDB の C# ドライバーを使用する場合は、MongoClient を作成するときに MongoClientSettings オブジェクトを使用する必要があります。  次のコード スニペットは、先ほどのアカウント情報を使ってアカウントに接続し、"Tasks" データベースを操作する方法を示しています。

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>次のステップ


- MongoDB のプロトコル対応の DocumentDB アカウントで [MongoChef を使用](documentdb-mongodb-mongochef.md) する方法を確認します。
- MongoDB のプロトコル対応 DocumentDB の [サンプル](documentdb-mongodb-samples.md)を体験します。

 



<!--HONumber=Oct16_HO2-->


