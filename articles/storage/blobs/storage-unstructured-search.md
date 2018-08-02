---
title: Azure クラウド ストレージで非構造化データを検索する
description: Azure Search を使用した非構造化データの検索。
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: eba2ef280e60693cfd4402348fe61b122cdccdf6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399858"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>クラウド ストレージで非構造化データを検索する

このチュートリアルでは、Azure BLOB に格納されたデータを使用し、[Azure Search](../../search/search-what-is-azure-search.md) を使用して、非構造化データを検索する方法を説明します。 非構造化データは、事前に定義された方法で編成されていないか、データ モデルがないデータです。 たとえば、.txt ファイルなどです。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * リソース グループの作成
> * ストレージ アカウントの作成
> * コンテナーを作成する
> * データをコンテナーにアップロードする
> * ポータルから検索サービスを作成する
> * 検索サービスを使用してコンテナーを検索する

## <a name="download-the-sample"></a>サンプルのダウンロード

サンプル データ セットが用意されています。 **[clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** をダウンロードし、独自のフォルダーに解凍します。

サンプルは、[clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) から取得したテキスト ファイルで構成されています。 Azure を使用して検索する場合のテキスト ファイルの例として、それらを使用できます。

## <a name="log-in-to-azure"></a>Azure にログインする

[Azure Portal](http://portal.azure.com) にログインします。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の場所を提供します。

現在のところ、**BLOB** と **汎用**の 2 種類のストレージ アカウントがあります。 このチュートリアルでは、**汎用**ストレージ アカウントを作成します。

汎用ストレージ アカウントを作成するプロセスに慣れていない場合は、それを作成する方法を次に示します。

1. 左側のメニューから **[Storage Accounts] (ストレージ アカウント)** を選択し、**[Add] \(追加)** を選択します。

2. ストレージ アカウント用に一意の名前を入力します。 

3. **[Deployment model] (デプロイ モデル)** に **[Resource Manager] (リソース マネージャー)** を選択し、**[Account kind] \(アカウントの種類)** ドロップダウンから、**[General purpose] \(汎用)** を選択します。

4. **[Replication] \(レプリケーション)** ドロップダウンから、**[Locally-redundant storage (LRS)] (ローカル冗長ストレージ (LRS))** を選択します。

5. **[Resource group] (リソース グループ)** で、**[Create new] \(新規作成)** を選択し、一意の名前を入力します。

6. 適切なサブスクリプションを選択します。

7. 場所を選択し、**[Create] \(作成)** を選択します。

  ![非構造化検索](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーはフォルダーに似ており、BLOB の格納に使用します。

このチュートリアルでは、1 つのコンテナーを使用して、clinicaltrials.gov から取得したテキスト ファイルを格納します。

1. Azure Portal のストレージ アカウントに移動します。

2. **[Blob service]** で **[BLOB の参照]** を選択します。

3. 新しいコンテナーを追加します。

4. コンテナーに、"data" という名前を付けて、パブリック アクセス レベルに **[Container] \(コンテナー)** を選択します。

5. **[OK]** を選択してコンテナーを作成します。 

  ![非構造化検索](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>サンプル データのアップロード

サンプル データをアップロードできるコンテナーが用意されました。

1. コンテナーを選択し、**[Upload] \(アップロード)** を選択します。

2. [Files] \(ファイル) フィールドの横に描かれた青いフォルダー アイコンを選択し、サンプル データを抽出したローカル フォルダーを参照します。

3. 抽出したすべてのファイルを選択し、**[Open] \(開く)** を選択します。

4. **[Upload] \(アップロード)** を選択し、アップロード プロセスを開始します。

  ![非構造化検索](media/storage-unstructured-search/upload.png)

アップロード プロセスには、しばらく時間がかかることがあります。

それが完了したら、データ コンテナーに移動し、テキスト ファイルがアップロードされたことを確認します。

  ![非構造化検索](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Search Service の作成

Azure Search は、Web、モバイル、およびエンタープライズ アプリケーションに豊かなデータ検索機能を追加するための API とツールを開発者に提供する、サービスとしての検索クラウド ソリューションです。

検索サービスを作成するプロセスに慣れていない場合は、それを作成する方法を次に示します。

1. Azure Portal のストレージ アカウントに移動します。

2. 下へスクロールし、**[Blob service]** で、**[Add Azure Search] (Azure Search の追加)** をクリックします。

3. **[Import Data] \(データのインポート)** で、**[Pick your service] \(サービスの選択)** を選択します。

4. **[Click here to create a new search service] \(ここをクリックして新しい検索サービスを作成)** を選択します。

5. **[New Search Service] \(新しい検索サービス)** 内で、**[URL]** フィールドに検索サービスの一意の名前を入力します。

6. **[Resource group] (リソース グループ)** で、**[Use existing] \(既存を使用)** を選択し、先に作成したリソース グループを選択します。

7. **[Pricing tier] \(価格レベル)** で、**[Free] \(無料)** レベルを選択し、**[Select] \(選択)** をクリックします。

8. **[Create] \(作成)** を選択して、検索サービスを作成します。

  ![非構造化検索](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>検索サービスをコンテナーに接続する

Blob Storage に接続できる検索サービスが用意されました。 このセクションでは、データ ソースの選択、インデックスの作成、インデクサーの作成のプロセスについて説明します。

1. ストレージ アカウントに移動します。

2. **[Blob service]** で、**[Add Azure Search] (Azure Search の追加)** を選択します。

3. **[Import Data] \(データのインポート)** 内で **[Search Service] \(検索サービス)** を選択し、前のセクションで作成した検索サービスをクリックします。 これにより、**[New data source] (新しいデータ ソース)** が開きます。

### <a name="new-data-source"></a>新しいデータ ソース

  データ ソースは、インデックスを作成するデータとデータにアクセスする方法を指定します。 データ ソースは、同じ検索サービスで、何回も使用できます。

1. データ ソースの名前を入力します。 **[Data to extract] \(抽出されるデータ)** で、**[Content and Metadata] \(コンテンツとメタデータ)** を選択します。 データ ソースは、インデックスを作成する BLOB の部分を指定します。
    
    a. 独自の将来のシナリオでは、**[Storage metadata only] \(ストレージのメタデータのみ)** を選択することもできます。 これを選択するのは、インデックスを作成するデータを標準 BLOB プロパティまたはユーザー定義プロパティに制限する場合などです。
    
    b. **[All metadata] (すべてのメタデータ)** を選択して、標準 BLOB プロパティおよび*すべて*のコンテンツ タイプ固有のメタデータを取得することもできます。 

2. 使用している BLOB はテキスト ファイルであるため、**[Parsing Mode] \(解析モード)** を **[Text] \(テキスト)** に設定します。
    
    a. 将来の独自のシナリオで、BLOB の内容によって、[他の解析モード](../../search/search-howto-indexing-azure-blob-storage.md)を選択したい場合もあるかもしれません。

  ![非構造化検索](media/storage-unstructured-search/datasources.png)

3. **[Storage Container] (ストレージ コンテナー)** を選択し、使用可能なストレージ アカウントを一覧表示します。

4. ストレージ アカウントを選択し、以前に作成したコンテナーを選択します。

5. **[Select] \(選択)** をクリックして、**[New data source] (新しいデータ ソース)** に戻り、**[OK]** を選択して続行します。

  ![非構造化検索](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>インデックスの構成

  インデックスは、検索可能なデータ ソースからのフィールドのコレクションです。 インデックスは、検索サービスに、データを検索する方法を認識させる方法です。

1. **[Import data] \(データのインポート)** で、**[Customize target index] \(対象インデックスをカスタマイズ)** を選択します。
 
2. **[Index name] \(インデックス名)** フィールドに、インデックスの名前を入力します。

3. **[metadata_storage_name]** で **[Retrievable]** 属性のチェックボックスを選択します。

  ![非構造化検索](media/storage-unstructured-search/valuestoselect.png)

4. **[OK]** をクリックすると、**[Create an Indexer] \(インデクサーの作成)** が表示されます。

インデックスのパラメーターとそれらのパラメーターを指定する属性は重要です。 パラメーターは*どの*データを格納するか、属性は*どのように*そのデータを格納するかを指定します。

**[FIELD NAME] \(フィールド名)** 列に、パラメーターが含まれます。 次の表に、使用可能な属性とその説明の一覧を提供します。

### <a name="field-attributes"></a>フィールド属性
| 属性 | 説明 |
| --- | --- |
| *キー* |ドキュメント検索に使用される各ドキュメントの一意の ID を提供する文字列です。 各インデックスに、1 つのキーが必要です。 1 つのフィールドだけをキーにすることができ、その型を Edm.String に設定する必要があります。 |
| *Retrievable* |検索結果でフィールドを返すことができるかどうかを設定します。 |
| *Filterable* |フィルター クエリでフィールドを使用できるようにします。 |
| *Sortable* |このフィールドを使ってクエリで検索結果を並べ替えられるようにします。 |
| *Facetable* |ユーザー自律フィルター処理のファセット ナビゲーション構造でフィールドを使用できるようにします。 通常は、複数のドキュメント (たとえば、1 つのブランドやサービス カテゴリに属する複数のドキュメント) をまとめてグループ化するために使用できる、反復する値があるフィールドが、ファセットとして最適です。 |
| *Searchable* |フィールドをフルテキスト検索可能としてマークします。 |


### <a name="create-an-indexer"></a>インデクサーの作成
    
  インデクサーはデータ ソースを検索インデックスに接続し、データのインデックスの再作成のスケジュールを提供します。

1. **[Name] \(名前)** フィールドに名前を入力し、**[OK]** を選択します。

  ![非構造化検索](media/storage-unstructured-search/exindexer.png)

2. **[Import Data] \(データのインポート)** に戻り、**[OK]** を選択して、接続プロセスを完了します。

BLOB を検索サービスに正常に接続しました。 ポータルで、インデックスに入力されたことが表示されるまで数分かかります。 ただし、検索サービスは、すぐにインデックス作成を開始するため、ただちに検索を開始できます。

## <a name="search-your-text-files"></a>テキスト ファイルの検索

ファイルを検索するには、新しく作成した検索サービスのインデックス内で Search エクスプローラーを開きます。

次の手順で、Search エクスプローラーがある場所を示し、いくつかのクエリ例を提供します。

1. すべてのリソースに移動し、新しく作成した検索サービスを見つけます。

  ![非構造化検索](media/storage-unstructured-search/exampleurl.png)

3. インデックスを選択して、それを開きます。 

  ![非構造化検索](media/storage-unstructured-search/overview.png)

4. **[Search エクスプローラー]** を選択して、Search エクスプローラーを開き、データへのライブ クエリを行うことができます。

  ![非構造化検索](media/storage-unstructured-search/indexespane.png)

5. クエリ文字列フィールドが空の状態で、**[Search] \(検索)** を選択します。 空のクエリは、BLOB から*すべて*のデータを返します。

  ![非構造化検索](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>フルテキスト検索 

**[Query string] \(クエリ文字列)** フィールドに、「Myopia」と入力し、**[Search] \(検索)** を選択します。 ファイルの内容の検索を開始すると、"Myopia" という単語を含むそれらのサブセットが返されます。

  ![非構造化検索](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>システム プロパティ検索

`$select` パラメーターを使用して、システム プロパティによって検索するクエリを作成することもできます。 クエリ文字列に `$select=metadata_storage_name` を入力して、Enter を押し、その特定のフィールドのみを返します。
    
クエリ文字列は、URL を直接に変更するため、スペースは使用できません。 複数のフィールドを検索するには、`$select=metadata_storage_name,metadata_storage_path` のように、コンマを使用します。
    
`$select` パラメーターは、インデックスを定義するときに取得可能とマークされたフィールドでのみ使用できます。

  ![非構造化検索](media/storage-unstructured-search/metadatasearchunstructured.png) 

このチュートリアルが完了し、非構造化データの検索可能なセットが用意されました。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の方法など、Azure Search を使用して非構造化データを検索することについて説明しました。

> [!div class="checklist"]
> * リソース グループの作成
> * ストレージ アカウントの作成
> * コンテナーを作成する
> * コンテナーへのデータのアップロード
> * Search Service の作成
> * Search Service を使用してコンテナーを検索する

Azure Search によるドキュメントのインデックス作成の詳細については、このリンクに従います。

> [!div class="nextstepaction"]
> [Azure Blob Storage 内ドキュメントのインデックスを Azure Search で作成する](../../search/search-howto-indexing-azure-blob-storage.md)