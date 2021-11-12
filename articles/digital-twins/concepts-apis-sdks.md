---
title: Azure Digital Twins API および SDK
titleSuffix: Azure Digital Twins
description: SDK ヘルパー クラスと一般的な使用に関する注意事項など、Azure Digital Twins API と SDK のオプションについて説明します。
author: baanders
ms.author: baanders
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 1fcbeeb532e813535cc8f3adcf02b2c2e990287e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500205"
---
# <a name="azure-digital-twins-apis-and-sdks"></a>Azure Digital Twins API および SDK

この記事では、使用可能な Azure Digital Twins API の概要と、API を操作するためのメソッドについて説明します。 REST API は、関連付けられている Swagger を使って直接使用することも ([Postman](how-to-use-postman.md) などのツールを使用)、SDK を通して使用することもできます。

Azure Digital Twins には、インスタンスとその要素を管理するための **コントロール プレーン API**、**データ プレーン API**、**SDK** が用意されています。 
* コントロール プレーン API は、[Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) API であり、インスタンスの作成や削除などのリソース管理操作に対応しています。 
* データ プレーン API は、Azure Digital Twins API であり、モデル、ツイン、グラフの管理などのデータ管理操作に使用されます。
* この SDK は、既存の API を利用して、カスタム アプリケーションの開発が Azure Digital Twins を役立てることで容易になるようにします。 コントロール プレーン SDK は、[.NET (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true) と [Java](/java/api/overview/azure/digitaltwins/resourcemanagement?view=azure-java-stable&preserve-view=true) で使用できます。データ プレーン SDK は、[.NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)、[Java](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true)、[JavaScript](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true)、[Python](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true) で使用できます。

## <a name="overview-control-plane-apis"></a>概要: コントロール プレーン API

コントロール プレーン API は、[ARM](../azure-resource-manager/management/overview.md) API であり、Azure Digital Twins インスタンス全体を管理するために使用されます。そのため、インスタンス全体の作成や削除などの操作に対応しています。 また、エンドポイントの作成と削除にもこれらの API を使用します。

最新のコントロール プレーン API のバージョンは、_**2020-12-01**_ です。

コントロール プレーン API を使用するには
* [コントロール プレーン Swagger リポジトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)の最新の Swagger フォルダーを参照することで、API を直接呼び出すことができます。 このフォルダーには、使用法を示す例が保存されているフォルダーも含まれています。
* 現在、コントロール API の SDK には、次の言語でアクセスできます。
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([参照 [自動生成]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([ソース](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [Java](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([参照 [自動生成]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([ソース](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([ソース](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([ソース](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([ソース](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

また、コントロール プレーン API の演習を行うには、[Azure portal](https://portal.azure.com) および [CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) を使用して Azure Digital Twins を操作します。

## <a name="overview-data-plane-apis"></a>概要: データ プレーン API

データ プレーン API は、Azure Digital Twins API であり、Azure Digital Twins インスタンス内の要素を管理するために使用されます。 たとえば、ルートの作成、モデルのアップロード、リレーションシップの作成、ツインの管理などの操作が含まれ、大まかに次のカテゴリーに分類できます。
* **DigitalTwinModels** - DigitalTwinModels カテゴリには、Azure Digital Twins のインスタンスの [モデル](concepts-models.md)を管理するための API が含まれています。 管理アクティビティには、DTDL で作成されたモデルのアップロード、検証、取得、および削除が含まれます。
* **DigitalTwins** - DigitalTwins カテゴリには、開発者が Azure Digital Twins のインスタンスで [Digital Twins](concepts-twins-graph.md) とその関係を作成、変更、および削除するための API が含まれています。
* **Query** - 開発者は、Query カテゴリを使用して、複数のリレーションシップにわたる [ツイングラフで一連の Digital Twins を検索できます](how-to-query-graph.md)。
* **Event Routes** - Event Routes カテゴリには、システムを通してダウンストリーミング サービスに [データをルーティングする](concepts-route-events.md) API が含まれています。

最新のデータ プレーン API のバージョンは、_**2020-10-31**_ です。

データ プレーン API を使用するには
* API を直接呼び出すには、次の操作を実行します。
   - [データ プレーン Swagger リポジトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)の最新の Swagger フォルダーを参照する。 このフォルダーには、使用法を示す例が保存されているフォルダーも含まれています。 
   - [API リファレンス ドキュメント](/rest/api/azure-digitaltwins/)を参照する。
* **.NET (C#)** SDK を使用できます。 .NET SDK は次の方法で使用します。
   - NuGet からパッケージ [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core) を表示して追加できます。 
   - [SDK のリファレンス ドキュメント](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)を確認する。
   - GitHub で、サンプルのフォルダーを含む SDK ソース [Azure IoT Digital Twins client library for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) を探す。 
   - 詳細な情報と使用例については、この記事の「[.NET (C#) SDK (データ プレーン)](#net-c-sdk-data-plane)」セクションを参照してください。
* **Java** SDK を使用できます。 Java SDK を使用するには、次のようにします。
   - Maven からパッケージを表示してインストールする。[`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - [SDK のリファレンス ドキュメント](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true)を確認する
   - GitHub で SDK のソースを探す。[Azure IoT Digital Twins client library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* **JavaScript** SDK を使用できます。 JavaScript SDK を使用するには、次のようにします。
   - npm からパッケージを表示してインストールする。[JavaScript 用 Azure Azure Digital Twins コア クライアント ライブラリ](https://www.npmjs.com/package/@azure/digital-twins-core)。
   - [SDK のリファレンス ドキュメント](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true)を確認する。
   - GitHub で SDK のソースが見つかります。[JavaScript 用 Azure Digital Twins コア クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* **Python** SDK を使用できます。 Python SDK を使用するには、次のようにします。
   - PyPi からのパッケージを表示してインストールする。[Python 用 Azure Azure Digital Twins Core クライアント ライブラリ](https://pypi.org/project/azure-digitaltwins-core/)。
   - [SDK のリファレンス ドキュメント](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true)を確認する。
   - GitHub で SDK のソースが見つかります。[Python 用 Azure Digital Twins コア クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)

また、データ プレーン API の演習を行うには、[CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) を使用して Azure Digital Twins を操作します。

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (データ プレーン)

Azure Digital Twins .NET (C#) SDK は、Azure SDK for .Net に含まれています。 これはオープン ソースであり、Azure Digital Twins データ プレーン API に基づいています。

> [!NOTE]
> SDK の設計の詳細については、[Azure SDK の一般的な設計原則](https://azure.github.io/azure-sdk/general_introduction.html)と特定の [.NET 設計ガイドライン](https://azure.github.io/azure-sdk/dotnet_introduction.html)を参照してください。

SDK を使用するには、NuGet パッケージ **Azure.DigitalTwins.Core** をプロジェクトに含めます。 また、**Azure.Identity** パッケージの最新バージョンも必要です。 Visual Studio では、NuGet パッケージ マネージャーを使用してこれらのパッケージを追加できます。そのためには、 *[ツール]、[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]* の順に選択します。 または、.NET コマンドライン ツールを、次の NuGet パッケージ リンクにあるコマンドと共に使用し、これらのパッケージをプロジェクトに追加することもできます。
* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core): [Azure Digital Twins SDK for .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) のパッケージです。 
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity): Azure に対する認証を支援するツールを提供するライブラリーです。

実際の API の使用方法の詳細については、[クライアント アプリをコーディングする](tutorial-code.md)を参照してください。 

### <a name="serialization-helpers"></a>シリアル化のヘルパー

シリアル化のヘルパーは、基本情報にアクセスするためのツイン データを、迅速に作成または逆シリアル化するために SDK 内で使用できるヘルパー関数です。 コア SDK メソッドでは、既定でツイン データが JSON として返されるため、それらのヘルパー クラスを使用して、ツイン データをさらに分割できます。

使用できるヘルパー クラスは次のとおりです。
* `BasicDigitalTwin`: 一般的にデジタル ツインのコア データを表します
* `BasicDigitalTwinComponent`: 一般的に `BasicDigitalTwin` の `Contents` プロパティ内のコンポーネントを表します
* `BasicRelationship`: 一般的にリレーションシップのコア データを表します
* `DigitalTwinsJsonPropertyName`: カスタムのデジタル ツイン型での JSON のシリアル化と逆シリアル化で使用される文字列定数が格納されています

## <a name="general-apisdk-usage-notes"></a>一般的な API/SDK の使用上の注意

> [!NOTE]
> 現時点では、Azure Digital Twins では **クロス オリジン リソース共有 (CORS)** がサポートされないことに注意してください。 影響と解決方法の詳細については、 [「クロス オリジン リソース共有 (CORS)」](concepts-security.md#cross-origin-resource-sharing-cors) セクション (*概念:Azure Digital Twins ソリューションのセキュリティ* に関するページを参照してください。

次のリストには、API と SDK の使用に関する詳細情報と一般的なガイドラインが示されています。

* Postman などの HTTP REST テスト ツールを使用して、Azure Digital Twins の API を直接呼び出すことができます。 このプロセスの詳細については、[Postman で API 要求を行う](how-to-use-postman.md)を参照してください。
* SDK を使用するには、`DigitalTwinsClient` クラスをインスタンス化します。 コンストラクターには、`Azure.Identity` パッケージ内の各種認証情報を使用して取得できる資格情報が必要です。 `Azure.Identity` の詳細については、[名前空間に関するドキュメント](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)を参照してください。 
* 作業を開始するときには `InteractiveBrowserCredential` が役立ちますが、[マネージド ID](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) の資格情報など、他にもいくつかのオプションがあります。これを使用して、Azure Digital Twins に対して [MSI を使用してセットアップされた Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet) を認証できます。 `InteractiveBrowserCredential` の詳細については、[クラスのドキュメント](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)を参照してください。
* Azure Digital Twins API への要求には、Azure Digital Twins インスタンスが存在している、同じ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) テナントの一部であるユーザーまたはサービス プリンシパルが必要です。 Azure Digital Twins エンドポイントの悪意のあるスキャンを防ぐために、発信元のテナントの外部からのアクセス トークンを含む要求には、"404 サブドメインが見つかりませんでした" というエラー メッセージが返されます。 このエラーは、ユーザーまたはサービス プリンシパルに [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) コラボレーションを通じて Azure Digital Twins データ所有者または Azure Digital Twins データ閲覧者のロールが与えられた "*場合でも*" 返されます。 複数のテナントにわたるアクセスを実現する方法については、[アプリ認証コードを作成する](how-to-authenticate-client.md#authenticate-across-tenants)を参照してください。
* すべてのサービス API 呼び出しは、`DigitalTwinsClient` クラスのメンバー関数として公開されます。
* すべてのサービス関数には、同期バージョンと非同期バージョンが存在します。
* すべてのサービス関数は、400 以上のリターン状態に対して例外をスローします。 `try` セクションに呼び出しをラップし、少なくとも `RequestFailedExceptions` をキャッチします。 この種類の例外の詳細については、[リファレンス ドキュメント](/dotnet/api/azure.requestfailedexception?view=azure-dotnet&preserve-view=true)を参照してください。
* ほとんどのサービス メソッドは `Response<T>` (または非同期呼び出しの場合は `Task<Response<T>>`) を返します。 `T` は、サービス呼び出しで返されるオブジェクトのクラスです。 [応答](/dotnet/api/azure.response-1?view=azure-dotnet&preserve-view=true)クラスは、サービスの戻り値をカプセル化し、その `Value` フィールドに戻り値を表示します。  
* ページングされた結果を含むサービス メソッドは、結果として `Pageable<T>` または `AsyncPageable<T>` を返します。 `Pageable<T>` クラスの詳細についてはその[リファレンス ドキュメント](/dotnet/api/azure.pageable-1?view=azure-dotnet&preserve-view=true)を、`AsyncPageable<T>` の詳細についてはその[リファレンス ドキュメント](/dotnet/api/azure.asyncpageable-1?view=azure-dotnet&preserve-view=true)を参照してください。
* `await foreach` ループを使用して、ページングした結果を反復処理できます。 このプロセスの詳細については、[関連するドキュメント](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)を参照してください。
* 基になる SDK は `Azure.Core` です。 SDK のインフラストラクチャと種類については、[Azure 名前空間のドキュメント](/dotnet/api/azure?view=azure-dotnet&preserve-view=true)を参照してください。


サービス メソッドは、可能な限り、厳密に型指定されたオブジェクトを返します。 ただし、Azure Digital Twins は、実行時にユーザーによって構成されたカスタム モデル (サービスにアップロードされた DTDL モデルを使用) に基づいているため、多くのサービス API はツイン データを JSON 形式で取得して返します。

## <a name="monitor-api-metrics"></a>API メトリックの監視

要求、待機時間、失敗率などの API メトリックは、[Azure portal](https://portal.azure.com/) で見ることができます。 

portal のホームページで、Azure Digital Twins インスタンスを検索して詳細を取得します。 Azure Digital Twins インスタンスのメニューから **[メトリック]** オプションを選択して、 *[メトリック]* ページを表示します。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット。":::

ここから、インスタンスのメトリックを表示したり、カスタム ビューを作成したりできます。

## <a name="next-steps"></a>次のステップ

Postman を使用して API に直接要求を行う方法を参照してください。
* [Postman で API 要求を行う](how-to-use-postman.md)

または、このチュートリアルを使用してクライアント アプリを作成し、.NET SDK を使用する練習を行います。
* [クライアント アプリをコーディングする](tutorial-code.md)
