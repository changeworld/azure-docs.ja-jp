---
title: 'Azure HDInsight: .NET サンプル'
description: HDInsight SDK for .NET を使用した一般的なタスクの C# .NET サンプルは、GitHub でご覧いただけます。
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: 3e2cb0768701196515ace3f6ef0d475f8e3b0f79
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688106"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: .NET サンプル

> [!div class="op_single_selector"]
> * [.NET のサンプル](hdinsight-sdk-dotnet-samples.md)
> * [Python のサンプル](hdinsight-sdk-python-samples.md)
> * [Java のサンプル](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

この記事では、次の内容について説明します。

* クラスター作成タスクのサンプルへのリンク
* その他の管理タスクのリファレンス コンテンツへのリンク

[Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)ことができます。Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Azure HDInsight SDK for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>クラスターの管理 - 作成

* [Kafka クラスターの作成](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Spark クラスターの作成](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Azure Data Lake Storage Gen2 での Spark クラスターの作成](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Enterprise セキュリティ パッケージ (ESP) での Spark クラスターの作成](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

これらの .NET サンプルは、[hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub リポジトリを複製することで入手できます。

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

ここに示した追加的な SDK 機能に関するコード スニペットは、[HDInsight SDK for .NET のリファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)でご覧いただけます。
