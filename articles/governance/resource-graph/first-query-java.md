---
title: 'クイックスタート: 初めての Java クエリ'
description: このクイックスタートでは、手順に従い、Java 用の Resource Graph Maven パッケージを有効にし、初めてのクエリを実行します。
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223859"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>クイックスタート: Java を使用して初めての Resource Graph クエリを実行する

初めて Azure Resource Graph を使用するには、まず必要な Java 用の Maven パッケージがインストールされていることを確認します。 このクイックスタートでは、Maven パッケージをお使いの Java のインストールに追加するプロセスについて説明します。

このプロセスの終了時には、Maven パッケージをお使いの Java のインストールに追加し、初めての Resource Graph クエリを実行していることになります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- 最新の Azure CLI がインストールされていることを確認します (**2.21.0** 以降)。 まだインストールされていない場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

  > [!NOTE]
  > 次の例で、Azure SDK for Java で **CLI ベースの認証** を使用できるようにするには、Azure CLI が必要です。 その他のオプションについては、「[Java 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)」を参照してください。

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン
  8.

- [Apache Maven](https://maven.apache.org/)、バージョン 3.6 以降。

## <a name="create-the-resource-graph-project"></a>Resource Graph プロジェクトを作成する

Java で Azure Resource Graph に対してクエリを実行できるようにするには、Maven を使用して新しいアプリケーションを作成および構成し、必要な Maven パッケージをインストールします。

1. [Maven アーキタイプ](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)を使用して、"argQuery" という名前の新しい Java アプリケーションを初期化します。

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. ディレクトリを新しいプロジェクト フォルダー `argQuery` に変更し、好みのエディターで `pom.xml` を開きます。 次の `<dependency>` ノードを既存の `<dependencies>` ノードの下に追加します。

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. `pom.xml` ファイルで、ベース `<project>` ノードの下に次の `<properties>` ノードを追加して、ソースとターゲットのバージョンを更新します。

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. `pom.xml` ファイルで、ベース `<project>` ノードの下に次の `<build>` ノードを追加して、実行するプロジェクトのゴールとメイン クラスを構成します。

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. `\argQuery\src\main\java\com\Fabrikam` 内の既定の `App.java` を次のコードに置き換え、更新したファイルを保存します。

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. `argQuery` コンソール アプリケーションをビルドします。

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

Java コンソール アプリケーションをビルドできたら、単純な Resource Graph クエリを試してみましょう。 このクエリでは、各リソースの **名前** と **リソースの種類** を使用して、最初の 5 つの Azure リソースが返されます。

`argQuery` に対するそれぞれの呼び出しには、自分独自の値で置き換える必要のある変数があります。

- `{subscriptionId}` - サブスクリプション ID で置き換えます
- `{query}` - Azure Resource Graph クエリで置き換えます

1. Azure CLI で `az login` を使用して認証します。

1. 前の `mvn -B archetype:generate` コマンドで作成した `argQuery` プロジェクト フォルダーにディレクトリを変更します。

1. Maven を使用して初めての Azure Resource Graph クエリを実行し、コンソール アプリケーションをコンパイルして、引数を渡します。 `exec.args` プロパティでは、引数が空白で識別されます。 クエリが単一の引数として識別されるようにするために、これを単一引用符 (`'`) で囲みます。

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > このクエリ例では、`order by` などの並べ替え修飾子を指定していません。そのため、このクエリを複数回実行すると、要求ごとに、得られる一連のリソースが異なる可能性があります。

1. `argQuery.exe` の引数を変更して、**Name** プロパティで並べ替える (`order by`) ようにクエリを変更します。

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを生成する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. 最後のパラメーターを `argQuery.exe` に変更し、まず **Name** プロパティで並べ替え (`order by`)、次に上位 5 件の結果に制限 (`limit`) するようにクエリを変更します。

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Java コンソール アプリケーションとインストールされているパッケージを削除する場合は、`argQuery` プロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、必要な Resource Graph パッケージを使用して Java コンソール アプリケーションを作成し、初めてのクエリを実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)