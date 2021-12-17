---
title: Azure Cosmos DB の Always Encrypted でクライアントサイド暗号化を行う
description: Azure Cosmos DB の Always Encrypted でクライアントサイド暗号化を行う方法を知る
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: thweiss
ms.openlocfilehash: 884626bd5b0cbbf6fb24bf0e3da3b7776e720f77
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071643"
---
# <a name="use-client-side-encryption-with-always-encrypted-for-azure-cosmos-db-preview"></a>Azure Cosmos DB の Always Encrypted でクライアントサイド暗号化を行う (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Always Encrypted は、Azure Cosmos DB に保存されている、クレジッド カード番号、国民の ID 番号 (たとえば、アメリカの社会保障番号) などの機密データを保護するための機能です。 Always Encrypted により、クライアント アプリケーション内部の機密データを暗号化でき、暗号化キーがデータベースに渡されることもありません。

Azure Cosmos DB では、Always Encrypted によってクライアントサイド暗号化を実行できます。 次の状況で、クライアントサイドのデータ暗号化が必要な場合があります。

- **特定の性質を有する機密データを保護している**: Always Encrypted では、クライアントアプリケーション内部の機密データを暗号化でき、プレーン テキスト データや暗号化キーが Azure Cosmos DB サービスに渡されることもありません。
- **プロパティごとにアクセスを制御している**: 暗号化の制御は、自分が所有し Azure Key Vault で管理しているキーで行うため、アクセス ポリシーを適用することで、各クライアントがどの機密プロパティにアクセスできるかを制御できます。

> [!IMPORTANT]
> Azure Cosmos DB の Always Encrypted は現在プレビューです。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

次の手順で Azure Cosmos DB の Always Encrypted の使用を開始できます。

