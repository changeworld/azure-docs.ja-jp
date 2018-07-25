---
title: ローカル コンピューター上で Azure Data Lake U-SQL スクリプトを実行する | Microsoft Docs
description: Azure Data Lake Tools for Visual Studio を使用してローカル コンピューター上で U-SQL ジョブを実行する方法について説明します。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888968"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>ローカル コンピューターで U-SQL スクリプトを実行する

U-SQL スクリプトを開発する場合は、コストと時間が節約されるため、U-SQL スクリプトをローカルで実行することが一般的です。 Azure Data Lake Tools for Visual Studio は、ローカル コンピューター上での U-SQL スクリプトの実行をサポートしています。 

## <a name="basic-concepts-for-local-run"></a>ローカル実行の基本的な概念

次の表は、ローカル実行のためのコンポーネント、およびこれらのコンポーネントとクラウド実行との対応を示しています。

|コンポーネント|ローカル実行|クラウド実行|
|---------|---------|---------|
|Storage|ローカル データ ルート フォルダー|既定の Azure Data Lake Store アカウント|
|コンピューティング|U-SQL ローカル実行エンジン|Azure Data Lake Analytics サービス|
|実行環境|ローカル コンピューター上の作業ディレクトリ|Azure Data Lake Analytics クラスター|

ローカル実行コンポーネントの詳細な説明:

### <a name="local-data-root-folder"></a>ローカル データ ルート フォルダー

ローカル データ ルート フォルダーは、ローカル コンピューティング アカウント用の "ローカル ストア" です。 ローカル コンピューター上のローカル ファイル システム内のすべてのフォルダーをローカル データ ルート フォルダーにすることができます。 これは、Data Lake Analytics アカウントの既定の Azure Data Lake Store アカウントと同等です。 別のデータ ルート フォルダーへの切り替えは、別の既定のストア アカウントへの切り替えと同じです。 

データ ルート フォルダーは、次の操作のために使用されます。
- データベース、テーブル、テーブル値関数、アセンブリなどのメタデータを格納する。
- U-SQL スクリプトで相対パスとして定義されている入力および出力パスを検索する。 相対パスを使用すると、U-SQL スクリプトを Azure にデプロイすることが容易になります。

### <a name="u-sql-local-run-engine"></a>U-SQL ローカル実行エンジン

U-SQL ローカル実行エンジンは、U-SQL ジョブ用の "ローカル コンピューティング アカウント" です。 ユーザーは、Azure Data Lake Tools for Visual Studio を使用して U-SQL ジョブをローカルで実行できます。 ローカル実行はまた、Azure Data Lake U-SQL SDK コマンド ラインやプログラミング インターフェイスでもサポートされています。 [詳細については、「Azure Data Lake U-SQL SDK」を参照してください](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)。

### <a name="working-directory"></a>作業ディレクトリ

U-SQL スクリプトを実行する場合は、コンパイル結果や実行ログなどをキャッシュするために作業ディレクトリ フォルダーが必要です。 Azure Data Lake Tools for Visual Studio では、作業ディレクトリは U-SQL プロジェクトの作業ディレクトリです (通常は、`<U-SQL project root path>/bin/debug>` の下にあります)。 作業ディレクトリは、新しい実行がトリガーされるたびにクリーニングされます。

## <a name="local-run-in-visual-studio"></a>Visual Studio でのローカル実行

Azure Data Lake Tools for Visual Studio には組み込みのローカル実行エンジンがあり、それがローカル コンピューティング アカウントとして表示されます。 U-SQL スクリプトをローカルで実行するには、スクリプトのエディターの端にあるドロップダウンで (Local-machine) または (Local-project) アカウントを選択し、**[送信]** をクリックします。

