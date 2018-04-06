---
title: Stream Analytics Visual Studio Tools を使用した継続的インテグレーションおよびデプロイ プロセスの設定 | Microsoft Docs
description: Stream Analytics Visual Studio Tools を使用した継続的インテグレーションおよびデプロイ プロセスの設定のチュートリアル
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 14bb15f19b517b55281959f0de970e3f5e0d360b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2018
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-a-continuous-integration-and-deployment-process"></a>Stream Analytics Visual Studio Tools を使用した継続的インテグレーションおよびデプロイ プロセスの設定
このチュートリアルでは、Azure Stream Analytics Visual Studio Tools を使用して継続的インテグレーションおよびデプロイ プロセスを設定する方法を説明します。

最新バージョン (2.3.0000.0 以降) の [Visual Studio の Stream Analytics ツール](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)では、MSBuild のサポートが追加されています。

新しくリリースされた NuGet パッケージ [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) もあります。 これは、Stream Analytics Visual Studio プロジェクトの継続的インテグレーションおよびデプロイ プロセスをサポートする MSBuild、ローカル実行、デプロイ ツールを提供します。 
> [!NOTE] 
NuGet パッケージは、Visual Studio の Stream Analytics ツールのバージョン 2.3.0000.0 以降でのみ使用できます。 以前のバージョンの Visual Studio Tools で作成されたプロジェクトがある場合は、バージョン 2.3.0000.0 以降で開き、保存します。 その後、新しい機能が有効になります。 

[Visual Studio の Stream Analytics ツールの使用方法](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)を確認してください。

## <a name="msbuild"></a>MSBuild
標準の Visual Studio MSBuild エクスペリエンスと同様に、プロジェクトをビルドするオプションは 2 つあります。 プロジェクトを右クリックし、次に **[ビルド]** を選択できます。 コマンド ラインから NuGet パッケージの **MSBuild** を使用することもできます。
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Stream Analytics Visual Studio プロジェクトが正常にビルドされると、**bin/[Debug/Retail]/Deploy** フォルダーに次の 2 つの Azure Resource Manager テンプレート ファイルが生成されます。 

*  Resource Manager テンプレート ファイル

       [ProjectName].JobTemplate.json 

*  Resource Manager パラメーター ファイル

       [ProjectName].JobTemplate.parameters.json   

parameters.json ファイルの既定のパラメーターは、Visual Studio プロジェクトの設定から取得されます。 別の環境にデプロイする場合は、パラメーターを適宜置換します。

> [!NOTE] 
すべての資格情報の既定値は、null 値に設定されます。 クラウドにデプロイする前に、値を設定する "*必要があります*"。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
[Resource Manager テンプレート ファイルと Azure PowerShell を使用してデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)方法を確認してください。 [Resource Manager テンプレートのパラメーターとしてオブジェクトを使用する](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)方法を確認してください。


## <a name="command-line-tool"></a>コマンドライン ツール

### <a name="build-the-project"></a>プロジェクトのビルド
NuGet パッケージには、**SA.exe** というコマンドライン ツールがあります。 継続的インテグレーションと継続的デリバリーのプロセスで使用できる任意のマシンでのプロジェクトのビルドとローカル テストをサポートします。 

デプロイ ファイルは、既定では現在のディレクトリに配置されます。 次の -OutputPath パラメーターを使用して出力パスを指定できます。

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>スクリプトのローカル テスト

プロジェクトで Visual Studio にローカル入力ファイルを指定した場合は、*localrun* コマンド使用して、自動化されたスクリプト テストを実行できます。 出力結果は現在のディレクトリに配置されます。
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Stream Analytics PowerShell API で使用するジョブ定義ファイルを生成する

*arm* コマンドは、ビルドで生成されたジョブ テンプレートとジョブ テンプレート パラメーター ファイルを引数に取ります。 次に、Stream Analytics PowerShell API で使用できるジョブ定義 JSON ファイルにこれらを結合します。

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
例:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


