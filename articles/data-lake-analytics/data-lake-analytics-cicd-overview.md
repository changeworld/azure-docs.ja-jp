---
title: Azure Data Lake Analytics の CI/CD パイプラインを設定する方法 | Microsoft Docs
description: Azure Data Lake Analytics の継続的インテグレーションと継続的デプロイをセットアップする方法について説明します。
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
ms.openlocfilehash: a55c8fd95409de4fb1ea725d234de907f79d3c7b
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890841"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics の CI/CD パイプラインをセットアップする方法

このドキュメントでは、U-SQL ジョブおよび U-SQL データベース用の CI/CD パイプラインをセットアップする方法について説明します。

## <a name="cicd-for-u-sql-job"></a>U-SQL ジョブ用の CI/CD

Azure Data Lake Tools for Visual Studio が提供する U-SQL プロジェクト タイプは、U-SQL スクリプトの整理に役立ちます。 U-SQL プロジェクトを使用して U-SQL コードを管理することで、さらなる CI/CD シナリオが容易になります。

## <a name="build-u-sql-project"></a>U-SQL プロジェクトのビルド

U-SQL プロジェクトは、MSBuild で対応するパラメーターを渡すことによってビルドできます。 U-SQL プロジェクトのビルド プロセスをセットアップするには、次の手順に従います。

### <a name="project-migration"></a>プロジェクトの移行

U-SQL プロジェクトのビルド タスクをセットアップする前に、必ず U-SQL プロジェクトの最新バージョンを使用してください。 エディターで U-SQL プロジェクト ファイルを開き、次のインポート項目があるかどうか確認します。

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

ない場合、プロジェクトを移行する 2 つのオプションがあります。

- オプション 1: 古いインポート項目を前出のものに変更します。
- オプション 2: 古いプロジェクトを、バージョン 2.3.3000.0 よりも後の Azure Data Lake Tools for Visual Studio で開きます。 古いプロジェクト テンプレートが自動的に最新バージョンにアップグレードされます。 バージョン 2.3.3000.0 よりも後に新規作成されるプロジェクトでは、新しいテンプレートを直接使用します。

### <a name="get-nuget-package"></a>NuGet パッケージの入手

MSBuild は、U-SQL プロジェクト タイプの組み込みサポートを提供しません。 この機能を追加するには、必要な言語サービスを追加する [Microsoft.Azure.DataLake.USQL.SDK Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)への参照をソリューションのために追加する必要があります。

NuGet パッケージ参照を追加するには、ソリューション エクスプローラーでソリューションを右クリックして **[NuGet パッケージの管理]** を選択します。 または、`packages.config` という名前のファイルをソリューション フォルダーに追加し、次の内容をそのファイルに追加します。

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>U-SQL データベース参照の管理

U-SQL プロジェクト内の U-SQL スクリプトに、U-SQL データベース オブジェクトのクエリ ステートメント (例: U-SQL テーブルをクエリする、アセンブリを参照する) がある場合、この U-SQL プロジェクトをビルドする前に、これらのオブジェクトの定義を含む、対応する U-SQL データベース プロジェクトを参照する必要があります。

[U-SQL データベース プロジェクトの詳細](data-lake-analytics-data-lake-tools-develop-usql-database.md)

### <a name="build-u-sql-project-with-msbuild-command-line"></a>MSBuild コマンド ラインによる U-SQL プロジェクトのビルド