![Data Lake Tools for Visual Studio ローカル アカウントへのスクリプトの送信](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>(Local-machine) アカウントでのローカル実行

(Local-machine) アカウントは、ローカル ストア アカウントとして 1 つのローカル データ ルート フォルダーを持つ共有されたローカル コンピューティング アカウントです。 データ ルート フォルダーは、既定では "C:\Users\<username>\AppData\Local\USQLDataRoot" にあり、**[ツール] > [Data Lake] > [オプションと設定]** でも構成できます。

![Data Lake Tools for Visual Studio ローカル データ ルートの構成](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
ローカル実行には U-SQL プロジェクトが必要です。 U-SQL プロジェクトの作業ディレクトリは、U-SQL ローカル実行の作業ディレクトリに使用されます。 ローカル実行中にコンパイル結果、実行ログ、その他のジョブの実行に関連したファイルが生成され、作業ディレクトリ フォルダーの下に格納されます。 スクリプトを再実行するたびに、作業ディレクトリ内のこれらのファイルはすべてクリーニングされ、再生成されることに注意してください。

## <a name="local-run-with-local-project-account"></a>(Local-project) アカウントでのローカル実行

(Local-project) アカウントは、分離されたローカル データ ルート フォルダーを持つ、各プロジェクトのプロジェクトごとに分離されたローカル コンピューティング アカウントです。 ソリューション エクスプローラーで開かれているすべてのアクティブな U-SQL プロジェクトは、対応する `(Local-project: <project name>)` アカウントがサーバー エクスプローラーと U-SQL スクリプトのエディターの端の両方に示されています。 

(Local-project) アカウントは、開発者のためのクリーンな分離開発環境を提供します。 すべてのローカル ジョブのメタデータや入出力データを格納する共有されたローカル データ ルート フォルダーがある (Local-machine) アカウントとは異なり、(Local-project) アカウントでは、U-SQL スクリプトが実行されるたびに U-SQL プロジェクトの作業ディレクトリの下に一時ローカル データ ルート フォルダーが作成されます。 この一時データ ルート フォルダーは、再ビルドまたは再実行が発生するとクリーニングされます。 

U-SQL プロジェクトでは、プロジェクト参照およびプロパティを通して、この分離されたローカル実行環境を管理するための優れたエクスペリエンスが提供されます。 プロジェクト内の U-SQL スクリプトの入力データ ソースと、参照されるデータベース環境の両方を構成できます。

### <a name="manage-input-data-source-for-local-project-account"></a>(Local-project) アカウントの入力データ ソースを管理する

U-SQL プロジェクトでは、(Local-project) アカウントのローカル データ ルート フォルダーの作成とデータの設定を管理します。 一時データ ルート フォルダーは、再ビルドおよびローカル実行が発生するたびにクリーニングされ、U-SQL プロジェクトの作業ディレクトリの下に再作成されます。 ローカル ジョブの実行の前に、U-SQL プロジェクトによって構成されたすべてのデータ ソースがこの一時ローカル データ ルート フォルダーにコピーされます。 

データ ソースのルート フォルダーは、**U-SQL プロジェクトの右クリック > [プロパティ] > [Test Data Source] (テスト データ ソース)** で構成できます。 (Local-project) アカウントで U-SQL スクリプトを実行する場合は、**Test Data Source** フォルダー内のすべてのファイルおよびサブフォルダー (サブフォルダーの下のファイルを含む) が一時ローカル データ ルート フォルダーにコピーされます。 ローカル ジョブの実行が完了した後、その出力結果も、プロジェクトの作業ディレクトリ内の一時ローカル データ ルート フォルダーの下に見つけることができます。 プロジェクトが再ビルドされ、クリーニングされると、これらのすべての出力が削除およびクリーニングされることに注意してください。 

![Data Lake Tools for Visual Studio プロジェクトのテスト データ ソースの構成](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>(Local-project) アカウントの参照されるデータベース環境を管理する 

U-SQL クエリで U-SQL データベース オブジェクトを使用するか、またはそれに対してクエリを実行する場合は、この U-SQL スクリプトをローカルで実行する前に、データベース環境をローカルで使用できるように準備する必要があります。 (Local-project) アカウントの場合は、U-SQL データベースの依存関係を U-SQL プロジェクト参照によって管理できます。 U-SQL プロジェクトに U-SQL データベース プロジェクト参照を追加できます。 (Local-project) アカウントで U-SQL スクリプトを実行する前に、参照されるすべてのデータベースが一時ローカル データ ルート フォルダーにデプロイされます。 さらに、実行のたびに、一時データ ルート フォルダーが新しい分離された環境としてクリーニングされます。

関連記事:
* [U-SQL データベース プロジェクトで U-SQL データベース定義を管理する方法について](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [U-SQL プロジェクトで U-SQL データベース参照を管理する方法について](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>(Local-machine) アカウントと (Local-project) アカウントの違い

(Local-machine) アカウントは、ユーザーのローカル コンピューター上で Azure Data Lake Analytics アカウントをシミュレートすることを目的にしています。 ここでは、Azure Data Lake Analytics アカウントと同じエクスペリエンスが共有されます。 (Local-project) は、スクリプトをローカルで実行する前に、ユーザーのデータベース参照や入力データのデプロイに役立つ、わかりやすいローカル開発環境を提供することを目的にしています。 (Local-machine) アカウントは、すべてのプロジェクトからアクセスできる、共有された永続的な環境を提供します。 (Local-project) アカウントはプロジェクトごとの分離開発環境を提供し、実行のたびに更新されます。 そのため、(Local-project) アカウントは新しい変更をすばやく適用することによって、より高速な開発エクスペリエンスを提供します。

次の表に、(Local-machine) アカウントと (Local-project) アカウントのさらに詳細な違いを示します。

|違いの観点|(Local-machine)|(Local-project)|
|----------------|---------------|---------------|
|ローカル アクセス|すべてのプロジェクトからアクセスできます。|このアカウントにアクセスできるのは対応するプロジェクトだけです。|
|ローカル データ ルート フォルダー|永続的なローカル フォルダー。 **[ツール] > [Data Lake] > [オプションと設定]** で構成されます。|U-SQL プロジェクトの作業ディレクトリの下の、ローカル実行のたびに作成される一時フォルダー。 このフォルダーは、再ビルドまたは再実行が発生するとクリーニングされます。|
|U-SQL スクリプトの入力データ|永続的なローカル データ ルート フォルダーの下の相対パス。|**U-SQL プロジェクトの [プロパティ] > [Test Data Source] (テスト データ ソース)** で設定されます。 ローカル実行の前に、すべてのファイルおよびサブフォルダーが一時データ ルート フォルダーにコピーされます。|
|U-SQL スクリプトの出力データ|永続的なローカル データ ルート フォルダーの下の相対パス。|一時データ ルート フォルダーに出力されます。 これらの結果は、再ビルドまたは再実行が発生するとクリーニングされます。|
|参照されるデータベースのデプロイ|(Local-machine) アカウントに対して実行する場合、参照されるデータベースは自動的にはデプロイされません。 Azure Data Lake Analytics アカウントへの送信についても同じです。|参照されるデータベースは、ローカル実行の前に (Local-project) アカウントに自動的にデプロイされます。 再ビルドまたは再実行が発生すると、すべてのデータベース環境がクリーニングされ、再デプロイされます。|

## <a name="local-run-with-u-sql-sdk"></a>U-SQL SDK でのローカル実行

Visual Studio での U-SQL スクリプトのローカルでの実行に加えて、Azure Data Lake U-SQL SDK を使用して、コマンド ラインやプログラミング インターフェイスで U-SQL スクリプトをローカルで実行することもできます。 これらのインターフェイス経由で、U-SQL のローカル実行やテストを自動化できます。

[詳細については、「Azure Data Lake U-SQL SDK」を参照してください](data-lake-analytics-u-sql-sdk.md)。

## <a name="next-steps"></a>次の手順

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [Azure Data Lake Analytics の CI/CD パイプラインを設定する方法](data-lake-analytics-cicd-overview.md)
- [U-SQL データベース プロジェクトを使用して U-SQL データベースを開発する](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Azure Data Lake Analytics コードをテストする方法](data-lake-analytics-cicd-test.md)
