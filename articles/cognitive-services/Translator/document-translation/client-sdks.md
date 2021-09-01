---
title: C#/.NET または Python のドキュメント翻訳クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: クラウドベースのバッチ ドキュメント翻訳サービスおよびプロセスには、Translator C#/.NET または Python クライアント ライブラリ (SDK) を使用します
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 07/06/2021
ms.author: lajanuar
ms.openlocfilehash: b7bcf5339f6bff6a0f055e2016bcd3e12bfd5f45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724505"
---
# <a name="document-translation-client-library-sdks"></a>ドキュメント翻訳クライアント ライブラリ SDK
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD001 -->
[ドキュメント翻訳](overview.md)は、[Azure Translator](../translator-overview.md) サービスのクラウドベースの機能です。 元のドキュメントの構造と形式を維持したまま、ドキュメント全体を翻訳したり、さまざまなファイル形式のバッチ ドキュメント翻訳を処理したりすることができます。 この記事では、ドキュメント翻訳サービスの C#/.NET および Python クライアント ライブラリを使用する方法について説明します。 REST API については、[クイックスタート](get-started-with-document-translation.md) ガイドを参照してください。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)。  アカウントがない場合は、[**無料アカウントを作成**](https://azure.microsoft.com/free/)できます。

* [**単一サービス Translator リソース**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (マルチサービス Cognitive Services リソースとは **異なる**)。

* [**Azure Blob Storage アカウント**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 Azure BLOB ストレージ アカウント内に、ソースおよびターゲット ファイル用の [**コンテナーを作成**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)します。

  * **ソースのコンテナー**。 このコンテナーは、翻訳対象のファイルをアップロードする場所です (必須)。
  * **ターゲットのコンテナー**。 このコンテナーは、翻訳されたファイルを格納する場所です (必須)。

* ソースおよびターゲット コンテナー用に Shared Access Signature (SAS) トークンを作成する必要もあります。 `sourceUrl` と `targetUrl` には Shared Access Signature (SAS) トークンを含める必要があり、クエリ文字列として追加します。 トークンは、コンテナーまたは特定の BLOB に割り当てることができます。 「[**ドキュメント翻訳プロセスの SAS トークンを作成する**](create-sas-tokens.md)」を "*参照してください*"。

  * **ソース** のコンテナーまたは BLOB には、**読み取り** と **一覧表示** のアクセス権が指定されている必要があります。
  * **ターゲット** のコンテナーまたは BLOB には、**書き込み** と **一覧表示** のアクセス権が指定されている必要があります。

詳細については、[SAS トークンの作成](create-sas-tokens.md)に関するページを "*参照してください*"。

## <a name="client-libraries"></a>クライアント ライブラリ

### <a name="cnet"></a>[C#/.NET](#tab/csharp)

| [パッケージ (NuGet)][documenttranslation_nuget_package] | [クライアント ライブラリ][documenttranslation_client_library_docs] |  [REST API][documenttranslation_rest_api] | [製品ドキュメント][documenttranslation_docs] | [サンプル][documenttranslation_samples] |

> [!IMPORTANT]
> これはプレリリース バージョンのドキュメント翻訳 SDK です。 お客様が早期にアクセスしてフィードバックを提供できるよう、紹介用としての位置付けで利用できようになっています。 プレリリース バージョンはまだ開発中であり、変更される可能性があります。また、特定の機能がサポートされていない場合や、機能に制限がある場合があります。これらは実稼働アプリケーションでは使用しないでください。

### <a name="set-up-your-project"></a>プロジェクトの設定

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`batch-document-translation` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。

```console
dotnet new console -n batch-document-translation
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次のコマンドでアプリケーションをビルドします。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

アプリケーション ディレクトリ内で、次のいずれかの方法を使用して .NET 用のドキュメント翻訳クライアント ライブラリをインストールします。

#### <a name="net-cli"></a>**.NET CLI**

```console
dotnet add package Azure.AI.Translation.Document --version 1.0.0-beta.2
```

#### <a name="nuget-package-manager"></a>**NuGet パッケージ マネージャー**

```console
Install-Package Azure.AI.Translation.Document -Version 1.0.0-beta.2
```

#### <a name="nuget-packagereference"></a>**NuGet PackageReference**

```xml
<ItemGroup>
    <!-- ... -->
<PackageReference Include="Azure.AI.Translation.Document" Version="1.0.0-beta.2" />
    <!-- ... -->
</ItemGroup>
```

プロジェクト ディレクトリから、好みのエディターまたは IDE で Program.cs ファイルを開きます。 ディレクティブを使用して以下を追加します。

```csharp
using Azure;
using Azure.AI.Translation.Document;

using System;
using System.Threading;
```

アプリケーションの **Program** クラスで、サブスクリプション キーとカスタム エンドポイントの変数を作成します。 詳細については、「[カスタム ドメイン名とサブスクリプション キー](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)」を "*参照してください*"。

```csharp
private static readonly string endpoint = "<your custom endpoint>";
private static readonly string subscriptionKey = "<your subscription key>";
```

### <a name="translate-a-document-or-batch-files"></a>ドキュメントまたはバッチ ファイルを翻訳する

* 1 つの BLOB コンテナー内の 1 つ以上のドキュメントについて翻訳操作を開始するには、`StartTranslationAsync` メソッドを呼び出します。

* `StartTranslationAsync` を呼び出すには、以下のパラメーターを含む `DocumentTranslationInput` オブジェクトを初期化する必要があります。

* **sourceUri**。 翻訳するドキュメントが含まれるソース コンテナーの SAS URI。
* **targetUri**。翻訳されたドキュメントが書き込まれるターゲット コンテナーの SAS URI。
* **targetLanguageCode**。 翻訳されるドキュメントの言語コード。 言語コードは、[言語サポート](../language-support.md)に関するページに記載されています。

```csharp

public void StartTranslation() {
  Uri sourceUri = new Uri("<sourceUrl>");
  Uri targetUri = new Uri("<targetUrl>");

  DocumentTranslationClient client = new DocumentTranslationClient(new Uri(endpoint), new AzureKeyCredential(subscriptionKey));

  DocumentTranslationInput input = new DocumentTranslationInput(sourceUri, targetUri, "es")

  DocumentTranslationOperation operation = await client.StartTranslationAsync(input);

  await operation.WaitForCompletionAsync();

  Console.WriteLine($ "  Status: {operation.Status}");
  Console.WriteLine($ "  Created on: {operation.CreatedOn}");
  Console.WriteLine($ "  Last modified: {operation.LastModified}");
  Console.WriteLine($ "  Total documents: {operation.DocumentsTotal}");
  Console.WriteLine($ "    Succeeded: {operation.DocumentsSucceeded}");
  Console.WriteLine($ "    Failed: {operation.DocumentsFailed}");
  Console.WriteLine($ "    In Progress: {operation.DocumentsInProgress}");
  Console.WriteLine($ "    Not started: {operation.DocumentsNotStarted}");

  await foreach(DocumentStatusResult document in operation.Value) {
    Console.WriteLine($ "Document with Id: {document.DocumentId}");
    Console.WriteLine($ "  Status:{document.Status}");
    if (document.Status == TranslationStatus.Succeeded) {
      Console.WriteLine($ "  Translated Document Uri: {document.TranslatedDocumentUri}");
      Console.WriteLine($ "  Translated to language: {document.TranslatedTo}.");
      Console.WriteLine($ "  Document source Uri: {document.SourceDocumentUri}");
    }
    else {
      Console.WriteLine($ "  Error Code: {document.Error.ErrorCode}");
      Console.WriteLine($ "  Message: {document.Error.Message}");
    }
  }
}
```

これで完了です。 .NET クライアント ライブラリを使用して BLOB コンテナー内のドキュメントを翻訳するプログラムを作成しました。

### <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
 > [**C# のコード サンプルをさらに確認する**](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.Translation.Document_1.0.0-beta.2/sdk/translation/Azure.AI.Translation.Document/samples)

<!-- LINKS -->

[documenttranslation_nuget_package]: https://www.nuget.org/packages/Azure.AI.Translation.Document/1.0.0-beta.2
[documenttranslation_client_library_docs]: /dotnet/api/azure.ai.translation.document
[documenttranslation_docs]: overview.md
[documenttranslation_rest_api]: reference/rest-api-guide.md
[documenttranslation_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/translation/Azure.AI.Translation.Document/samples

### <a name="python"></a>[Python](#tab/python)

| [パッケージ (PyPI)][python-dt-pypi] |  [クライアント ライブラリ][python-dt-client-library] |  [REST API][python-rest-api] | [製品ドキュメント][python-dt-product-docs] | [サンプル][python-dt-samples] |

> [!IMPORTANT]
> これはプレリリース バージョンのドキュメント翻訳 SDK です。 お客様が早期にアクセスしてフィードバックを提供できるよう、紹介用としての位置付けで利用できようになっています。 プレリリース バージョンはまだ開発中であり、変更される可能性があります。また、特定の機能がサポートされていない場合や、機能に制限がある場合があります。これらは実稼働アプリケーションでは使用しないでください。

### <a name="set-up-your-project"></a>プロジェクトの設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

まだの場合は [Python](https://www.python.org/downloads/) をインストールしてから、最新バージョンの Translator クライアント ライブラリをインストールします。

```console
pip install azure-ai-translation-document
```

### <a name="create-your-application"></a>アプリケーションを作成する

お気に入りのエディターまたは IDE で、新しい Python アプリケーションを作成します。 次に、次のライブラリをインポートします。

```python
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.translation.document import DocumentTranslationClient
```

リソース サブスクリプション キー、カスタム エンドポイント、sourceUrl、targetUrl の変数を作成します。 詳細については、「[カスタム ドメイン名とサブスクリプション キー](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)」を "*参照してください*"

```python
 subscriptionKey = "<your-subscription-key>"
 endpoint = "<your-custom-endpoint>"
 sourceUrl = "<your-container-sourceUrl>"
 targetUrl = "<your-container-targetUrl>"
```

### <a name="translate-a-document-or-batch-files"></a>ドキュメントまたはバッチ ファイルを翻訳する

```python
client = DocumentTranslationClient(endpoint, AzureKeyCredential(subscriptionKey))

    poller = client.begin_translation(sourceUrl, targetUrl, "fr")
    result = poller.result()

    print("Status: {}".format(poller.status()))
    print("Created on: {}".format(poller.details.created_on))
    print("Last updated on: {}".format(poller.details.last_updated_on))
    print("Total number of translations on documents: {}".format(poller.details.documents_total_count))

    print("\nOf total documents...")
    print("{} failed".format(poller.details.documents_failed_count))
    print("{} succeeded".format(poller.details.documents_succeeded_count))

    for document in result:
        print("Document ID: {}".format(document.id))
        print("Document status: {}".format(document.status))
        if document.status == "Succeeded":
            print("Source document location: {}".format(document.source_document_url))
            print("Translated document location: {}".format(document.translated_document_url))
            print("Translated to language: {}\n".format(document.translated_to))
        else:
            print("Error Code: {}, Message: {}\n".format(document.error.code, document.error.message))
```

これで完了です。 Python クライアント ライブラリを使用して BLOB コンテナー内のドキュメントを翻訳するプログラムを作成しました。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Python のコード サンプルをさらに確認する](https://github.com/Azure/azure-sdk-for-python/tree/azure-ai-translation-document_1.0.0b1/sdk/translation/azure-ai-translation-document/samples)

<!-- LINKS -->
[python-dt-pypi]: https://aka.ms/azsdk/python/texttranslation/pypi
[python-dt-client-library]: https://aka.ms/azsdk/python/documenttranslation/docs
[python-rest-api]: reference/rest-api-guide.md
[python-dt-product-docs]: overview.md
[python-dt-samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/translation/azure-ai-translation-document/samples

---