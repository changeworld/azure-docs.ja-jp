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
Azure Data Factory コピー ウィザードでは、リンクされたサービス、データセット、パイプラインの JSON 定義を作成しなくても、サポートされているソースからコピー先にデータをコピーするパイプラインを作成できます。コピー ウィザードを起動するには、Data Factory のホーム ページで **[データをコピー]** タイルをクリックします。

![コピー ウィザード](./media/data-factory-copy-wizard/copy-data-wizard.png)

## Features (機能)

### データをコピーするための直観的でシームレスなウィザード
このウィザードでは、次の簡単な手順で数分以内にソースからコピー先にデータを簡単に移動できます。

1.	ソースを選択します。
2.	コピー先を選択します。
3.	設定を構成します。

![データ ソースの選択](./media/data-factory-copy-wizard/select-data-source-page.png)

### 豊富なデータの探索とスキーマ マッピング
ウィザードでは、テーブルおよびフォルダーの参照、データのプレビュー、スキーマのマッピング、式の検証、簡単なデータ変換の実行が可能です。

**テーブル/フォルダーの参照** ![テーブルやフォルダーの参照](./media/data-factory-copy-wizard/browse-tables-folders.png)

### 多様なデータとオブジェクトの種類のためのスケーラブルな操作性
この操作性は、最初からビッグ データを考慮して設計されています。数百個のフォルダー、ファイル、またはテーブルを移動する Data Factory パイプラインを簡単かつ効率的に作成できます。

**データのプレビュー** ![ファイル形式設定](./media/data-factory-copy-wizard/file-format-settings.png)

**スキーマのマッピング** ![スキーマ マッピング](./media/data-factory-copy-wizard/schema-mapping.png)

**簡単な変換の実行** ![式の検証](./media/data-factory-copy-wizard/validate-expressions.png)

### 多様なデータとオブジェクトの種類のためのスケーラブルな操作性
コピー ウィザードを使用すると、数百個のフォルダー、ファイル、またはテーブルを効率的かつ簡単に移動できます。

![データのコピー元のテーブルの選択](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### 豊富なスケジュール オプション
コピー操作は 1 回だけ実行することも、スケジュールに従って (毎時、毎日など) 実行することもできます。このどちらのオプションも、オンプレミス、クラウド、ローカル デスクトップ コピーのさまざまなコネクタで使用できます。

1 回限りのコピー操作では、ソースからコピー先に 1 回だけデータを移動できます。これは、サポートされている形式のあらゆるサイズのデータに適用されます。スケジュールされたコピーでは、指定した繰り返しでデータをコピーできます。豊富な設定 (再試行、タイムアウト、アラートなど) を使用して、スケジュールされたコピーを構成できます。

![スケジュール プロパティ](./media/data-factory-copy-wizard/scheduling-properties.png)

## Azure BLOB フォルダー パスの変数
フォルダー パスで変数を使用すると、[WindowStart システム変数](data-factory-functions-variables.md#data-factory-system-variables)に基づいて実行時に決定されるフォルダーからデータをコピーできます。サポートされている変数は、**{year}**、**{month}**、**{day}**、**{hour}**、**{minute}**、**{custom}** です。例: inputfolder/{year}/{month}/{day}。

次の形式の入力フォルダーがあるとします。

	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

**[ファイルまたはフォルダー]** の **[参照]** ボタンをクリックして、これらのフォルダーのいずれか (例: 2016->03->01->02) を参照し、**[選択]** をクリックします。テキスト ボックスに "**2016/03/01/02**" と表示されます。ここで、**2016** を **{year}**、**03** を **{month}**、**01** を **{day}**、**02** を **{hour}** にそれぞれ置き換え、Tab キーを押します。この 4 つの変数の形式を選択するドロップダウン リストが表示されます。

![システム変数の使用](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

次のスクリーンショットに示すように、**custom** 変数と、任意の[サポートされる書式文字列](https://msdn.microsoft.com/library/8kb3ddd4.aspx)を使用することもできます。その構造のフォルダーを選択するには、まず **[参照]** をクリックします。次に、値を **{custom}** に置き換え、Tab キーを押して、書式文字列を入力できるテキスト ボックスを表示します。

![カスタム変数の使用](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## 次のステップ
Data Factory コピー ウィザードを使用して、コピー アクティビティを含むパイプラインを作成する簡単なチュートリアルについては、[コピー ウィザードを使用してパイプラインを作成する方法に関するチュートリアル](data-factory-copy-data-wizard-tutorial.md)をご覧ください。

<!---HONumber=AcomDC_0824_2016-->