- 2\.11.13.0 以上のバージョンの [Azure Cosmos DB ローカル エミュレーター](local-emulator.md) を使用します。
- [このフォーム](https://ncv.microsoft.com/poTcF52I6N)に必要事項を記入して、Azure Cosmos DB アカウントでプレビューを有効にする依頼を行います。

> [!TIP]
> Azure Cosmos DB の Always Encrypted のプレビューに関するフィードバックを行いたい場合 [azurecosmosdbcse@service.microsoft.com](mailto:azurecosmosdbcse@service.microsoft.com) にご連絡ください。

## <a name="concepts"></a>概念

Azure Cosmos DB の Always Encrypted により、クライアントサイド暗号化の構成に新しい要素が追加されました。

### <a name="encryption-keys"></a>暗号化キー

#### <a name="data-encryption-keys"></a>データ暗号化キー

Always Encrypted の使用時は、事前に作成が必要なデータ暗号化キー (DEK) によってデータを暗号化します。 これらの DEK は Azure Cosmos DB サービスに保存し、データベース単位で定義するため、DEK は複数のコンテナー間で共有できます。 DEK は Azure Cosmos DB SDK を使用してクライアントサイドで作成します。

次のことを実行できます。

- 暗号化するプロパティごとに 1 つの DEK を作成する、または
- 同じ DEK で複数のプロパティを暗号化する。

#### <a name="customer-managed-keys"></a>カスタマー マネージド キー

DEK は、Azure Cosmos DB に保存する際にカスタマーマネージド キー (CMK) でラップされます。 CMK では、DEK のラップとラップ解除を行うことにより、対応する DEK で暗号化したデータへのアクセスを効果的に制御します。 CMK の保存には拡張可能な、プラグイン型のモデルを採用しており、既定の実装では Azure Key Vault に保存します。

:::image type="content" source="./media/how-to-always-encrypted/encryption-keys.png" alt-text="暗号化キー" border="true":::

### <a name="encryption-policy"></a>暗号化ポリシー

[インデックス作成ポリシー](index-policy.md)と同様に、JSON のプロパティの暗号化方法を記述したコンテナー単位の仕様が暗号化ポリシーです。 このポリシーはコンテナー作成時に設定する必要があり、変更はできません。 現在のリリースでは、暗号化ポリシーは更新できません。

暗号化ポリシーでは、暗号化するプロパティごとに次のものを指定します。

- `/property` 形式のプロパティのパス。 現在、一番上の階層のパスだけをサポートしており、`/path/to/property` のような入れ子のパスはサポートしていません。
- プロパティの暗号化と解読に使用する [DEK](#data-encryption-keys) の ID。
- 暗号化の種類。 "ランダム" と "決定論的" のどちらかを選択します。
- プロパティの暗号化に使用する暗号化アルゴリズム。 指定したアルゴリズムは、互換性があれば、キー作成時に指定したアルゴリズムを上書きできます。

> [!NOTE]
> 次のプロパティは暗号化できません。
> - id
> - コンテナーのパーティション キー

#### <a name="randomized-vs-deterministic-encryption"></a>ランダム暗号化と決定論的暗号化の比較

Azure Cosmos DB サービスでは、Always Encrypted で暗号化したプロパティのプレーン テキストを読むことができません。 ただし、プロパティに使用する暗号化の種類によっては、暗号化されたデータに対するいくつかのクエリ機能をサポートしています。 Always Encrypted では次の 2 種類の暗号化をサポートしています。

- **決定論的暗号化:** 同一のプレーン テキスト値を同一の暗号化構成で暗号化した場合、常に同じ暗号化値を生成します。 決定論的暗号化では、クエリを使用して、暗号化されたプロパティに等価性のフィルターをかけられます。 ただし、暗号化されたプロパティのパターンを調べることで、暗号化された情報を攻撃者に推測されることがあり得ます。 暗号化される値の候補が少ない場合、たとえば True/False や North/South/East/West などの文脈では、これが特に問題になります。

- **ランダム暗号化:** 推定されにくい方法でデータを暗号化します。 ランダム暗号化は安全性が高いですが、クエリによって、暗号化されたプロパティにフィルターをかけることができません。

Always Encrypted の決定論的暗号化とランダム暗号化に関する詳細は、[初期化ベクター (IV) の生成](/sql/relational-databases/security/encryption/always-encrypted-cryptography#step-1-generating-the-initialization-vector-iv)に関するセクションをご覧ください。

## <a name="setup-azure-key-vault"></a>Azure Key Vault をセットアップする

Always Encrypted の使用を始めるには、まず Azure Key Vault に CMK を作成します。

1. Azure Key Vault の新しいインスタンスを作成するか、既存のインスタンスに移動します。
1. **[キー]** セクションで新しいキーを作成します。
1. キーを作成したら現在のバージョンに移動し、完全なキー識別子をコピーします。<br>`https://<my-key-vault>.vault.azure.net/keys/<key>/<version>`. キー識別子の末尾でキーのバージョンを指定しない場合、最新バージョンのキーが使用されます。

次に、Azure Cosmos DB SDK で Azure Key Vault のインスタンスにアクセスする方法を構成する必要があります。 この認証は Azure Active Directory (AD) ID で行います。 クライアント コードと Azure Key Vault インスタンスの間のプロキシには、どの種類の ID も使用できますが、Azure AD アプリケーションの ID か[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使う場合が多いでしょう。 次の手順で Azure AD アプリケーションをプロキシとして使用します。

1. [このクイックスタート](../active-directory/develop/quickstart-register-app.md)の説明に従って、新しいアプリケーションを作成し、クライアント シークレットを追加します。

1. Azure Key Vault のインスタンスに戻り、 **[アクセス ポリシー]** セクションに移動して、新しいポリシーを追加します。

   1. **[キーのアクセス許可]** で **[取得]** 、 **[一覧表示]** 、 **[キーの折り返しを解除]** 、 **[キーを折り返す]** 、 **[検証]** 、 **[署名]** を選択します。
   1. **[プリンシパルの選択]** で、作成済みの AAD アプリケーションを検索します。

### <a name="protect-your-cmk-from-accidental-deletion"></a>CMK を誤って削除することを防止する

CMK を誤って削除して暗号化されたデータにアクセスできなくなることがないよう、Azure Key Vault のインスタンスには **Soft Delete** (論理的な削除) と **Purge Protection** (消去保護) の 2 つのプロパティを設定することをお勧めします。

新しい Azure Key Vault インスタンスを作成する場合は、作成時にこれらのプロパティを有効にします。

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Azure Key Vault の新しいインスタンスで論理的な削除と消去保護を有効にする":::

既存の Azure Key Vault インスタンスを使用している場合は、Azure portal の **[プロパティ]** セクションを見て、これらのプロパティが有効であることを確認できます。 これらのプロパティのいずれかが有効になっていない場合は、次のいずれかの記事の「消去保護を有効にする」と「論理的な削除を有効にする」のセクションを参照してください。

- [PowerShell で論理的な削除を使用する方法](../key-vault/general/key-vault-recovery.md)
- [Azure CLI で論理的な削除を使用する方法](../key-vault/general/key-vault-recovery.md)

## <a name="initialize-the-sdk"></a>SDK を初期化する

> [!NOTE]
> Azure Cosmos DB の Always Encrypted は現在、次のものでサポートされています。
> - **.NET**。[Microsoft.Azure.Cosmos.Encryption パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Encryption)のインストールが必要です。
> - **Java**。[azure.cosmos.encryption パッケージ](https://mvnrepository.com/artifact/com.azure/azure-cosmos-encryption)のインストールが必要です。

Always Encrypted を使用するには、`EncryptionKeyStoreProvider` のインスタンスを Azure Cosmos DB SDK のインスタンスにアタッチする必要があります。 このオブジェクトを使用して、CMK を保存しているキー ストアを操作します。 Azure Key Vault の既定のキー ストア プロバイダーは `AzureKeyVaultKeyStoreProvider` という名前です。

次のスニペットで、クライアント シークレットが存在する Azure AD アプリケーションの ID を使用する方法を示します。 異なる種類の `TokenCredential` クラスを作成する例が用意されています。

- [.NET の場合](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java の場合](/java/api/overview/azure/identity-readme#credential-classes)

# <a name="net"></a>[.NET](#tab/dotnet)

> [!NOTE]
> .NET で `AzureKeyVaultKeyStoreProvider` クラスにアクセスするには、さらに [Microsoft.Data.Encryption.AzureKeyVaultProvider パッケージ](https://www.nuget.org/packages/Microsoft.Data.Encryption.AzureKeyVaultProvider)が必要です。

```csharp
var tokenCredential = new ClientSecretCredential(
    "<aad-app-tenant-id>", "<aad-app-client-id>", "<aad-app-secret>");
var keyStoreProvider = new AzureKeyVaultKeyStoreProvider(tokenCredential);
var client = new CosmosClient("<connection-string>")
    .WithEncryption(keyStoreProvider);
```

# <a name="java"></a>[Java](#tab/java)

```java
TokenCredential tokenCredential = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<aad-app-tenant-id>")
    .clientId("<aad-app-client-id>")
    .clientSecret("<aad-app-secret>")
    .build();
AzureKeyVaultKeyStoreProvider encryptionKeyStoreProvider =
    new AzureKeyVaultKeyStoreProvider(tokenCredential);
CosmosAsyncClient client = new CosmosClientBuilder()
    .endpoint("<endpoint>")
    .key("<primary-key>")
    .buildAsyncClient();
EncryptionAsyncCosmosClient encryptionClient =
    EncryptionAsyncCosmosClient.buildEncryptionAsyncClient(client, encryptionKeyStoreProvider);
```
---

## <a name="create-a-data-encryption-key"></a>データ暗号化キーを作成する

コンテナーでデータを暗号化するには、親データベースで[データ暗号化キー](#data-encryption-keys)を事前に作成しておく必要があります。 この操作は `CreateClientEncryptionKeyAsync` メソッドを呼び出して次の値を渡すことにより実行します。

- データベースにあるキーを一意に特定する文字列識別子。
- キーと共に使用する暗号化アルゴリズム。 現在サポートしているアルゴリズムは 1 つだけです。
- Azure Key Vault に保存している [CMK](#customer-managed-keys) のキー識別子。 このパラメーターは通常のオブジェクト `EncryptionKeyWrapMetadata` に渡します。ここで、`name` には好みの名前を指定でき、`value` にはキー識別子を指定する必要があります。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var database = client.GetDatabase("my-database");
await database.CreateClientEncryptionKeyAsync(
    "my-key",
    DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256,
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionCosmosAsyncDatabase database =
    client.getEncryptedCosmosAsyncDatabase("my-database");
database.createClientEncryptionKey(
    "my-key",
    CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256,
    new EncryptionKeyWrapMetadata(
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```
---

## <a name="create-a-container-with-encryption-policy"></a>暗号化ポリシーを使用してコンテナーを作成する

コンテナー作成時に、コンテナー単位の暗号化ポリシーを指定します。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var path1 = new ClientEncryptionIncludedPath
{
    Path = "/property1",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Deterministic.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
var path2 = new ClientEncryptionIncludedPath
{
    Path = "/property2",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Randomized.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
await database.DefineContainer("my-container", "/partition-key")
    .WithClientEncryptionPolicy()
    .WithIncludedPath(path1)
    .WithIncludedPath(path2)
    .Attach()
    .CreateAsync();
```

# <a name="java"></a>[Java](#tab/java)

```java
ClientEncryptionIncludedPath path1 = new ClientEncryptionIncludedPath();
path1.clientEncryptionKeyId = "my-key":
path1.path = "/property1";
path1.encryptionType = CosmosEncryptionType.DETERMINISTIC;
path1.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

ClientEncryptionIncludedPath path2 = new ClientEncryptionIncludedPath();
path2.clientEncryptionKeyId = "my-key":
path2.path = "/property2";
path2.encryptionType = CosmosEncryptionType.RANDOMIZED;
path2.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

List<ClientEncryptionIncludedPath> paths = new ArrayList<>();
paths.add(path1);
paths.add(path2);

CosmosContainerProperties containerProperties =
    new CosmosContainerProperties("my-container", "/id");
containerProperties.setClientEncryptionPolicy(new ClientEncryptionPolicy(paths));
database.createEncryptionContainerAsync(containerProperties);
```
---

## <a name="read-and-write-encrypted-data"></a>暗号化されたデータを読み書きする

### <a name="how-data-gets-encrypted"></a>データ暗号化の方法

ドキュメントを Azure Cosmos DB に書き込むときは毎回、暗号化ポリシーでどのプロパティをどのように暗号化するよう指定されているか、SDK による確認が行われます。 暗号化により生成されるのは Base64 文字列です。

**複雑なデータの暗号化**:

- 暗号化するプロパティが JSON 配列であるときは、配列のすべての項目が暗号化されます。

- 暗号化するプロパティが JSON オブジェクトであるときは、オブジェクトのリーフの値だけが暗号化されます。 中間階層のサブプロパティの名前はプレーン テキスト形式のままになります。

### <a name="read-encrypted-items"></a>暗号化された項目を読む

ポイント読み取り (ID とパーティション キーによって単一の項目を取得する操作) 要求を行うとき、および変更フィードを読み取るときは、暗号化されたプロパティの解読を行うのに明示的な操作は必要ありません。 その理由:

- 暗号化ポリシーでどのプロパティを解読するかが指定されているか、SDK による確認が行われるからです。
- 暗号化により生成されたデータには、元の値の JSON の種類の情報が埋め込まれます。

暗号化されたプロパティの暗号化とそれに続く解読は、要求からの応答にのみ基づいて行います。 たとえば、`property1` を暗号化して `property2` (`SELECT property1 AS property2 FROM c`) に投影した場合、SDK でこれを受け取ったときに、暗号化されたプロパティとして認識されません。

### <a name="filter-queries-on-encrypted-properties"></a>暗号化されたプロパティにフィルターをかけるクエリ

暗号化されたプロパティにフィルターをかけるクエリを書くときは、`AddParameterAsync` メソッドによってクエリのパラメーターを渡す必要があります。 このメソッドは次の引数を取ります。

- クエリ パラメーターの名前。
- クエリで使用する値。
- 暗号化されたプロパティのパス (暗号化ポリシーで指定)。

> [!IMPORTANT]
> 暗号化されたプロパティは等価性のフィルター (`WHERE c.property = @Value`) でのみ使用できます。 それ以外の方法で使用すると、予測不能で誤ったクエリの結果が返されます。 今後公開されるバージョンの SDK で、この制約を適切に処理できるようにする予定です。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var queryDefinition = container.CreateQueryDefinition(
    "SELECT * FROM c where c.property1 = @Property1");
await queryDefinition.AddParameterAsync(
    "@Property1",
    1234,
    "/property1");
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionSqlQuerySpec encryptionSqlQuerySpec = new EncryptionSqlQuerySpec(
    new SqlQuerySpec("SELECT * FROM c where c.property1 = @Property1"), container);
encryptionSqlQuerySpec.addEncryptionParameterAsync(
    new SqlParameter("@Property1", 1234), "/property1")
```
---

### <a name="reading-documents-when-only-a-subset-of-properties-can-be-decrypted"></a>一部のプロパティだけが解読できる状態でドキュメントを読む

プロパティの暗号化に使用するすべての CMK に対するアクセス権がクライアントにない場合、データを読み取ると、一部のプロパティだけを解読できます。 たとえば、`property1` をキー 1 で、`property2` をキー 2 で暗号化した場合、キー 1 に対するアクセス権だけを持つクライアントでは、データの読み取りはできるものの `property2` は読めません。 このような場合、SQL クエリでデータを読み取って、クライアントで解読できないプロパティは除く必要があります: `SELECT c.property1, c.property3 FROM c`。

## <a name="cmk-rotation"></a>CMK のローテーション

使用中の CMK が漏洩した疑いがある場合、CMK を "ローテーション" する (つまり、使用中の CMK に代わって新しい CMK を使う) ことができます。 定期的な CMK のローテーションは、よく行われるセキュリティ対策でもあります。 特定の DEK をラップするのに使用する新しい CMK のキー識別子を指定するだけで、このローテーションを実行できます。 この操作はデータの暗号化方法には影響しませんが、DEK の保護に効果があります。 前の CMK へのアクセス権は、ロテーションが完了するまでは残しておくべきです。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
await database.RewrapClientEncryptionKeyAsync(
    "my-key",
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
database. rewrapClientEncryptionKey(
    "my-key",
    new EncryptionKeyWrapMetadata(
        "akvKey", " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```
---

## <a name="next-steps"></a>次のステップ

- [Cosmos DB 内のデータへのセキュリティで保護されたアクセス](secure-access-to-data.md)の概要を理解します。
- [カスタマー マネージド キー](how-to-setup-cmk.md)について詳しく知る。