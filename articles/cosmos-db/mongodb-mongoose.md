---
title: Azure Cosmos DB で Mongoose フレームワークを使用する | Microsoft Docs
description: Node.js Mongoose アプリを Azure Cosmos DB に接続する方法について説明します。
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: sclyon
ms.openlocfilehash: aa178a24f0c36a1c5fb56b342141b066c150c7c3
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038388"
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB: Azure Cosmos DB で Mongoose フレームワークを使用する

このチュートリアルでは、Azure Cosmos DB にデータを格納するときに [Mongoose フレームワーク](http://mongoosejs.com/)を使用する方法について説明します。 このチュートリアルでは MongoDB API for Azure Cosmos DB を使用します。 知らない場合に備えて説明すると、Mongoose は、Node.js での MongoDB 用のオブジェクト モデル化フレームワークです。アプリケーション データをモデル化するための単純なスキーマベース ソリューションが提供されます。

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) バージョン v0.10.29 以降

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでください。

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Node.js アプリケーションをセットアップする

>[!Note]
> アプリケーションをセットアップするのではなくサンプル コードを確認するだけの場合は、このチュートリアルで使用される[サンプル](https://github.com/Azure-Samples/Mongoose_CosmosDB)を複製して、Node.js Mongoose アプリケーションを Azure Cosmos DB 上に構築します。

1. 選択したフォルダーに Node.js アプリケーションを作成するには、ノードのコマンド プロンプトで次のコマンドを実行します。

    ```npm init```

    質問に回答すると、プロジェクトが準備完了になります。

1. 新しいファイルをフォルダーに追加し、名前を ```index.js``` にします。
1. 次の ```npm install``` オプションのいずれかを使用して、必要なパッケージをインストールします。
    * Mongoose: ```npm install mongoose --save```
    * Dotenv (.env ファイルからシークレットを読み込む場合): ```npm install dotenv --save```

    >[!Note]
    > ```--save``` フラグによって、package.json ファイルに依存関係が追加されます。

1. index.js ファイルに依存関係をインポートします。
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Cosmos DB の接続文字列と Cosmos DB の名前を ```.env``` ファイルに追加します。

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Mongoose フレームワークを使用して Azure Cosmos DB に接続するには、次のコードを index.js の末尾に追加します。
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > ここで、環境変数は、'dotenv' npm パッケージを使用して process.env.{variableName} によって読み込まれます。

    Azure Cosmos DB に接続したら、Mongoose でのオブジェクト モデルのセットアップを開始できます。

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Azure Cosmos DB での Mongoose の使用に関する注意事項

Mongoose では、ユーザーが作成するモデルごとに、新しい MongoDB コレクションが自動的に作成されます。 ただし、Azure Cosmos DB のコレクションごとの課金モデルの場合に、データがほとんど移入されていない複数のオブジェクト モデルがあると、最もコスト効果の高い方法にならない可能性があります。

このチュートリアルでは 2 つのモデルを扱います。 最初は、コレクションごとに 1 種類のデータを格納するモデルについて説明します。 これは、Mongoose の標準の動作です。

Mongoose には、[ディスクリミネーター](http://mongoosejs.com/docs/discriminators.html)という概念もあります。 ディスクリミネーターはスキーマ継承メカニズムです。 これにより、基礎となる同一の MongoDB コレクション上で、スキーマが重複する複数のモデルを持つことが可能になります。

同一コレクション内にさまざまなデータ モデルを格納でき、クエリの際にはフィルター句を使用して必要なデータのみをプルダウンできます。

### <a name="one-collection-per-object-model"></a>オブジェクト モデルごとに 1 つのコレクション

既定の Mongoose の動作では、オブジェクト モデルを作成するたびに MongoDB コレクションが作成されます。 このセクションでは、Azure Cosmos DB 対応の MongoDB でこれがどのように実現されるかを説明します。 この方法を Azure Cosmos DB でお勧めするのは、大容量のデータを含むオブジェクト モデルがある場合です。 これは、Mongoose の既定の処理モデルであり、Mongoose に関する知識があれば知っている可能性があります。

1. 再び、```index.js``` を開きます。

1. 'Family' のスキーマ定義を作成します。

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. 'Family' のオブジェクトを作成します。

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. 最後に、オブジェクトを Azure Cosmos DB に保存します。 これによりコレクションが自動的に作成されます。

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. 次に、別のスキーマとオブジェクトを作成します。 ここでは、家族連れ向けの行楽地 (Vacation Destinations) に対応するものを作成します。
    1. 前回と同じように、まずスキーマを作成します。
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. サンプル オブジェクトを作成して保存します (このスキーマには複数のオブジェクトを追加できます)。
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. ここで、Azure Portal に移動すると、Azure Cosmos DB に 2 つのコレクションが作成されていることがわかります。

    ![Node.js チュートリアル - Azure Cosmos DB アカウントを示し、複数のコレクション名が強調表示されている Azure Portal のスクリーン ショット - Node データベース][mutiple-coll]

1. 最後に、Azure Cosmos DB からデータを読み取ります。 既定の Mongoose 処理モデルを使用しているため、読み取り方法は Mongoose の他の読み取りと同じです。

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Mongoose ディスクリミネーターを使用して 1 つのコレクションにデータを格納する

この方法では、各 Azure Cosmos DB コレクションのコストを最適化するために [Mongoose ディスクリミネーター](http://mongoosejs.com/docs/discriminators.html)を使用します。 ディスクリミネーターによって、識別するための "キー" を定義することができ、このキーを使用して、さまざまなオブジェクト モデルでの格納、識別、フィルター処理が可能になります。

ここでは、ベース オブジェクト モデルを作成し、識別キーを定義し、ベース モデルに拡張子として 'Family' と 'VacationDestinations' を追加します。

1. 基本構成を設定し、ディスクリミネーター キーを定義します。

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. 次は、共通オブジェクト モデルを定義します。

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. ここで 'Family' モデルを定義します。 ```mongoose.model```の代わりに ```commonModel.discriminator``` を使用していることに注意してください。 さらに、Mongoose スキーマに基本構成を追加します。 次のように、discriminatorKey は ```FamilyType``` になります。

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. 同様に、'VacationDestinations' に対してもう 1 つのスキーマを追加します。 ここでは、DiscriminatorKey は ```VacationDestinationsType``` です。

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. 最後に、モデルのオブジェクトを作成して保存します。
    1. 'Family' モデルにオブジェクトを追加します。
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. 次に、'VacationDestinations' モデルにオブジェクトを追加し、保存します。
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. ここで、Azure Portal に戻ると、```alldata``` というコレクションが 1 つだけがあり、'Family' と 'VacationDestinations' 両方のデータが含まれていることがわかります。

    ![Node.js チュートリアル - Azure Cosmos DB アカウントを示し、コレクション名が強調表示されている Azure Portal のスクリーン ショット - Node データベース][alldata]

1. また、各オブジェクトには ```__type``` と呼ばれる別の属性があることも確認できます。これが 2 つの異なるオブジェクト モデルを区別するために使用されます。

1. 最後に、Azure Cosmos DB に格納されているデータを読み取ります。 モデルに基づいたデータのフィルター処理は Mongoose によって行われます。 そのため、データを読み取るときにそれ以外の処理を行う必要はありません。 モデル (この場合は ```Family_common```)のみを指定すると、Mongoose が 'DiscriminatorKey' に基づいたフィルター処理を行います。

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

説明からわかるように、Mongoose ディスクリミネーターは容易に使用できます。 したがって、Mongoose フレームワークを使用するアプリがある場合、このチュートリアルを使用すれば、あまり多くの変更を必要とせずに、アプリケーションを Azure Cosmos DB 上の MongoDB API で実行できるようになります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

「[MongoDB の機能と構文に対する MongoDB API サポート](mongodb-feature-support.md)」で、Azure Cosmos DB MongoDB API でサポートされる MongoDB の操作、演算子、ステージ、コマンド、オプションについて学びます。

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
