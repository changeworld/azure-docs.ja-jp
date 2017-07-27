---
title: "Visual Studio を使用する Azure Functions の開発 | Microsoft Docs"
description: "Azure Functions Tools for Visual Studio 2017 を使用して、Azure Functions を開発およびテストする方法を説明します。"
services: functions
documentationcenter: .net
author: ggailey777
manager: erikre
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: glenga, donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 8b569727c51589bd622d41465bb3565220c19fca
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---
# <a name="azure-functions-tools-for-visual-studio"></a>Azure Functions Tools for Visual Studio  

Azure Functions Tools for Visual Studio 2017 は Visual Studio の拡張機能です。C# 関数の開発、テスト、および Azure へのデプロイを可能にします。 Azure Functions を初めて使用する場合は、詳細について、「[Azure Functions の概要](functions-overview.md)」を参照してください。

Azure Functions Tools には、次のような利点があります。 

* ローカル開発用コンピューターで関数を編集、作成、および実行できます。 
* Azure に直接 Azure Functions プロジェクトを発行できます。 
* WebJobs 属性を使用して、定義をバインドするために個別に function.json を維持するのではなく、C# コードで直接関数バインディングを宣言できます。
* コンパイル済み C# 関数を開発およびデプロイできます。 コンパイル済み関数では、C# スクリプト ベースの関数より優れたコールド スタート パフォーマンスが得られます。 
* Visual Studio 開発のすべての利点を得ながら、C# で関数をコーディングできます。 

このトピックでは、Azure Functions Tools for Visual Studio 2017 を使用して、C# で関数を開発する方法を示します。 .NET アセンブリとして Azure にプロジェクトを発行する方法についても説明します。

## <a name="prerequisites"></a>前提条件

Azure Functions Tools をインストールする前に、次のワークロードのいずれかを含む、[Visual Studio 2017 Preview バージョン 15.3](https://www.visualstudio.com/vs/preview/) をインストールする必要があります。

* Azure の開発
* ASP.NET および Web の開発

関数を作成してデプロイするには、以下も必要になります。

* 有効な Azure サブスクリプション Azure サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を利用できます。

* Azure Storage のアカウント ストレージ アカウントを作成する場合は、「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」を参照してください。  

## <a name="install-the-azure-functions-tools"></a>Azure Functions Tools をインストールする

[拡張機能パッケージをダウンロードしてインストールする](https://marketplace.visualstudio.com/vsgallery/e3705d94-7cc3-4b79-ba7b-f43f30774d28)ことができます。また、以下の手順を使用して、Visual Studio からインストールすることもできます。  

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)] 


## <a name="create-an-azure-functions-project"></a>Azure Functions プロジェクトを作成する 

[!INCLUDE [Create a project using the Azure Functions ](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>ローカル開発用のプロジェクトを構成する

Azure Functions テンプレートを使用して、新しいプロジェクトを作成する場合は、次のファイルを含む空の C# プロジェクトを取得します。

* **host.json**: Functions のホストを構成することができます。 これらの設定は、ローカルでの実行時と Azure での実行時の両方に適用されます。 詳細については、[host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) のリファレンス記事を参照してください。
    
* **local.settings.json**: 関数をローカルで実行するときに使用される設定を保持します。 これらの設定は Azure では使用されず、Azure Functions Core Tools で使用されます。 このファイルを使用して、他の Azure サービスに対する接続文字列などの設定を指定します。 新しいキーを、プロジェクトの関数に必要な各接続の **Values** 配列に追加します。 詳細については、Azure Functions Core Tools トピックの[ローカル設定ファイル](functions-run-local.md#local-settings-file)に関する記述を参照してください。

Functions ランタイムでは内部的に Azure Storage アカウントを使用します。 HTTP と webhook 以外のすべてのトリガーの種類については、**Values.AzureWebJobsStorage** キーを有効な Azure Storage アカウントの接続文字列に設定する必要があります。 ストレージ アカウントの接続文字列を設定するには、次のようにします。

1. [Azure Portal](https://portal.azure.com/) で、ストレージ アカウントに移動し、**[すべての設定]** > **[アクセス キー]** の順にクリックしてから、キーのいずれかの**接続文字列**をコピーします。 

2. Visual Studio のプロジェクトで、local.settings.json プロジェクト ファイルを開き、コピーした接続文字列に **AzureWebJobsStorage** キーの値を設定します。

3. 前の手順を繰り返し、関数に必要なその他のすべての接続について、**Values** 配列に一意のキーを追加します。  

## <a name="create-a-function"></a>関数を作成する

コンパイル済みの関数では、関数で使用されるバインディングはコードで属性を適用することで定義されます。 Azure Functions Tools を使用して提供されているテンプレートから関数を作成する場合は、これらの属性が適用されます。 

1. **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックし、**[追加]** > **[新しいアイテム]** の順に選択します。 **[Azure 関数]** を選択し、クラスの **[名前]** を入力して **[追加]** をクリックします。

2. トリガーを選択し、バインドのプロパティを設定して、**[作成]** をクリックします。 次の例は、Queue Storage によってトリガーされる関数を作成する場合の設定を示しています。 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    **QueueStorage** という名前の接続文字列キーが指定されています。これは、local.settings.json ファイルで定義されています。 クラス名 
 
3. 新しく追加されたクラスを確認します。 **FunctionName** 属性に関連付けられている、静的な **Run** メソッドが表示されています。 この属性は、メソッドが関数のエントリ ポイントであることを示します。 

    たとえば、次の C# クラスは基本的な Queue Storage トリガー関数を表します。

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    
    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]        
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    } 
    ````
 
    バインド固有の属性は、エントリ ポイント メソッドに指定された各バインド パラメーターに適用されます。 属性ではパラメーターとしてバインド情報を取ります。 前の例では、最初のパラメーターに **QueueTrigger** 属性が適用されています。これは、キューによってトリガーされる関数を示します。 キュー名および接続文字列の設定名は、パラメーターとして渡されます。  

## <a name="testing-functions"></a>関数のテスト

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

プロジェクトを実行して、デプロイ済みの関数をテストする場合と同じように、コードをテストできます。 詳細については、「[Azure Functions のコードをテストするための戦略](functions-test-a-function.md)」を参照してください。 デバッグ モードで実行している場合は、予期したとおりに Visual Studio でブレークポイントがヒットします。 

キューによってトリガーされる関数のテスト方法の例については、[キューによってトリガーされる関数のクイックスタート チュートリアル](functions-create-storage-queue-triggered-function.md#test-the-function)を参照してください。  

Azure Functions Core Tools の使用の詳細については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。

## <a name="publish-to-azure"></a>Azure に発行する

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="next-steps"></a>次のステップ

Azure Functions Tools の詳細については、[Visual Studio 2017 Tools for Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) ブログ投稿のよく寄せられる質問を参照してください。

Azure Functions Core Tools の詳細については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。
