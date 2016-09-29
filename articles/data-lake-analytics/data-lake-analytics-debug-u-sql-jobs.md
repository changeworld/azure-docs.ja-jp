<properties 
   pageTitle="U-SQL ジョブのデバッグ | Microsoft Azure" 
   description="Visual Studio を使用して、U-SQL の失敗した頂点をデバッグする方法について説明します。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#Data Lake Analytics ジョブに U-SQL で C# コードをデバッグする 

Azure Data Lake Visual Studio ツールを使用して、ユーザー コード内のバグが原因で失敗した U-SQL ジョブをデバッグする方法について説明します。

Visual Studio ツールでは、失敗したジョブをトレースおよびデバッグするために、コンパイル済みのコードと必要な頂点データをクラスターからダウンロードすることができます。

ビッグ データ システムは、通常、Java、C#、Python などの言語で記述した機能拡張モデルを提供します。これら多くのシステムが出すランタイム デバッグ情報には制限があり、カスタム コード内でランタイム エラーをデバッグすることを難しくしています。最新の Visual Studio ツールには、"Failed Vertex Debug" と呼ばれる機能が備わっています。この機能を使用すると、Azure からローカル ワークステーションにランタイム データをダウンロードすることができ、クラウドの同じランタイム データと入力データを使用して失敗したカスタム C# コードをデバッグできます。問題解決後に、ツールから、Azure で変更後のコードを再実行することができます。

この機能を紹介するビデオについては、「[Azure Data Lake Analytics でカスタム コードをデバッグする](https://mix.office.com/watch/1bt17ibztohcb)」を参照してください。

>[AZURE.NOTE] 2 つの Windows アップグレード ([Microsoft Visual C++ 2015 再頒布可能 Update 2](https://www.microsoft.com/download/details.aspx?id=51682) と [Windows Universal C Runtime](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)) を適用していない場合は、Visual Studio がハングまたはクラッシュする可能性があります。


##前提条件
-	[使用の開始](data-lake-analytics-data-lake-tools-get-started.md)に関する記事を確認済みである。

## デバッグ プロジェクトを作成および構成する

失敗したジョブを Data Lake Visual Studio ツールで開くと、アラートが表示されます。エラーに関する詳細情報が、[エラー] タブとウィンドウの上部にある黄色のアラート バーに表示されます。

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 頂点のダウンロード](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**頂点をダウンロードしてデバッグ ソリューションを作成するには**

1.	失敗した U-SQL ジョブを Visual Studio で開きます。
2.	**[ダウンロード]** をクリックして必要なリソースと入力ストリームをすべてダウンロードします。ダウンロードに失敗した場合は **[再試行]** をクリックします。
3.	ダウンロードが完了したら、**[開く]** をクリックしてローカル デバッグ プロジェクトを作成します。**VertexDebug** という新しい Visual Studio ソリューションと **LocalVertexHost** という空のプロジェクトが作成されます。

U-SQL 分離コード (Script.usql.cs) でユーザー定義演算子を使用している場合、ユーザー定義演算子コードを含むクラス ライブラリ C# プロジェクトを作成し、このプロジェクトを VertexDebug ソリューションに含める必要があります。

.dll アセンブリを Data Lake Analytics データベースに登録している場合は、そのアセンブリのソース コードを VertexDebug ソリューションに追加する必要があります。
 
U-SQL 分離コードに個別の C# クラス ライブラリを作成して .dll アセンブリを Data Lake Analytics データベースに登録している場合は、そのアセンブリのソース C# プロジェクトを VertexDebug ソリューションに追加する必要があります。

U-SQL 分離コード (Script.usql.cs) でユーザー定義演算子を使用している場合、ユーザー定義演算子コードを含むクラス ライブラリ C# プロジェクトを作成し、このプロジェクトを VertexDebug ソリューションに含める必要があります。これを機能させるには、ソース コードを含む C# ライブラリを作成し、クラスターに登録されている 1 つに、アセンブリ名を変更する必要があります。クラスターで実行されているスクリプトをチェックすると、クラスターに登録されているアセンブリ名を取得することができます。U-SQL ジョブを開き、ジョブ パネルの ”スクリプト” をクリックすることにより、これを行うことができます。

**ソリューションをを構成するには**

1.	ソリューション エクスプローラーで、作成した C# プロジェクトを右クリックしてから **[プロパティ]** をクリックします。
2.	出力パスを LocalVertexHost プロジェクトの作業ディレクトリのパスとして設定します。LocalVertexHost プロジェクトの作業ディレクトリのパスは、LocalVertexHost プロパティから取得できます。
3.	C# プロジェクトを作成して .pdb ファイルをLocalVertexHost プロジェクトの作業ディレクトリに配置します。または、.pdb ファイルをこのフォルダーに手動でコピーできます。
4.	**[例外設定]** で [Common Language Runtime Exceptions] チェック マークをクリックします。

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##ジョブをデバッグする

頂点をダウンロードしてデバッグ ソリューションを作成し、環境を構成したら、U-SQL コードをデバッグできます。

1.	ソリューション エクスプローラーで、作成した **[LocalVertexHost]** プロジェクトを右クリックし、**[デバッグ]** をポイントして、**[新しいインスタンスを開始]** をクリックします。LocalVertexHost は、スタートアップ プロジェクトとして設定する必要があります。最初に次のメッセージが表示される場合がありますが、このメッセージは無視できます。デバッグ画面が表示されるまで最大 1 分間かかることがあります。
 
    ![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.	Visual Studio ベースのデバッグ機能 (ウォッチ、変数など) を使用して、問題のトラブルシューティングを行います。
5.	問題を特定したら、コードを修正し、C# プロジェクトをリビルドしてから再度テストして、最終的にすべての問題を解決します。デバッグが正常に完了したら、出力ウィンドウに次のメッセージが表示されます。

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##ジョブを再送信する

U-SQL コードのデバッグが完了したら、失敗したジョブを再送信することができます。

1. 新しい .dll アセンブリを ADLA データベースに登録します。

    1.	Data Lake Visual Studio ツールのサーバー エクスプローラーまたはクラウド エクスプローラーで、**[データベース]** ノードを展開します。
    2.	アセンブリを登録するためにアセンブリを右クリックしてします。
    3.	新しい .dll アセンブリを ADLA データベースに登録します。
 
2.	または、C# コードを script.usql.cs (C# 分離コード ファイル) にコピーします 。
3.	ジョブを再送信します。

##次のステップ

- [チュートリアル: Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)
- [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0914_2016-->