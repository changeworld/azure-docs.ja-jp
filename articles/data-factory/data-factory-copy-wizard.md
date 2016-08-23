<properties 
	pageTitle="Data Factory コピー ウィザード | Microsoft Azure" 
	description="Data Factory コピー ウィザードを使用して、サポートされるデータ ソースからシンクにデータをコピーする方法を説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2016" 
	ms.author="spelluru"/>

# Data Factory コピー ウィザード
**Data Factory コピー ウィザード**では、リンクされたサービス、データセット、パイプラインの JSON 定義を作成しなくても、サポートされているソースからコピー先にデータをコピーするパイプラインを作成できます。コピー ウィザードを起動するには、Data Factory のホーム ページの **[データをコピー]** タイルをクリックします。

![データのコピー ウィザード](./media/data-factory-copy-wizard/copy-data-wizard.png)

## Features (機能)

### データをコピーするための直観的でシームレスなウィザード 
このウィザードでは、次の簡単な手順で数分以内にソースからコピー先にデータを簡単に移動できます。

1.	**コピー元**を選択する
2.	**コピー先**を選択する
3.	**設定**を構成する

![データ ソースの選択](./media/data-factory-copy-wizard/select-data-source-page.png)

### 豊富なデータの探索とスキーマ マッピング
ウィザード内で、テーブル/フォルダーの参照、データのプレビュー、スキーマのマップ、式の検証、簡単なデータ変換の実行が可能です。

**テーブル/フォルダーの参照** ![テーブルやフォルダーの参照](./media/data-factory-copy-wizard/browse-tables-folders.png)

### 多様なデータとオブジェクトの種類のためのスケーラブルな操作性
この操作性は、最初からビッグ データを考慮して設計されています。数百のフォルダー、ファイル、またはテーブルを移動する Data Factory パイプラインを簡単かつ効率的に作成できます。

**データのプレビュー、スキーマのマッピング、簡単な変換の実行** ![ファイル形式設定](./media/data-factory-copy-wizard/file-format-settings.png) ![スキーマ マッピング](./media/data-factory-copy-wizard/schema-mapping.png) ![式の検証](./media/data-factory-copy-wizard/validate-expressions.png)

### 多様なデータとオブジェクトの種類のためのスケーラブルな操作性
この操作性は、最初からビッグ データを考慮して設計されています。数百のフォルダー、ファイル、テーブルの移動は、コピー ウィザードを使用して、簡単かつ効率的に作成できます。

![データをコピーするテーブルの選択](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### 豊富なスケジュール オプション
コピー操作は、1 回だけ、またはスケジュールに従って (毎時、毎日など) 実行できます。これらのオプションどちらも、オンプレミス、クラウド、ローカル デスクトップ コピーにわたる幅広いコネクタで使用できます。1 回限りのコピーは、ソースからコピー先への 1 回だけのデータ移動が可能で、任意のサイズと、サポートされる任意の形式のデータに適用されます。スケジュールされたコピーは、規定された繰り返しでデータをコピーできます。豊富な設定 (再試行、タイムアウト、アラートなど) を利用して、スケジュールされたコピーを構成できます。

![スケジュール プロパティ](./media/data-factory-copy-wizard/scheduling-properties.png)


## チュートリアル
**Data Factory コピー ウィザード**を使用して、コピー アクティビティによってパイプラインを作成する簡単なチュートリアルについては、[コピー ウィザードを使用してパイプラインを作成する方法](data-factory-copy-data-wizard-tutorial.md)に関するチュートリアルをご覧ください。


## Azure BLOB フォルダー パスの変数
フォルダー パスで変数を使用すると、[WindowStart システム変数](data-factory-functions-variables.md#data-factory-system-variables)に基づいて実行時に決定されるフォルダーからデータをコピーできます。サポートされている変数は、**year**、**month**、**day**、**hour**、**minute**、**{custom}** です。例: inputfolder/{year}/{month}/{day}。

次の形式の入力フォルダーがあるとします。
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

**[ファイルまたはフォルダー]** の **[参照]** ボタンをクリックして、これらのいずれかのフォルダー、たとえば 2016->03->01->02 に移動し、**[選択]** をクリックします。テキスト ボックスに "**2016/03/01/02**" と表示されます。ここで、**2016** を **{year}**、**03** を **{month}**、**01** を **{day}**、**02** を **{hour}** にそれぞれ置き換えて、**Tab** キーを押します。次に示すように、これらの 4 つの変数の**形式**を選択するドロップ ダウン リストが表示されます。

![システム変数の使用](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

次に示すように、**custom** 変数を使用し、任意の[サポートされる書式文字列](https://msdn.microsoft.com/library/8kb3ddd4.aspx)を使用することもできます。最初に [参照] ボタンを使用して、該当する構造を使用するフォルダーを選択し、値を **{custom}** に置き換えます。**Tab** キーを押すと、書式文字列を入力できるテキスト ボックスが表示されます。

![カスタム変数の使用](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## 次のステップ
- [チュートリアル: コピー アクティビティがあるパイプラインを Data Factory コピー ウィザードで作成する](data-factory-copy-data-wizard-tutorial.md)

<!---HONumber=AcomDC_0810_2016-->