プロジェクトを移行して NuGet パッケージを入手した後、次の引数を指定して標準の MSBuild コマンド ラインを呼び出すことにより、U-SQL プロジェクトをビルドすることができます。

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder
``` 

引数の定義と値は次のとおりです。

* USQLSDKPath=<U-SQL Nuget パッケージ>\build\runtime: このパラメーターは、U-SQL 言語サービス用 NuGet パッケージのインストール パスを参照します。
* USQLTargetType=Merge または SyntaxCheck:
    * Merge: Merge モードでは、.cs、.py、.r ファイルなどの分離コード ファイルをコンパイルし、結果として得られたユーザー定義コード ライブラリ (DLL バイナリ、Python、R コードなど) を U-SQL スクリプトにインライン化します。
    * SyntaxCheck: SyntaxCheck モードでは、まず分離コード ファイルを U-SQL スクリプトにマージし、次に U-SQL スクリプトをコンパイルしてコードを検証します。
* DataRoot=<DataRoot path>: DataRoot は SyntaxCheck モードでのみ必要です。 SyntaxCheck モードでスクリプトをビルドする間、MSBuild はデータベース オブジェクトに対するスクリプト内の参照をチェックします。 ビルド前に必ず、U-SQL データベースからの被参照オブジェクトを含む一致したローカル環境を、ビルド コンピューターの DataRoot フォルダー上にセットアップしてください。 [U-SQL データベース プロジェクトを参照する](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)ことによって、これらのデータベース依存関係を管理することもできます。 MSBuild はデータベース オブジェクト参照のみをチェックし、ファイルはチェックしないことに注意してください。

### <a name="continuous-integration-with-visual-studio-team-service"></a>Visual Studio Team Services を使用した継続的インテグレーション

コマンド ラインに加えて、Visual Studio Build または MSBuild タスクを使用して、Visual Studio Team Service で U-SQL プロジェクトをビルドすることもできます。 ビルド タスクをセットアップするには、必ず次の手順を実行してください。

1.  ソリューションで参照する NuGet パッケージに `Azure.DataLake.USQL.SDK` を含めるための NuGet 復元タスクを追加して、MSBuild が U-SQL 言語ターゲットを見つけられるようにします。 

    ![U-SQL プロジェクト用の Data Lake Set CI CD MSBuild タスク](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  MSBuild の引数を設定します。次のように、Visual Studio Build または MSBuild タスクで引数を設定できます。または、VSTS ビルド定義でこれらの引数の変数を定義できます。

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory)
    ```

    ![U-SQL プロジェクト用の Data Lake Set CI CD MSBuild 変数](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>U-SQL プロジェクトのビルド出力

ビルドの実行後、U-SQL プロジェクト内のすべてのスクリプトがビルドされ、`USQLProjectName.usqlpack` という名前の zip ファイルに出力されます。 プロジェクトのフォルダー構造は、zip 形式のビルド出力でも維持されます。

>[!NOTE]
>
>各 U-SQL スクリプトの分離コード ファイルは、インライン ステートメントとしてスクリプトのビルド出力にマージされます。
>

## <a name="test-u-sql-script"></a>U-SQL スクリプトのテスト

Azure Data Lake は、U-SQL スクリプトおよび C# UDO/UDAG/UDF 用のテスト プロジェクトを提供します。
* [U-SQL スクリプトおよび拡張 C# コード用のテスト ケースを追加する方法](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [これらのテスト ケースを Visual Studio Team Service で実行する方法](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>U-SQL ジョブのデプロイ

ビルドとテストのプロセスを通じてコードを検証した後、**Azure PowerShell タスク**を通じて、U-SQL ジョブを直接 Visual Studio Team Service から送信することができます。 スクリプトを Azure Data Lake Store/Azure Blob Storage にデプロイし、[スケジュールされたジョブを Azure Data Factory を通じて実行する](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)こともできます。

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Visual Studio Team Service から U-SQL ジョブを送信する

U-SQL プロジェクトのビルド出力は **USQLProjectName.usqlpack** という名前の zip ファイルであり、プロジェクト内のすべての U-SQL スクリプトが含まれています。 [Visual Studio Team Service 内の Azure PowserShell タスク](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts)と、次のサンプル PowserShell スクリプトを使用して、U-SQL ジョブを直接、Visual Studio Team Service のビルドまたはリリース パイプラインから送信することができます。

```powershell
param(
    [Parameter(Mandatory=$true)][string]$AnalyticsAccountName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, #Root folder (e.g. artifacts root folder)
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
        # [System.IO.Compression.ZipFile]::ExtractToDirectory($USQLPackfile, $UnzipOutput, 0)
        # $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
        # Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.DirectoryName -match $subFolder}

    return $USQLFiles
}

Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."
    # submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "($usqlFiles.IndexOf())Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        LogJobInformation $jobToSubmit
        
        Write-Output "waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
        
        # ProcessResult $jobResult
    }
}

Function ProcessResult($jobResult)
{
    if ($jobResult.Result -eq "Failed")
    {
        Write-Error "Job Failed. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name), Log: $($jobResult.LogFolder)"
    }
    else
    {
        Write-Output "Job Succeeded. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name)"
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{

    Write-Output "Starting USQL script deployment..."

    # Submit ADLA jobs with usql scripts in given sub-folder.
    # Order is important here. Scripts with least dependency goes first followed 
    # by scripts which more dependencies.
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Azure Data Factory を通じた U-SQL ジョブのデプロイ

Visual Studio Team Service から直接 U-SQL ジョブを送信することに加えて、ビルド スクリプトを Azure Data Lake Store/Azure Blob Storage にアップロードし、[スケジュールされたジョブを Azure Data Factory を通じて実行する](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)こともできます。

U-SQL スクリプトを Azure Data Lake Store アカウントにアップロードするには、[Visual Studio Team Service 内の Azure PowerShell タスク](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts)と、次のサンプル PowerShell スクリプトを使用します。

```powershell
param(
    [Parameter(Mandatory=$true)][string]$ADLSName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot #Root folder (e.g. artifacts root folder)
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading DLL files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles
    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $usqlScripts.FullName -Destination "/ScriptResource2/$usqlScripts" -Force -Recurse
    # $files = @(get-childitem $usqlScripts -recurse)
    # foreach($file in $files)
    # {
    #    Write-Host "Uploading file: $($file.Name)"
    #    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/ScriptResource/$file" -Force -Recurse
    # }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return $UnzipOutput
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>U-SQL データベース用の CI/CD

Azure Data Lake Tools for Visual Studio が提供する U-SQL データベース プロジェクト テンプレートは、開発者が U-SQL データベースを迅速かつ容易に開発、管理、デプロイするために役立ちます。 [U-SQL データベース プロジェクトの詳細](data-lake-analytics-data-lake-tools-develop-usql-database.md)。

## <a name="build-u-sql-database-project"></a>U-SQL データベース プロジェクトのビルド

### <a name="get-nuget-package"></a>NuGet パッケージの入手

MSBuild は、U-SQL データベース プロジェクト タイプの組み込みサポートを提供しません。 この機能を追加するには、必要な言語サービスを追加する [Microsoft.Azure.DataLake.USQL.SDK Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)への参照をソリューションのために追加する必要があります。

NuGet パッケージ参照を追加するには、ソリューション エクスプローラーでソリューションを右クリックし、ソリューションに対する **[NuGet パッケージの管理]** を選択し、NuGet パッケージを検索してインストールします。 または、"packages.config" という名前のファイルをソリューション フォルダーに追加し、次の内容をそのファイルに追加します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>MSBuild コマンド ラインによる U-SQL データベース プロジェクトのビルド

次のように、標準の MSBuild コマンド ラインを呼び出して、U-SQL SDK NuGet パッケージ参照を追加引数として渡すことによって、U-SQL データベース プロジェクトをビルドすることができます。

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

引数 `USQLSDKPath=<U-SQL Nuget package>\build\runtime` は、U-SQL 言語サービス用の NuGet パッケージのインストール パスを参照します。

### <a name="continuous-integration-with-visual-studio-team-service"></a>Visual Studio Team Services を使用した継続的インテグレーション

コマンド ラインに加えて、**Visual Studio Build** または **MSBuild タスク**を使用して、Visual Studio Team Service で U-SQL データベース プロジェクトをビルドすることもできます。 ビルド タスクをセットアップするには、必ず次の手順を実行してください。

1.  ソリューションで参照する NuGet パッケージに `Azure.DataLake.USQL.SDK` を含めるための NuGet 復元タスクを追加して、MSBuild が U-SQL 言語ターゲットを見つけられるようにします。 

    ![U-SQL データベース プロジェクト用の Data Lake Set CI CD MSBuild タスク](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  MSBuild の引数を設定します。次のように、Visual Studio Build または MSBuild タスクで引数を設定できます。または、VSTS ビルド定義でこれらの引数の変数を定義できます。

```
/p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime
```

![U-SQL データベース プロジェクト用の Data Lake Set CI CD MSBuild 変数](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>U-SQL データベース プロジェクトのビルド出力

U-SQL データベース プロジェクトのビルド出力は、名前に `.usqldbpack` というサフィックスのついた U-SQL データベース展開パッケージです。 `.usqldbpack` パッケージは zip ファイルで、DDL フォルダー内の単一の U-SQL スクリプトにはすべての DDL ステートメントが、Temp フォルダーにはアセンブリのすべての dll と追加ファイルが含まれています。

## <a name="test-table-valued-function-and-stored-procedure"></a>テーブル値関数とストアド プロシージャのテスト

現時点で、テーブル値関数およびストアド プロシージャ用テスト ケースの直接追加はサポートされていません。 回避策として、それらの関数を呼び出す U-SQL スクリプトを含む U-SQL プロジェクトを作成し、それらに対するテスト ケースを記述することができます。 U-SQL データベース プロジェクトで定義されたテーブル値関数およびストアド プロシージャ用のテスト ケースをセットアップするには、次の手順に従います。

1.  テスト目的の U-SQL プロジェクトを作成し、テーブル値関数およびストアド プロシージャを呼び出す U-SQL スクリプトを記述します。
2.  この U-SQL プロジェクトにデータベース参照を追加します。 テーブル値関数およびストアド プロシージャの定義を取得するには、DDL ステートメントを含むデータベース プロジェクトを参照する必要があります。 [データベース参照の詳細](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)。
3.  テーブル値関数およびストアド プロシージャを呼び出す U-SQL スクリプト用のテスト ケースを追加します。 [U-SQL スクリプト用のテスト ケースを追加する方法](data-lake-analytics-cicd-test.md#test-u-sql-scripts)。

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Visual Studio Team Service から U-SQL データベースをデプロイする

`PackageDeploymentTool.exe` は、U-SQL データベース デプロイ パッケージ (.usqldbpack) のデプロイに役立つプログラミングおよびコマンド ライン インターフェイスを提供します。 SDK は [U-SQL SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)に含まれており、場所は build/runtime/PackageDeploymentTool.exe です。 `PackageDeploymentTool.exe` を使用して、U-SQL データベースを Azure Data Lake Analytics とローカル アカウントの両方にデプロイできます。

>[!NOTE]
>
>U-SQL データベース デプロイに対する、PowerShell コマンド ラインのサポートと Visual Studio Team Service リリース タスクのサポートが計画されています。
>

Visual Studio Team Service でデータベース デプロイ タスクをセットアップするには、次の手順に従います。

1. ビルドまたはリリース パイプラインで PowerShell Script タスクを追加し、次の PowerShell スクリプトを実行します。 このタスクにより、`PackageDeploymentTool.exe` のための Azure SDK 依存関係を満たします。 これらの依存項目を特定のフォルダーに読み込むために、-outputfolder パラメーターを設定することができます。 手順 2 で、このフォルダーのパスを `PackageDeploymentTool.exe` に渡します。 

    ```powershell
    param (
        [string]$outputfolder = "RequiredDll",
        [string]$workingfolder = ""
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force

    echo "Copy required DLLs to output folder..."

    mkdir $outputfolder -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $outputfolder
    ```

2. ビルドまたはリリース パイプラインで **Command-Line タスク**を追加し、`PackageDeploymentTool.exe` を呼び出すスクリプトを入力します。 サンプル スクリプトは次のとおりです。 

    * U-SQL データベースをローカルでデプロイする

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * 対話型認証モードを使用して、U-SQL データベースを Azure Data Lake Analytics のアカウントに追加します。

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * シークレット認証を使用して、U-SQL データベースを Azure Data Lake Analytics のアカウントに追加します。

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * certFile 認証を使用して、U-SQL データベースを Azure Data Lake Analytics のアカウントに追加します。

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**PackageDeploymentTool.exe のパラメーターの説明:**

**一般的なパラメーター:**

|パラメーター|説明|既定値|必須|
|---------|-----------|-------------|--------|
|Package|デプロイする U-SQL データベース デプロイ パッケージのパス|null|true|
|Database|デプロイ先または作成するデータベースの名前|master|false|
|LogFile|ログ記録のためのファイルのパス。既定では標準出力 (コンソール)|null|false|
|LogLevel|ログ レベル : 詳細、標準、警告、エラー|LogLevel.Normal|false|

**ローカル デプロイのパラメーター:**

|パラメーター|説明|既定値|必須|
|---------|-----------|-------------|--------|
|DataRoot|ローカル Data Root フォルダーのパス|null|true|

**Azure Data Lake Analytics デプロイのパラメーター:**

|パラメーター|説明|既定値|必須|
|---------|-----------|-------------|--------|
|Account|どの Azure Data Lake Analytics アカウントにデプロイするかをアカウント名で指定する|null|true|
|ResourceGroup|Azure Data Lake Analytics アカウントの Azure リソース グループ名|null|true|
|SubscriptionId|Azure Data Lake Analytics アカウントの Azure サブスクリプション ID|null|true|
|Tenant|テナント名 (AAD ディレクトリのドメイン名。Azure portal のサブスクリプション管理ページで確認可能)|null|true|
|AzureSDKPath|Azure SDK 内の依存アセンブリを検索するためのパス|null|true|
|Interactive|認証に対話型モードを使用するかどうか|false|false|
|ClientId|非対話型認証のための AAD アプリケーション ID。非対話型認証に必要|null|非対話型認証に必要|
|Secrete|非対話型認証用のシークレット/パスワード。信頼された/安全な環境でのみ使用すること|null|非対話型認証に必要、または SecreteFile を使用|
|SecreteFile|非対話型認証用のシークレット/パスワードを保存したファイル。必ず、現在のユーザーによる読み取りのみが可能な状態を維持すること|null|非対話型認証に必要、または Secrete を使用|
|CertFile|非対話型認証用の X.509 証明書を保存したファイル。既定ではクライアント シークレット認証を使用|null|false|
|JobPrefix|データベース デプロイ U-SQL DDL ジョブ用のプレフィックス|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>次の手順

- [Azure Data Lake Analytics コードをテストする方法](data-lake-analytics-cicd-test.md)
- [ローカル コンピューターで U-SQL スクリプトを実行する](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL データベース プロジェクトを使用して U-SQL データベースを開発する](data-lake-analytics-data-lake-tools-develop-usql-database.md)