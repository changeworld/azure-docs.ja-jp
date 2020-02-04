---
title: Visual Studio Code を使用して Azure で初めての関数を作成する
description: Visual Studio Code で Azure Functions 拡張機能を使用して、HTTP によってトリガーされる単純な関数を作成し、Azure に発行します。
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842258"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>クイック スタート:Visual Studio Code を使用して Azure Functions プロジェクトを作成する

この記事では、HTTP 要求に応答する関数を、Visual Studio Code を使用して作成します。 コードをローカルでテストした後、Azure Functions のサーバーレス環境にデプロイします。 このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。 

また、この記事の [CLI ベースのバージョン](functions-create-first-azure-function-azure-cli.md)も存在します。

## <a name="prerequisites"></a>前提条件

+ [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。 
::: zone pivot="programming-language-csharp"
+ Visual Studio Code 用の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) または [Python 3.6](https://www.python.org/downloads/release/python-368/)。どちらも Azure Functions でサポートされます。 Python 3.8 はまだサポートされていません。 

+ Visual Studio Code 用の [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 以上](https://www.microsoft.com/net/download)

+ [Visual Studio Code 用 PowerShell 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)。 
::: zone-end

+ Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 

+ アクティブなサブスクリプションが含まれる [Azure アカウント](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

## <a name="create-an-azure-functions-project"></a>ローカル プロジェクトを作成する 

このセクションでは、Visual Studio Code を使用し、選択した言語でローカル Azure Functions プロジェクトを作成します。 後からこの記事の中で、関数コードを Azure に発行します。 

1. アクティビティ バーで Azure アイコンを選択し、 **[Azure: Functions]** 領域の **[新しいプロジェクトの作成]** アイコンを選択します。

    ![[新しいプロジェクトの作成] を選択する](media/functions-create-first-function-vs-code/create-new-project.png)

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトで、次の情報を入力します。

    ::: zone pivot="programming-language-csharp"

    | Prompt | Value | 
    | ------ | ----- | 
    | Select a language for your function project (関数プロジェクトの言語を選択してください) | C# |
    | Select a version (バージョンを選択してください) | Azure Functions v2 | 
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | HTTP トリガー | 
    | Provide a function name (関数名を指定してください) | HttpExample | 
    | Provide a namespace (名前空間を指定してください) | My.Functions | 
    | 承認レベル | Anonymous | 
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | Add to workspace (ワークスペースに追加) |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Prompt | Value | 
    | ------ | ----- | 
    | Select a language for your function project (関数プロジェクトの言語を選択してください) | JavaScript | 
    | Select a version (バージョンを選択してください) | Azure Functions v2 | 
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | HTTP トリガー | 
    | Provide a function name (関数名を指定してください) | HttpExample | 
    | 承認レベル | Anonymous | 
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | Add to workspace (ワークスペースに追加) |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Prompt | Value | 
    | ------ | ----- | 
    | Select a language for your function project (関数プロジェクトの言語を選択してください) | TypeScript | 
    | Select a version (バージョンを選択してください) | Azure Functions v2 | 
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | HTTP トリガー | 
    | Provide a function name (関数名を指定してください) | HttpExample | 
    | 承認レベル | Anonymous | 
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | Add to workspace (ワークスペースに追加) |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Prompt | Value | 
    | ------ | ----- | 
    | Select a language for your function project (関数プロジェクトの言語を選択してください) | PowerShell | 
    | Select a version (バージョンを選択してください) | Azure Functions v2 | 
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | HTTP トリガー | 
    | Provide a function name (関数名を指定してください) | HttpExample | 
    | 承認レベル | Anonymous | 
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | Add to workspace (ワークスペースに追加) |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Prompt | Value | 
    | ------ | ----- | 
    | Select a language for your function project (関数プロジェクトの言語を選択してください) | Python | 
    | Select a version (バージョンを選択してください) | Azure Functions v2 | 
    | Select a Python alias to create a virtual environment (仮想環境を作成する Python エイリアスを選択してください) | Python エイリアス | 
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | HTTP トリガー | 
    | Provide a function name (関数名を指定してください) | HttpExample | 
    | 承認レベル | Anonymous | 
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | Add to workspace (ワークスペースに追加) | 

    ::: zone-end

1. Visual Studio Code によって次の処理が実行されます。

    + [host.json](functions-host-json.md) と [local.settings.json](functions-run-local.md#local-settings-file) 構成ファイルを含んだ Azure Functions プロジェクトを新しいワークスペースに作成します。 

    ::: zone pivot="programming-language-csharp"

    + 関数を実装する [HttpExample.cs クラス ライブラリ ファイル](functions-dotnet-class-library.md#functions-class-library-project)を作成します。

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + ルート フォルダーに package.json ファイルを作成します。

    + [function.json 定義ファイル](functions-reference-node.md#folder-structure)と [index.js ファイル](functions-reference-node.md#exporting-a-function) (関数コードを含む Node.js ファイル) の格納先となる HttpExample フォルダーを作成します。
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + package.json ファイルと tsconfig.json ファイルをルート フォルダーに作成します。

    + [function.json 定義ファイル](functions-reference-node.md#folder-structure)と [index.ts ファイル](functions-reference-node.md#typescript) (関数コードを含む TypeScript ファイル) の格納先となる HttpExample フォルダーを作成します。
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + [function.json 定義ファイル](functions-reference-python.md#programming-model)と run.ps1 ファイル (関数コードを含むファイル) の格納先となる HttpExample フォルダーを作成します。
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Functions に必要なパッケージをリストしたプロジェクトレベルの requirements.txt ファイルを作成します。
    
    + [function.json 定義ファイル](functions-reference-python.md#programming-model)と \_\_init\_\_.py ファイル (関数コードを含むファイル) の格納先となる HttpExample フォルダーを作成します。
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

関数がローカル コンピューター上で正常に動作することを確認したら、Visual Studio Code を使用してプロジェクトを直接 Azure に発行します。 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Azure で関数を実行する

1. **出力**パネルから HTTP トリガーの URL をコピーします。 前と同様に、この URL の末尾にクエリ文字列 `name` を `?name=<yourname>` として追加してから、要求を実行します。

    HTTP によってトリガーされる関数を呼び出す URL は、次の形式である必要があります。

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. HTTP 要求のこの新しい URL をブラウザーのアドレス バーに貼り付けます。 関数によって返されたリモート GET 要求に対するブラウザーでの応答を次の例に示します。 

    ![ブラウザーでの関数の応答](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この後、[関数に Azure Storage キュー バインドを追加する](functions-add-output-binding-storage-queue-vs-code.md)手順に進む場合、既存の作業をベースにするので、リソースはすべてそのままにしておく必要があります。

それ以外の場合は、追加コストの発生を避けるために、次の手順に従って関数アプリとその関連リソースを削除してください。

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Functions のコストについて詳しくは、「[従量課金プランのコストの見積もり](functions-consumption-costs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Visual Studio Code を使用して、HTTP によってトリガーされる単純な関数を含む関数アプリを作成しました。 次の記事では、出力バインディングを追加してその関数を拡張します。 このバインディングでは、HTTP 要求の文字列が Azure Queue Storage キュー内のメッセージに書き込まれます。 

> [!div class="nextstepaction"]
> [関数に Azure Storage キュー バインドを追加する](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
