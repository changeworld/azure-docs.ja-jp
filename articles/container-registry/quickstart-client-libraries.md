---
title: クイック スタート - クライアント ライブラリを使用してコンテナー レジストリのコンテンツを管理する
description: このクイックスタートは、Azure Container Registry クライアント ライブラリを使用して、リポジトリ、イメージ、成果物を管理するために使用します
author: dlepow
ms.topic: quickstart
ms.date: 10/05/2021
ms.author: danlep
ms.custom: ''
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 6af32e7bd841960cdf1fd3aef6af50171cc1e594
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007668"
---
# <a name="quickstart-use-the-azure-container-registry-client-libraries"></a>クイック スタート: Azure Container Registry クライアント ライブラリを使用する

::: zone pivot="programming-language-csharp, programming-language-java, programming-language-javascript, programming-language-python"

この記事では、Azure Container Registry のクライアント ライブラリの使用を開始する方法について説明します。 イメージと成果物に対するデータ プレーン操作のコード例を試す場合は、こちらの手順に従います。

Azure Container Registry のクライアント ライブラリを使用して、次のことを行います。

* レジストリ内のイメージまたは成果物を一覧表示する
* イメージと成果物、リポジトリ、タグのメタデータを取得する
* レジストリ項目の読み取り、書き込み、削除プロパティを設定する
* イメージと成果物、リポジトリ、タグを削除する

Azure Container Registry には、レジストリの作成や更新など、コントロール プレーン操作用の管理ライブラリもあります。 

## <a name="prerequisites"></a>前提条件

