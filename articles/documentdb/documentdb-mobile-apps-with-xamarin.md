---
title: "Xamarin と Azure DocumentDB を使用したモバイル アプリケーションの構築 | Microsoft Docs"
description: "Azure DocumentDB を使用して Xamarin iOS、Android、またはフォーム アプリケーションを作成するチュートリアル。 DocumentDB は、モバイル アプリ向けのきわめて高速な世界規模のクラウド データベースです。"
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4a783397b038b7545bbbfc45813db3d9973c5e19
ms.lasthandoff: 03/22/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Xamarin と Azure DocumentDB を使用したモバイル アプリケーションの構築
ほとんどのモバイル アプリでは、クラウドにデータを格納する必要があります。Azure DocumentDB は、モバイル アプリ向けのクラウド データベースです。 モバイル開発者が必要なすべてのものを備えています。 必要に応じて拡大縮小できる完全に管理されたサービスとしての NoSQL データベースで、ユーザーが世界中どこに行っても、アプリケーションにデータを透過的に提供できます。 [Azure DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) を使用すると、中間層なしで、Xamarin モバイル アプリが DocumentDB と直接やり取りすることができます。

この記事では、Xamarin と DocumentDB を使用したモバイル アプリの構築についてのチュートリアルを提供します。 ユーザーとアクセス許可を管理する方法を含め、このチュートリアルの完全なソース コードについては、[Github の Xamarin と DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) に関するページを参照してください。

## <a name="documentdb-capabilities-for-mobile-apps"></a>モバイル アプリ向けの DocumentDB の機能
DocumentDB は、モバイル アプリ開発者向けの次の主要な機能を備えています。

