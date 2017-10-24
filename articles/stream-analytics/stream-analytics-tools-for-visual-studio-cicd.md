---
title: "Stream Analytics Visual Studio Tools を使用して継続的インテグレーションおよびデプロイ プロセスを設定する方法 | Microsoft Docs"
description: "Stream Analytics Visual Studio Tools を使用した継続的インテグレーションおよびデプロイ プロセスの設定のチュートリアル"
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 947266dc94babab21556da26d8cc8c917cf81c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-the-continuous-integration-and-deployment-process"></a>Stream Analytics Visual Studio Tools を使用した継続的インテグレーションおよびデプロイ プロセスの設定
このチュートリアルでは、Stream Analytics Visual Studio Tools を使用して継続的インテグレーションおよびデプロイ プロセスを設定する方法を説明します。

最新バージョン (2.3.0000.0 以降) の [Visual Studio の Stream Analytics ツール](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)では、**MSBuild** のサポートが追加されています。 

新しくリリースされた NuGet パッケージ [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) もあります。 これは、Stream Analytics Visual Studio プロジェクトの継続的インテグレーションおよびデプロイ プロセスをサポートする MSBuild、ローカル実行、デプロイ ツールを提供します。 
> [!NOTE] 
NuGet パッケージは、Visual Studio の Stream Analytics ツールのバージョン 2.3.0000.0 以降でのみ使用できます。 以前のバージョンの Visual Studio Tools で作成されたプロジェクトがある場合は、バージョン 2.3.0000.0 以降で開き、保存します。 そうすると、新機能が有効になります。 

[Visual Studio の Stream Analytics ツールの使用方法](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="msbuild"></a>MSBuild
標準の Visual Studio MSBuild エクスペリエンスのように、プロジェクトを作成するには、プロジェクトを右クリックして **[ビルド]** を選択するか、コマンド ラインから NuGet パッケージの **MSBuild** を使用します。
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Stream Analytics Visual Studio プロジェクトが正常にビルドされると、**bin/[Debug/Retail]/Deploy** フォルダーに次の 2 つのAzure Resource Manager テンプレート ファイルが生成されます。 

Azure Resource Manager テンプレート ファイル。
*       [ProjectName].JobTemplate.json 

Azure Resource Manager パラメーター ファイル。
*       [ProjectName].JobTemplate.parameters.json   

parameters.json ファイルの既定のパラメーターは、Visual Studio プロジェクトの設定から取得されます。 他の環境にデプロイする場合は、パラメーターを適宜置換するだけです。 
> [!NOTE] 
すべての資格情報の既定値は、すべて null 値に設定されます。 これらはクラウドにデプロイする前に設定する**必要があります**。
```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
詳しくは、[Azure Resource Manager テンプレート ファイルと Azure PowerShell を使用してデプロイする方法](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy)に関する記事と[オブジェクトを Azure Resource Manager テンプレートのパラメーターとして使用する方法](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters)に関する記事をご覧ください。


## <a name="command-line-tool"></a>コマンドライン ツール

### <a name="build-the-project"></a>プロジェクトのビルド
NuGet パッケージには、**SA.exe** というコマンドライン ツールがあります。 継続的インテグレーションと継続的配信のプロセスで使用できる任意のマシンでのプロジェクトのビルド、ローカル テストをサポートします。 

デプロイ ファイルは、既定では現在のディレクトリに配置されます。 -OutputPath パラメーターで出力パスを指定できます。

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>スクリプトのローカル テスト

プロジェクトで Visual Studio にローカル入力ファイルを指定した場合は、*localrun* コマンド使用して、自動化されたスクリプト テストを実行できます。 出力結果は現在のディレクトリに配置されます。
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-job-definition-file-to-use-with-stream-analytics-power-shell"></a>Stream Analytics Power Shell で使用するジョブ定義ファイルを生成します。

*arm* コマンドは、ビルドで生成されたジョブ テンプレートとジョブ テンプレート パラメーター ファイルを引数に取ります。 次に、Stream Analytics PowerShell API で使用できるジョブ定義 JSON ファイルに結合します。

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```

```
Example
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


