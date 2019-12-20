---
title: マッピング データ フロー スクリプト
description: Data Factory のデータ フロー スクリプトのコードビハインド言語の概要
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/10/2019
ms.openlocfilehash: d861a4355158dfe18ac3aa40a7f98dc11ebda90b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930255"
---
# <a name="data-flow-script-dfs"></a>データ フロー スクリプト (DFS)

データ フロー スクリプト (DFS) とは、マッピング データ フローに含まれている変換を実行するために使用される、コーディング言語に似た基礎になっているメタデータです。 すべての変換は、ジョブを正しく実行するために必要な情報を提供する一連のプロパティによって表されます。 スクリプトは、ブラウザー UI の上部のリボンにある [スクリプト] ボタンをクリックして、ADF から表示および編集できます。

![[スクリプト] ボタン](media/data-flow/scriptbutton.png "[スクリプト] ボタン")

たとえば、ソース変換の `allowSchemaDrift: true,` は、それがスキーマ プロジェクションに含まれていない場合でも、データ フロー内のソース データセットのすべての列を含めるようにサービスに指示します。

## <a name="use-cases"></a>ユース ケース
DFS は、ユーザー インターフェイスによって自動的に生成されます。 スクリプトを表示してカスタマイズするには、[スクリプト] ボタンをクリックします。 ADF UI を使用せずにスクリプトを生成し、それを PowerShell コマンドレットに渡すこともできます。 複雑なデータ フローをデバッグする場合、ご自分のフローの UI グラフ表現をスキャンするのではなく、スクリプトのコードビハインドをスキャンした方が簡単な場合があります。

次にいくつかのユース ケースの例を示します。
- 非常に類似したデータ フローをプログラミングして多数作成する場合。つまり、データ フローを "スタンプアウト" する場合。
- UI で管理するのが困難な、または検証の問題が発生する可能性がある複雑な式の場合。
- 実行時に返されるさまざまなエラーをデバッグおよび理解したい場合。

PowerShell または API で使用するデータ フロー スクリプトを作成する場合は、書式設定されたテキストを 1 行に折りたたむ必要があります。 これらのタブと改行はエスケープ文字として保持できます。 ただし、テキストは JSON プロパティ内に収まるように書式設定する必要があります。 下部のスクリプト エディターの UI には、スクリプトを 1 行に書式設定するボタンがあります。

![コピー ボタン](media/data-flow/copybutton.png "コピー ボタン")

## <a name="how-to-add-transforms"></a>変換を追加する方法
変換を追加するには、主な変換データを追加し、入力ストリームを再ルーティングし、出力ストリームを再ルーティングするという 3 つの基本的な手順を行う必要があります。 これは、例で簡単に確認できます。
まず、次のようにデータ フローをシンクする単純なソースで開始します。

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

派生変換を追加する場合は、まず、`upperCaseTitle` という新しい大文字の列を追加する主な変換テキストの単純な式を作成する必要があります。
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

次いで、この既存の DFS を使用して変換を追加します。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

次に、新しい変換をどの変換の後に入れるか (この場合は `source1`) を指定し、そのストリームの名前を新しい変換にコピーして、受信ストリームを再ルーティングします。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

最後に、この新しい変換の後に入れる変換を指定し、その入力ストリーム (この例では `sink1`) を新しい変換の出力ストリーム名に置き換えます。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS の基礎
DFS は、ソース、シンクなど、新しい列の追加、データのフィルター処理、データの結合など多数のことを実行できる一連の変換の組み合わせで構成されています。 通常スクリプトは、1 つ以上のソースで開始し、その後に多数の変換が続き、1 つ以上のシンクで終わります。

すべてのソースの基本構成は同じです。
```
source(
  source properties
) ~> source_name
```

たとえば、3 つの列 (movieId、title、genres) がある単純なソースは次のようになります。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

ソース以外のすべての変換の基本構成は同じです。
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

たとえば、列 (title) を受け取り、それを大文字のバージョンで上書きする単純な派生変換は、次のようになります。
```
source1 derive(
  title = upper(title)
) ~> derive1
```

スキーマのないシンクは、単純に次のようになります。
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>次の手順

データ フローの詳細については、まず[データ フロー概要に関する記事](concepts-data-flow-overview.md)を参照してください。