![モバイル アプリ向けの DocumentDB の機能](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* スキーマなしのデータに対するリッチ クエリ。 DocumentDB では、データをスキーマなしの JSON ドキュメントとして異種コレクションに格納し、[高速なリッチ クエリ](documentdb-sql-query.md)を提供します。スキーマやインデックスについて心配する必要はありません。
* 高速。 保証。 DocumentDB を使用してドキュメントを読み書きするのには、数ミリ秒しかかかりません。 開発者は必要なスループットを指定でき、DocumentDB では 99.99% の SLA でそのスループットが保証されます。
* 無限のスケール。 DocumentDB のコレクションは、[アプリの規模の拡大に合わせて増加](documentdb-partition-data.md)します。 小さいデータ サイズと 1 秒あたり数百件の要求から開始し、大きいデータ サイズ、1 秒あたり数千万や数億件の要求のスループット、ペタバイトのデータに任意に拡張できます。
* グローバルに分散。 モバイル アプリ ユーザーは外に出かけ、世界中を飛び回ることもよくあります。 DocumentDB は、[グローバル分散データベース](documentdb-distribute-data-globally.md)です。地図上で 1 回クリックするだけで、ユーザーがどこにいてもデータが提供されます。
* 組み込みの豊富な承認機能。 DocumentDB では、複雑なカスタムの承認コードを作成せずに、[ユーザーごとのデータ](https://aka.ms/documentdb-xamarin-todouser)やマルチ ユーザー共有データのような一般的なパターンを簡単に実装できます。
* 地理空間クエリ。 現在、多くのモバイル アプリが地理的な場所に応じたエクスペリエンスを提供しています。 DocumentDB では、[地理空間型](documentdb-geospatial.md)を高いレベルでサポートすることにより、そうしたエクスペリエンスを簡単に実現できます。
* バイナリの添付ファイル。 アプリ データには、バイナリ BLOB が含まれていることがよくあります。 添付ファイルがネイティブでサポートされているため、DocumentDB をアプリ データのワンストップ ショップとして簡単に使用できます。

## <a name="documentdb-and-xamarin-tutorial"></a>DocumentDB と Xamarin のチュートリアル
次のチュートリアルでは、5 つの簡単な手順で Xamarin と DocumentDB を使用してモバイル アプリケーションを構築する方法について説明します。 このチュートリアルの完全なソース コードについては、[Github の Xamarin と DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) に関するページを参照してください。

### <a name="get-started"></a>作業開始
DocumentDB の利用を開始するのは簡単です。 Azure Portal に移動し、新しい DocumentDB アカウントを作成します。次に、[クイック スタート] タブに移動し、Xamarin フォームの "to do list" サンプルをダウンロードすると、DocumentDB アカウントに接続されます。 

![モバイル アプリ向けの DocumentDB の [クイック スタート]](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

または、既存の Xamarin アプリがある場合は、単純にこの [DocumentDB NuGet パッケージ](documentdb-sdk-dotnet-core.md)を追加できます。 DocumentDB では、Xamarin.IOS、Xamarin.Android、および Xamarin Forms の共有ライブラリをサポートしています。

### <a name="work-with-data"></a>データの処理
DocumentDB では、データ レコードをスキーマなしの JSON ドキュメントとして異種コレクションに格納します。 同じコレクションに異なる構造のドキュメントを格納できます。

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Xamarin プロジェクトでは、スキーマなしのデータに対して統合言語クエリを使用することができます。

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Add users
多くの入門サンプルと同様に、ダウンロードした DocumentDB のサンプルでは、アプリのコード内にハードコードされたマスター キーを使用してサービスに対して認証を行います。 この方法は、ローカル エミュレーター以外でアプリを実行する場合にはお勧めしません。 攻撃者がマスター キーを入手すると、DocumentDB アカウントの全データのセキュリティが侵害されます。 代わりに、ログインしているユーザーのレコードにのみアプリがアクセスできるようにします。 DocumentDB では、コレクション、パーティション キーでグループ化された一連のドキュメント、または特定のドキュメントに対する読み取りアクセスまたは読み取り/書き込みアクセスをアプリケーションに付与できます。 

たとえば、この Todo リスト アプリをマルチ ユーザー "to do list" アプリに変更する方法を次に示します。 

* Facebook、Active Directory などのプロバイダーを使用したログインをアプリに追加します。
* パーティション キーとして /userId を使用して DocumentDB UserItems コレクションを作成します。 コレクションのパーティション キーを指定すると、高速なクエリを提供する一方で、アプリのユーザー数の増加に応じて DocumentDB を無限に拡張できます。
* DocumentDB リソース トークン ブローカーを追加します。これは、ユーザーを認証し、ログイン ユーザーに対してユーザーのパーティション内のドキュメントにのみアクセスできる有効期間の短いトークンを発行する簡単な Web API です。 この例では、App Service でリソース トークン ブローカーをホストします。
* Facebook を使用してリソース トークン ブローカーに対して認証し、ログインした Facebook ユーザーのリソース トークンを要求して、UserItems コレクション内のユーザー データにアクセスするようにアプリを変更します。  

このパターンの完全なコード サンプルについては、[Github のリソース トークン ブローカー](http://aka.ms/documentdb-xamarin-todouser)に関するページを参照してください。 次の図には、このソリューションを示しています。

![DocumentDB ユーザーおよびアクセス許可のブローカー](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

ここで、2 人のユーザーが同じ "to do list" にアクセスできるようにする場合は、リソース トークン ブローカーでアクセス トークンにアクセス許可を追加します。

### <a name="scale-on-demand"></a>オンデマンドでの拡張
DocumentDB は、管理されたサービスとしてのデータベースです。 ユーザー ベースが拡大しても、VM のプロビジョニングやコア数の増加について心配する必要はありません。 必要なのは、アプリに必要な 1 秒あたりの操作数 (スループット) を DocumentDB に指示することだけです。 ポータルの [スケール] タブで、1 秒あたりの要求ユニット (RU) と呼ばれるスループットの尺度を使用して、スループットを指定できます。 たとえば、1 KB のドキュメントの読み取り操作には 1 RU が必要です。 また、"スループット" メトリックのアラートを追加して、トラフィックの増加を監視し、アラートの発生に応じてスループットをプログラムで変更することもできます。

![オンデマンドでの DocumentDB のスループットの拡張](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>世界規模化
アプリが普及すると、ユーザーが世界中に広がる可能性があります。 また、DocumentDB コレクションを作成した Azure データ センターに隕石が落ちた場合に備えておくことが必要なこともあります。 Azure Portal で、DocumentDB アカウントにアクセスし、地図上でクリックすると、世界中の任意の数のリージョンにデータを継続的にレプリケートできます。 こうすると、ユーザーがどこにいてもデータを利用できるようにし、万一に備えたフェールオーバー ポリシーを追加することができます。

![地理的リージョンをまたいだ DocumentDB の拡張](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

お疲れさまでした。 ソリューションが完成し、モバイル アプリで Xamarin と DocumentDB を利用できるようになりました。 同様のパターンは、DocumentDB JavaScript SDK を使用した Cordova アプリや、DocumentDB REST API を使用したネイティブ iOS/Android アプリで使用することができます。

## <a name="next-steps"></a>次のステップ
* [Github の Xamarin と DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) のソース コードを確認する。
* [DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) をダウンロードする。
* [.NET アプリケーション](documentdb-dotnet-samples.md)向けの他のコード サンプルを探す。
* [DocumentDB のリッチ クエリ機能](documentdb-sql-query.md)について確認する。
* [DocumentDB の地理空間サポート](documentdb-geospatial.md)について確認する。