* このライブラリを使用するには、[Azure サブスクリプション](https://azure.microsoft.com/free/)と Azure コンテナー レジストリが必要です。

    新しい Azure コンテナー レジストリを作成するには、[Azure portal](container-registry-get-started-portal.md)、[Azure PowerShell](container-registry-get-started-powershell.md)、または [Azure CLI](container-registry-get-started-azure-cli.md) を使用できます。 Azure CLI を使う例を次に示します。

    ```azurecli
    az acr create --name MyContainerRegistry --resource-group MyResourceGroup \
        --location westus --sku Basic
    ```

* 1 つ以上のコンテナー イメージをレジストリにプッシュします。 手順については、「[Docker CLI を使用した、Azure のコンテナー レジストリへの最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)」を参照してください。

## <a name="key-concepts"></a>主要な概念

* Azure コンテナー レジストリには、"*コンテナー イメージ*" と [OCI 成果物](container-registry-oci-artifacts.md)が格納されます。 
* イメージまたは成果物は、"*マニフェスト*"と "*レイヤー*" で構成されます。 
* マニフェストは、イメージまたは成果物を構成するレイヤーを記述します。 これは、その "*ダイジェスト*" で一意に識別されます。 
* イメージまたは成果物に "*タグ付け*" して、人間が読み取り可能な別名を提供することもできます。 イメージまたは成果物には、0 個以上のタグを関連付けることができ、各タグによって画像が一意に識別されます。 
* 同じ名前を共有しているがタグが異なるイメージまたは成果物のコレクションは、"*リポジトリ*" です。

詳細については、「[レジストリ、リポジトリ、成果物について](container-registry-concepts.md)」を参照してください。

::: zone-end

::: zone pivot="programming-language-csharp"

## <a name="get-started"></a>はじめに

[ソース コード][dotnet_source] | [パッケージ (NuGet)][dotnet_package] | [API リファレンス][dotnet_docs] | [サンプル][dotnet_samples]

Azure Container Registry インスタンスに接続できる .NET アプリケーション コードを開発するには、`Azure.Containers.ContainerRegistry` ライブラリが必要です。

### <a name="install-the-package"></a>パッケージをインストールする

[NuGet][nuget] を使用して .NET 用の Azure Container Registry クライアント ライブラリをインストールします。

```Powershell
dotnet add package Azure.Containers.ContainerRegistry --prerelease
```

## <a name="authenticate-the-client"></a>クライアントを認証する

アプリケーションをレジストリに接続するには、それによって認証できる `ContainerRegistryClient` を作成する必要があります。 [Azure ID ライブラリ][dotnet_identity]を使用して、対応する Azure サービスで Azure SDK クライアントを認証するための Azure Active Directory サポートを追加します。  

アプリケーションをローカルで開発およびデバッグする場合は、独自のユーザーを使用して自身のレジストリで認証できます。 これを実現する 1 つの方法は、[Azure CLI を使用してユーザーを認証](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity#authenticating-via-the-azure-cli)し、この環境からアプリケーションを実行することです。 `DefaultAzureCredential` で認証するように構築されたクライアントをアプリケーションで使用している場合、指定されたエンドポイントのレジストリで正しく認証されます。  

```C#
// Create a ContainerRegistryClient that will authenticate to your registry through Azure Active Directory
Uri endpoint = new Uri("https://myregistry.azurecr.io");
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });
```

ローカルとデプロイの両方の環境で `DefaultAzureCredential` を使用して認証するその他の方法については、[Azure ID README][dotnet_identity] を参照してください。 パブリックでない Azure クラウド内のレジストリに接続するには、[API リファレンス][dotnet_docs]を参照してください。

Azure Container Registry での Azure AD の使用に関する詳細については、[認証の概要](container-registry-authentication.md)に関するページを参照してください。

## <a name="examples"></a>例

各サンプルでは、`https://` プレフィックスとログイン サーバーの名前を含む文字列に設定された `REGISTRY_ENDPOINT` 環境変数 (例: "https://myregistry.azurecr.io") があることを前提としています。

次のサンプルでは、タスクを返す非同期 API を使用します。 同期 API も使用できます。

### <a name="list-repositories-asynchronously"></a>リポジトリを非同期的に一覧表示する

レジストリ内のリポジトリのコレクションを反復処理します。

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="set-artifact-properties-asynchronously"></a>成果物のプロパティを非同期的に設定する

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="delete-images-asynchronously"></a>イメージを非同期的に削除する

```C# Snippet:ContainerRegistry_Tests_Samples_DeleteImageAsync
using System.Linq;
using Azure.Containers.ContainerRegistry;
using Azure.Identity;

// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Iterate through repositories
AsyncPageable<string> repositoryNames = client.GetRepositoryNamesAsync();
await foreach (string repositoryName in repositoryNames)
{
    ContainerRepository repository = client.GetRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    AsyncPageable<ArtifactManifestProperties> imageManifests =
        repository.GetManifestPropertiesCollectionAsync(orderBy: ArtifactManifestOrderBy.LastUpdatedOnDescending);

    // Delete images older than the first three.
    await foreach (ArtifactManifestProperties imageManifest in imageManifests.Skip(3))
    {
        RegistryArtifact image = repository.GetArtifact(imageManifest.Digest);
        Console.WriteLine($"Deleting image with digest {imageManifest.Digest}.");
        Console.WriteLine($"   Deleting the following tags from the image: ");
        foreach (var tagName in imageManifest.Tags)
        {
            Console.WriteLine($"        {imageManifest.RepositoryName}:{tagName}");
            await image.DeleteTagAsync(tagName);
        }
        await image.DeleteAsync();
    }
}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="get-started"></a>はじめに

[ソース コード][java_source] | [パッケージ (Maven)][java_package] | [API リファレンス][java_docs] | [サンプル][java_samples]

### <a name="currently-supported-environments"></a>現在サポートされている環境

* [Java Development Kit (JDK)][jdk_link] バージョン 8 以降。

### <a name="include-the-package"></a>パッケージを組み込む

[//]: # ({x-version-update-start;com.azure:azure-containers-containerregistry;current})
```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-containers-containerregistry</artifactId>
  <version>1.0.0-beta.3</version>
</dependency>
```
[//]: # ({x-version-update-end})

## <a name="authenticate-the-client"></a>クライアントを認証する

[Azure ID ライブラリ][java_identity]では、認証のために Azure Active Directory のサポートが提供されています。

次のサンプルでは、`https://` プレフィックスとログイン サーバーの名前を含むレジストリ エンドポイント文字列 (例: "https://myregistry.azurecr.io") があることを前提としています。

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L31-L35 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();
```

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L39-L43 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryAsyncClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildAsyncClient();
```

Azure Container Registry での Azure AD の使用に関する詳細については、[認証の概要](container-registry-authentication.md)に関するページを参照してください。

## <a name="examples"></a>例

各サンプルでは、`https://` プレフィックスとログイン サーバーの名前を含むレジストリ エンドポイント文字列 (例: "https://myregistry.azurecr.io") があることを前提としています。

### <a name="list-repository-names"></a>リポジトリ名を一覧表示する

レジストリ内のリポジトリのコレクションを反復処理します。

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L47-L53 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();

client.listRepositoryNames().forEach(repository -> System.out.println(repository));
```

### <a name="set-artifact-properties"></a>成果物のプロパティを設定する

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L119-L132 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

RegistryArtifact image = client.getArtifact(repositoryName, digest);

image.updateTagProperties(
    tag,
    new ArtifactTagProperties()
        .setWriteEnabled(false)
        .setDeleteEnabled(false));
```

### <a name="delete-images"></a>イメージを削除する

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L85-L113 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

final int imagesCountToKeep = 3;
for (String repositoryName : client.listRepositoryNames()) {
    final ContainerRepository repository = client.getRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    PagedIterable<ArtifactManifestProperties> imageManifests =
        repository.listManifestProperties(
            ArtifactManifestOrderBy.LAST_UPDATED_ON_DESCENDING,
            Context.NONE);

    imageManifests.stream().skip(imagesCountToKeep)
        .forEach(imageManifest -> {
            System.out.printf(String.format("Deleting image with digest %s.%n", imageManifest.getDigest()));
            System.out.printf("    This image has the following tags: ");

            for (String tagName : imageManifest.getTags()) {
                System.out.printf("        %s:%s", imageManifest.getRepositoryName(), tagName);
            }

            repository.getArtifact(imageManifest.getDigest()).delete();
        });
}
```

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="get-started"></a>はじめに

[ソース コード][javascript_source] | [パッケージ (npm)][javascript_package] | [API リファレンス][javascript_docs] | [サンプル][javascript_samples]

### <a name="currently-supported-environments"></a>現在サポートされている環境

- [Node.js の LTS バージョン](https://nodejs.org/about/releases/)

詳細については、Microsoft の[サポート ポリシー](https://github.com/Azure/azure-sdk-for-js/blob/main/SUPPORT.md)を参照してください。

### <a name="install-the-azurecontainer-registry-package"></a>`@azure/container-registry` パッケージのインストール

`npm` を使用して JavaScript 用の Container Registry クライアント ライブラリをインストールします。

```bash
npm install @azure/container-registry
```

## <a name="authenticate-the-client"></a>クライアントを認証する

[Azure ID ライブラリ][javascript_identity]では、認証のために Azure Active Directory のサポートが提供されています。

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT;
// Create a ContainerRegistryClient that will authenticate through Active Directory
const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
```

Azure Container Registry での Azure AD の使用に関する詳細については、[認証の概要](container-registry-authentication.md)に関するページを参照してください。

## <a name="examples"></a>例

各サンプルでは、`https://` プレフィックスとログイン サーバーの名前を含む文字列に設定された `CONTAINER_REGISTRY_ENDPOINT` 環境変数 (例: "https://myregistry.azurecr.io") があることを前提としています。

### <a name="list-repositories-asynchronously"></a>リポジトリを非同期的に一覧表示する

レジストリ内のリポジトリのコレクションを反復処理します。

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  console.log("Listing repositories");
  const iterator = client.listRepositoryNames();
  for await (const repository of iterator) {
    console.log(`  repository: ${repository}`);
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="set-artifact-properties-asynchronously"></a>成果物のプロパティを非同期的に設定する

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";

  // Create a new ContainerRegistryClient and RegistryArtifact to access image operations
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
  const image = client.getArtifact("library/hello-world", "v1");

  // Set permissions on the image's "latest" tag
  await image.updateTagProperties("latest", { canWrite: false, canDelete: false });
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="delete-images-asynchronously"></a>イメージを非同期的に削除する

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  // Iterate through repositories
  const repositoryNames = client.listRepositoryNames();
  for await (const repositoryName of repositoryNames) {
    const repository = client.getRepository(repositoryName);
    // Obtain the images ordered from newest to oldest by passing the `orderBy` option
    const imageManifests = repository.listManifestProperties({
      orderBy: "LastUpdatedOnDescending"
    });
    const imagesToKeep = 3;
    let imageCount = 0;
    // Delete images older than the first three.
    for await (const manifest of imageManifests) {
      imageCount++;
      if (imageCount > imagesToKeep) {
        const image = repository.getArtifact(manifest.digest);
        console.log(`Deleting image with digest ${manifest.digest}`);
        console.log(`  Deleting the following tags from the image:`);
        for (const tagName of manifest.tags) {
          console.log(`    ${manifest.repositoryName}:${tagName}`);
          image.deleteTag(tagName);
        }
        await image.delete();
      }
    }
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

::: zone-end

::: zone pivot="programming-language-python"

## <a name="get-started"></a>はじめに

[ソース コード][python_source] | [パッケージ (Pypi)][python_package] | [API リファレンス][python_docs] | [サンプル][python_samples]

### <a name="install-the-package"></a>パッケージをインストールする

[pip][pip_link] を使用して Python 用の Azure Container Registry クライアント ライブラリをインストールします。

```bash
pip install --pre azure-containerregistry
```

## <a name="authenticate-the-client"></a>クライアントを認証する

[Azure ID ライブラリ][python_identity]では、認証のために Azure Active Directory のサポートが提供されています。 `DefaultAzureCredential` では、`AZURE_CLIENT_ID`、`AZURE_TENANT_ID` および `AZURE_CLIENT_SECRET` の環境変数が設定されていることを前提としています。 詳細については、[Azure ID の環境変数](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#environment-variables)に関するページを参照してください。

```python
# Create a ContainerRegistryClient that will authenticate through Active Directory
from azure.containerregistry import ContainerRegistryClient
from azure.identity import DefaultAzureCredential

account_url = "https://mycontainerregistry.azurecr.io"
client = ContainerRegistryClient(account_url, DefaultAzureCredential())
```

## <a name="examples"></a>例

各サンプルでは、`https://` プレフィックスとログイン サーバーの名前を含む文字列に設定された `CONTAINERREGISTRY_ENDPOINT` 環境変数 (例: "https://myregistry.azurecr.io") があることを前提としています。

### <a name="list-tags-asynchronously"></a>タグを非同期的に一覧表示する

このサンプルでは、レジストリにリポジトリ `hello-world` が含まれていることを前提としています。

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class ListTagsAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def list_tags(self):
        # Create a new ContainerRegistryClient      
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        manifest = await client.get_manifest_properties("library/hello-world", "latest")
        print(manifest.repository_name + ": ")
        for tag in manifest.tags:
            print(tag + "\n")
```

### <a name="set-artifact-properties-asynchronously"></a>成果物のプロパティを非同期的に設定する

このサンプルでは、`v1` のタグが付けられたイメージを持つリポジトリ `hello-world` がレジストリに含まれていることを前提としています。

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class SetImagePropertiesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def set_image_properties(self):
        # Create a new ContainerRegistryClient
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        audience = "https://management.azure.com"
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        # [START update_manifest_properties]
        # Set permissions on the v1 image's "latest" tag
        await client.update_manifest_properties(
            "library/hello-world",
            "latest",
            can_write=False,
            can_delete=False
        )
        # [END update_manifest_properties]
        # After this update, if someone were to push an update to "myacr.azurecr.io\hello-world:v1", it would fail.
        # It's worth noting that if this image also had another tag, such as "latest", and that tag did not have
        # permissions set to prevent reads or deletes, the image could still be overwritten. For example,
        # if someone were to push an update to "myacr.azurecr.io\hello-world:latest"
        # (which references the same image), it would succeed.
```

### <a name="delete-images-asynchronously"></a>イメージを非同期的に削除する

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry import ManifestOrder
from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class DeleteImagesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def delete_images(self):
        # [START list_repository_names]   
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        async with client:
            async for repository in client.list_repository_names():
                print(repository)
                # [END list_repository_names]

                # [START list_manifest_properties]
                # Keep the three most recent images, delete everything else
                manifest_count = 0
                async for manifest in client.list_manifest_properties(repository, order_by=ManifestOrder.LAST_UPDATE_TIME_DESCENDING):
                    manifest_count += 1
                    if manifest_count > 3:
                        await client.delete_manifest(repository, manifest.digest)
                # [END list_manifest_properties]
```

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure コンテナー レジストリをクリーンアップして削除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](container-registry-get-started-portal.md#clean-up-resources)
* [Azure CLI](container-registry-get-started-azure-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Container Registry クライアント ライブラリを使用して、コンテナー レジストリ内のイメージと成果物に対して操作を実行する方法について学習しました。

* 詳細については、次の API リファレンス ドキュメントを参照してください。

    * [.NET][dotnet_docs]
    * [Java][java_docs]
    * [JavaScript][javascript_docs]
    * [Python][python_docs]

* Azure Container Registry [REST API][rest_docs] について確認します。

[dotnet_source]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/src
[dotnet_package]: https://www.nuget.org/packages/Azure.Containers.ContainerRegistry/
[dotnet_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/samples
[dotnet_docs]: /dotnet/api/azure.containers.containerregistry
[rest_docs]: /rest/api/containerregistry/
[product_docs]:  container-registry-intro.md
[nuget]: https://www.nuget.org/
[dotnet_identity]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity/README.md
[javascript_identity]: https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md
[javascript_source]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/src
[javascript_package]: https://www.npmjs.com/package/@azure/container-registry
[javascript_docs]: /javascript/api/overview/azure/container-registry-readme
[jdk_link]: /java/azure/jdk/
[javascript_samples]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/samples
[java_source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/containerregistry/azure-containers-containerregistry/src
[java_package]: https://search.maven.org/artifact/com.azure/azure-containers-containerregistry
[java_docs]: /java/api/overview/azure/containers-containerregistry-readme
[java_identity]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/identity/azure-identity/README.md
[java_samples]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/containerregistry/azure-containers-containerregistry/src/samples/
[python_package]: https://pypi.org/project/azure-containerregistry/
[python_docs]: /python/api/overview/azure/containerregistry-readme
[python_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry/samples
[pip_link]: https://pypi.org
[python_identity]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity
[python_source]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry
