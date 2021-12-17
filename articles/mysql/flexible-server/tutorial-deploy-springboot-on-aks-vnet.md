---
title: 'チュートリアル: VNet 内の MySQL フレキシブル サーバーを使用してSpring Boot アプリケーションを AKS クラスターにデプロイする'
description: Azure Database for MySQL - フレキシブル サーバー、VNet 内のセキュリティで保護された接続を使用して、Spring Boot アプリケーションを AKS に迅速にビルドしてデプロイする方法について説明します。
ms.service: mysql
author: shreyaaithal
ms.author: shaithal
ms.topic: tutorial
ms.date: 11/11/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8b07ffd9ea4d9cbcba5cf36bcac048f1abccee37
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557130"
---
# <a name="tutorial-deploy-a-spring-boot-application-on-aks-cluster-with-mysql-flexible-server-in-a-vnet"></a>チュートリアル: VNet 内の MySQL フレキシブル サーバーを使用してSpring Boot アプリケーションを AKS クラスターにデプロイする

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

このチュートリアルでは、[Azure 仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)内で互いに安全に通信している [Azure Database for MySQL - フレキシブル サーバー](overview.md)をバックエンドで使用して、[Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)クラスターに [Spring Boot](https://spring.io/projects/spring-boot) アプリケーションをデプロイする方法について説明します。 

> [!NOTE]
> このチュートリアルでは、Kubernetes の概念、Java Spring Boot、MySQL の基本的な理解を前提としています。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- [Azure コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli)。
- サポートされている [Java 開発キット](/azure/developer/java/fundamentals/java-support-on-azure)、バージョン 8 (Azure Cloud Shell に含まれます)。
- [Apache Maven](https://maven.apache.org/) ビルド ツール。
- [Git](https://github.com/) クライアント。
- [Docker](https://www.docker.com/) クライアント。

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーを作成する 

### <a name="create-a-resource-group"></a>リソース グループを作成する
Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 [az group create](/cli/azure/group#az_group_create) コマンドを使用して、*eastus* の場所に *rg-mysqlaksdemo* というリソース グループを作成しましょう。

1. コマンド プロンプトを開きます。 
1. Azure アカウントにサインインします。
    ```azurecli-interactive
    az login
    ```
1. Azure サブスクリプションを選択します。
    ```azurecli-interactive
    az account set -s <your-subscription-ID>
    ```    
1. リソース グループを作成します。
    ```azurecli-interactive
    az group create --name rg-mysqlaksdemo --location eastus
    ```

### <a name="create-a-mysql-flexible-server"></a>MySQL フレキシブル サーバーの作成

次に、仮想ネットワーク内にフレキシブル サーバーを作成します (プライベート アクセス接続方法)。

1. このチュートリアルのすべてのリソースに対して Azure 仮想ネットワーク *vnet-mysqlaksdemo* を作成し、MySQL フレキシブル サーバー用にサブネット *subnet-mysql* を作成します。

    ```azurecli-interactive
    az network vnet create \
    --resource-group rg-mysqlaksdemo \
    --name vnet-mysqlaksdemo \
    --address-prefixes 155.55.0.0/16 \
    --subnet-name subnet-mysql \
    --subnet-prefix 155.55.1.0/24 
    ```

1. [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)コマンドを使用して、上記で作成したサブネットに Azure Database for MySQL - フレキシブル サーバー *mysql-mysqlaksdemo* を作成します。 管理者のユーザー名とパスワードの値を置き換えます。

    ```azurecli-interactive
    az mysql flexible-server create \
    --name mysql-mysqlaksdemo \
    --resource-group rg-mysqlaksdemo \
    --location eastus \
    --admin-user <your-admin-username> \
    --admin-password <your-admin-password> \
    --vnet vnet-mysqlaksdemo \
    --subnet subnet-mysql
    ```
    
    これで、バースト可能な B1MS コンピューティング、32 GB ストレージ、7 日間のバックアップ保有期間、指定されたサブネット *subnet-mysql* のフレキシブル サーバーを eastus リージョンに作成しました。 このサブネットには他のリソースがデプロイされていない必要があります。これは、Microsoft.DBforMySQL/flexibleServers に委任されます。

1. Spring Boot アプリケーションで使用する新しい MySQL データベース ```demo``` を構成します。

    ```azurecli-interactive
    az mysql flexible-server db create \
    --resource-group rg-mysqlaksdemo \
    --server-name mysql-mysqlaksdemo \
    --database-name demo
    ```

## <a name="create-an-azure-container-registry"></a>Azure コンテナー レジストリを作成する 

リソース グループ内に、プライベートな Azure コンテナー レジストリを作成します。 このチュートリアルでは、後の手順で、このレジストリに Docker イメージとしてサンプル アプリをプッシュします。 ```mysqlaksdemoregistry``` を、レジストリの一意の名前に置き換えます。

```azurecli-interactive
az acr create --resource-group rg-mysqlaksdemo \
--location eastus \
--name mysqlaksdemoregistry \
--sku Basic
```

## <a name="code-the-application"></a>アプリケーションをコーディングする

このセクションでは、デモ アプリケーションをコーディングします。 速く進める場合は、[https://github.com/Azure-Samples/tutorial-springboot-mysql-aks](https://github.com/Azure-Samples/tutorial-springboot-mysql-aks) で利用可能なコーディングされたアプリケーションをダウンロードし、次のセクション「[イメージをビルドして ACR にプッシュする](#build-the-image-and-push-to-acr)」に進む必要があります。 

1. Spring Initializr を使用してアプリケーションを生成します。 

    ```bash
    curl https://start.spring.io/starter.tgz \
    -d dependencies=web,data-jdbc,mysql \
    -d baseDir=springboot-mysql-aks \
    -d bootVersion=2.5.6.RELEASE \
    -d artifactId=springboot-mysql-aks \
    -d description="Spring Boot on AKS connecting to Azure DB for MySQL" \
    -d javaVersion=1.8 | tar -xzvf -
    ```
    
    基本 Spring Boot アプリケーションは ```springboot-mysql-aks``` フォルダー内に生成されます。
    
    次の手順では [VSCode](https://code.visualstudio.com/docs) や任意の IDE など、お好みのテキスト エディターを使用します。

1. Azure Database for MySQL - フレキシブル サーバーを使用するように Spring Boot を構成します。

    src/main/resources/application.properties ファイルを開き、以下のスニペットを追加します。 このコードは、Kubernetes マニフェスト ファイルからデータベース ホスト、データベース名、ユーザー名、パスワードを読み取ります。
    
    ```properties
    logging.level.org.springframework.jdbc.core=DEBUG
    spring.datasource.url=jdbc:mysql://${DATABASE_HOST}:3306/${DATABASE_NAME}?serverTimezone=UTC
    spring.datasource.username=${DATABASE_USERNAME}
    spring.datasource.password=${DATABASE_PASSWORD}
    spring.datasource.initialization-mode=always
    ```
    >[!Warning]
    > 構成プロパティ ```spring.datasource.initialization-mode=always``` は、サーバーが起動されるたびに、```schema.sql``` ファイル (後ほど作成します) を使用して、Spring Boot がデータベース スキーマを自動的に生成することを意味します。 これはテストには適していますが、再起動するたびにデータが削除されるため、運用環境では使用しないでください。

    >[!Note]
    >構成プロパティ ```spring.datasource.url``` に ```?serverTimezone=UTC``` を追加して、データベースへの接続時に UTC 日付形式 (協定世界時) を使用するように JDBC ドライバーに指示します。 そうしないと、Java サーバーではデータベースと同じ日付形式が使用されず、エラーが発生します。

1. データベース スキーマを作成します。
    
    Spring Boot は、データベース スキーマを作成するために ```src/main/resources/schema.sql``` を自動的に実行します。 このファイルを次の内容で作成します。

    ```sql
    DROP TABLE IF EXISTS todo;
    CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
    ```
1. Java Spring Boot アプリケーションをコーディングします。
    
    JDBC を使用して MySQL サーバーにデータを格納および取得する Java コードを追加します。 ```DemoApplication``` クラスの横に新しい ```Todo``` Java クラスを作成し、以下のコードを追加します。

    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.annotation.Id;
    
    public class Todo {
    
        public Todo() {
        }
    
        public Todo(String description, String details, boolean done) {
            this.description = description;
            this.details = details;
            this.done = done;
        }
    
        @Id
        private Long id;
    
        private String description;
    
        private String details;
    
        private boolean done;
    
        public Long getId() {
            return id;
        }
    
        public void setId(Long id) {
            this.id = id;
        }
    
        public String getDescription() {
            return description;
        }
    
        public void setDescription(String description) {
            this.description = description;
        }
    
        public String getDetails() {
            return details;
        }
    
        public void setDetails(String details) {
            this.details = details;
        }
    
        public boolean isDone() {
            return done;
        }
    
        public void setDone(boolean done) {
            this.done = done;
        }
    }
    ```
    このクラスは、先ほど作成した ```todo``` テーブルにマップされるドメイン モデルです。

    このクラスを管理するには、リポジトリが必要です。 同じパッケージ内に新しい ```TodoRepository``` インターフェイスを定義します。
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.repository.CrudRepository;
    
    public interface TodoRepository extends CrudRepository<Todo, Long> {
    }
    ```

    このリポジトリは、Spring Data JDBC によって管理されるリポジトリです。

    データを格納および取得できるコントローラーを作成して、アプリケーションを完成させます。 同じパッケージに ```TodoController``` クラスを実装し、次のコードを追加します。
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.http.HttpStatus;
    import org.springframework.web.bind.annotation.*;
    
    @RestController
    @RequestMapping("/")
    public class TodoController {
    
        private final TodoRepository todoRepository;
    
        public TodoController(TodoRepository todoRepository) {
            this.todoRepository = todoRepository;
        }
    
        @PostMapping("/")
        @ResponseStatus(HttpStatus.CREATED)
        public Todo createTodo(@RequestBody Todo todo) {
            return todoRepository.save(todo);
        }
    
        @GetMapping("/")
        public Iterable<Todo> getTodos() {
            return todoRepository.findAll();
        }
    }
    ```

1. 基本ディレクトリ *springboot-mysql-aks* に新しい Dockerfile を作成し、このコード スニペットをコピーします。

    ```dockerfile
    FROM openjdk:8-jdk-alpine
    RUN addgroup -S spring && adduser -S spring -G spring
    USER spring:spring
    ARG DEPENDENCY=target/dependency
    COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
    COPY ${DEPENDENCY}/META-INF /app/META-INF
    COPY ${DEPENDENCY}/BOOT-INF/classes /app
    ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.springbootmysqlaks.DemoApplication"]
    ```

1. *pom.xml* ファイルに移動し、pom.xml ファイル内の ```<properties>``` コレクションを、Azure Container Registry のレジストリ名と ```jib-maven-plugin``` の最新バージョンで更新します。
    注: ACR 名に大文字が含まれている場合は、必ず小文字に変換してください。

    ```xml
    <properties>
        <docker.image.prefix>mysqlaksdemoregistry.azurecr.io</docker.image.prefix>
        <jib-maven-plugin.version>3.1.4</jib-maven-plugin.version>
        <java.version>1.8</java.version>
    </properties>
    ```

1. 以下の例に示すように *pom.xml* ファイルの ```<plugins>``` コレクションを更新し、```jib-maven-plugin``` のエントリが含まれる ```<plugin>``` 要素が存在するようにします。 ここでは、Microsoft Container Registry (MCR) の基本イメージ (```mcr.microsoft.com/java/jdk:8-zulu-alpine```) が使用されていることに注目してください。このイメージには、公式にサポートされた Azure 用の JDK が含まれています。 その他公式にサポートされた JDK を含む MCR 基本イメージについては、「[Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)」、「[Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)」、「[Java SE ヘッドレス JRE](https://hub.docker.com/_/microsoft-java-jre-headless)」、「[Java SE JDK と Maven](https://hub.docker.com/_/microsoft-java-maven)」を参照してください。
    
    ```xml
    <plugin>
        <artifactId>jib-maven-plugin</artifactId>
        <groupId>com.google.cloud.tools</groupId>
        <version>${jib-maven-plugin.version}</version>
        <configuration>
            <from>
                <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
            </from>
            <to>
                <image>${docker.image.prefix}/${project.artifactId}</image>
            </to>
        </configuration>
    </plugin>
    ```

## <a name="build-the-image-and-push-to-acr"></a>イメージをビルドして ACR にプッシュします。

コマンド プロンプトで *springboot-mysql-aks* フォルダーに移動し、次のコマンドを実行して、最初に Azure Container Registry の既定の名前を設定し (それ以外の場合は、```az acr login``` で名前を指定する必要があります)、イメージをビルドしてから、イメージをレジストリにプッシュします。

この手順の実行中に、Docker デーモンが実行されていることを確認します。

```azurecli-interactive
az config set defaults.acr=mysqlaksdemoregistry
az acr login && mvn compile jib:build
```

## <a name="create-a-kubernetes-cluster-on-aks"></a>AKS で Kubernetes クラスターを作成する

次に、仮想ネットワーク *vnet-mysqlaksdemo* に AKS クラスターを作成します。 

このチュートリアルでは、AKS でAzure CNI ネットワークを使用します。 代わりに kubenet ネットワークを構成する場合は、[AKS で kubenetネットワークを使用する](../../aks/configure-kubenet.md#create-a-service-principal-and-assign-permissions)に関するページを参照してください。  

1. AKS クラスターが使用するサブネット *subnet-aks* を作成します。

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group rg-mysqlaksdemo \
    --vnet-name vnet-mysqlaksdemo \
    --name subnet-aks \
    --address-prefixes 155.55.2.0/24
    ```

1. サブネット リソース ID を取得します。

    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet show --resource-group rg-mysqlaksdemo --vnet-name vnet-mysqlaksdemo --name subnet-aks --query id -o tsv)
    ```
    
1. 仮想ネットワークに AKS クラスターを作成し、Azure Container Registry (ACR) *mysqlaksdemoregistry* を接続します。

    ```azurecli-interactive
        az aks create \
        --resource-group rg-mysqlaksdemo \
        --name aks-mysqlaksdemo \
        --network-plugin azure \
        --service-cidr 10.0.0.0/16 \
        --dns-service-ip 10.0.0.10 \
        --docker-bridge-address 172.17.0.1/16 \
        --vnet-subnet-id $SUBNET_ID \
        --attach-acr mysqlaksdemoregistry \
        --dns-name-prefix aks-mysqlaksdemo \
        --generate-ssh-keys
    ```

    クラスター作成プロセスの一部として、次の IP アドレス範囲も定義します。
    
    - *--service-cidr* は、AKS クラスター内の内部サービスに IP アドレスを割り当てるために使用します。 次の要件を満たす任意のプライベート アドレス範囲を使用できます。
        - クラスターの仮想ネットワークの IP アドレス範囲に含まれていてはなりません
        - クラスターの仮想ネットワークがピアリングする他の仮想ネットワークと重複していてはなりません
        - オンプレミスのどの IP アドレスとも重複していてはなりません
        - 169.254.0.0/16、172.30.0.0/16、 172.31.0.0/16、192.0.2.0/24 の範囲内にあってはなりません
    
    - *--dns-service-ip* アドレスはクラスターの DNS サービスの IP アドレスです。 *[Kubernetes service address range]\(Kubernetes サービス アドレスの範囲\)* 内に含まれるアドレスを指定する必要があります。 アドレス範囲内の最初の IP アドレスは使用しないでください。 サブネット範囲の最初のアドレスは、*kubernetes.default.svc.cluster.local* アドレスに使用されます。
    
    - *--docker-bridge-address* は、すべての Docker インストールに存在する既定の *docker0* ブリッジのネットワーク アドレスを表す Docker ブリッジのネットワーク アドレスです。 ネットワーク上の残りの CIDR と競合しないアドレス空間を選択する必要があります。これには、クラスターのサービス CIDR とポッド CIDR が含まれます。

## <a name="deploy-the-application-to-aks-cluster"></a>AKS クラスターにアプリケーションをデプロイする

1. Azure portal で AKS クラスター リソースに移動します。

1. いずれかのリソース ビュー ([名前空間]、[ワークロード]、[サービスとイングレス]、[ストレージ]、または [構成]) から **[追加]** および **[YAML で追加]** を選択します。 

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-resource-blade.png" alt-text="Azure portal 上の Azure Kubernetes Service リソース ビューを示すスクリーンショット。":::

1. 次の YAML を貼り付けます。 MySQL フレキシブル サーバーの管理者のユーザー名とパスワードの値を置き換えます。

    ```yml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: springboot-mysql-aks
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: springboot-mysql-aks
      template:
        metadata:
          labels:
            app: springboot-mysql-aks
        spec:
          containers:
          - name: springboot-mysql-aks
            image: mysqlaksdemoregistry.azurecr.io/springboot-mysql-aks:latest
            env:
            - name: DATABASE_HOST
              value: "mysql-mysqlaksdemo.mysql.database.azure.com"
            - name: DATABASE_USERNAME
              value: "<your-admin-username>"
            - name: DATABASE_PASSWORD
              value: "<your-admin-password>"
            - name: DATABASE_NAME    
              value: "demo"     
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: springboot-mysql-aks
    spec:
      type: LoadBalancer
      ports:
      - port: 80
        targetPort: 8080
      selector:
        app: springboot-mysql-aks
    ```
1. YAML エディターの下部にある **[追加]** を選択してアプリケーションをデプロイします。

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-add-with-yaml.png" alt-text="[YAML で追加] エディターを示すスクリーンショット。":::

1. YAML ファイルが追加されると、リソース ビューアーに Spring Boot アプリケーションが表示されます。 外部サービスに含まれているリンクされた外部 IP アドレスをメモします。

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-external-ip.png" alt-text="Azure Kubernetes クラスター サービス 外部 IP の Azure portal でのビューを示すスクリーンショット。":::

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションをテストするには、cURL を使用します。

まず、次のコマンドを使用して、データベースに新しい "todo" 項目を作成します。

```bash
curl --header "Content-Type: application/json" \
--request POST \
--data '{"description":"configuration","details":"congratulations, you have deployed your application correctly!","done": "true"}' \
http://<AKS-service-external-ip>
```

次に、新しい cURL 要求を使用するか、ブラウザーにクラスターの *外部 IP* を入力して、データを取得します。

```bash
curl http://<AKS-service-external-ip>
```

このコマンドを実行すると、作成した項目を含む "todo" 項目の一覧が返されます。

```json
[{"id":1,"description":"configuration","details":"congratulations, you have deployed your application correctly!","done":true}]
```

この cURL 要求のスクリーンショットを次に示します。:::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-curl-output.png" alt-text="cURL 要求のコマンド ライン出力を示すスクリーンショット":::

ブラウザーから同様の出力が表示されます。 :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-browser-output.png" alt-text="ブラウザー要求の出力を示すスクリーンショット。":::


お疲れさまでした。 バックエンドで Azure Database for MySQL - フレキシブル サーバーを使用して、Spring Boot アプリケーションを Azure Kubernetes Service (AKS) クラスターに正常にデプロイしました。 


## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

Azure の課金を回避するには、不要なリソースをクリーンアップする必要があります。  クラスターが必要なくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使って、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

```azurecli-interactive
az group delete --name rg-mysqlaksdemo
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除](../../aks/kubernetes-service-principal.md#additional-considerations)に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [MySQL を使用して AKS に WordPress アプリをデプロイする](tutorial-deploy-wordpress-on-aks.md)

> [!div class="nextstepaction"]
> [MySQL を使用した PHP (Laravel) Web アプリを作成する](tutorial-php-database-app.md)


