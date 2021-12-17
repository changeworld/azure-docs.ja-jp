---
title: Azure Spring Cloud で Config Server インスタンスを設定する
description: Azure portal で自分の Azure Spring Cloud 向けに Spring Cloud Config Server インスタンスを設定する方法を説明します
ms.service: spring-cloud
ms.topic: how-to
ms.author: karler
author: karlerickson
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 00411cf37a6f2728d3f8d1d0565e1d836b839192
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135398"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>自分のサービス向けに Spring Cloud Config Server インスタンスを設定する

**この記事の適用対象:** ✔️ Java ✔️ C#

この記事では、Spring Cloud Config Server インスタンスを自分の Azure Spring Cloud サービスに接続する方法を紹介します。

Spring Cloud Config は、分散システムで外部化された構成用に、サーバー側とクライアント側のサポートを提供します。 Config Server インスタンスを使用すると、すべての環境にわたってアプリケーションの外部プロパティを一元的に管理できます。 詳細については、[Spring Cloud Config Server のリファレンス](https://spring.io/projects/spring-cloud-config)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* 既にプロビジョニングされ、実行されている Azure Spring Cloud サービス。 Azure Spring Cloud サービスを設定し、起動するには、「[クイックスタート: Azure CLI を使用して Java Spring アプリケーションを起動する](./quickstart.md)」を参照してください。

## <a name="restriction"></a>Restriction

Git バックエンドで Config Server を使用する場合、いくつかの制限があります。 Config Server と Service Discovery にアクセスするため、一部のプロパティがお使いのアプリケーション環境に自動的に挿入されます。 これらのプロパティを Config Server ファイルからも構成すると、競合と予期しない動作が生じる場合があります。 プロパティには、次のようなものがあります。

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> 上のプロパティをご自分の Config Server アプリケーション ファイルには設定 "_しない_" ことを強くお勧めします。

## <a name="create-your-config-server-files"></a>Config Server ファイルを作成する

Azure Spring Cloud では、Config Server ファイルを格納するために、Azure DevOps、GitHub、GitLab、および Bitbucket がサポートされています。 自分のリポジトリの準備ができたら、以下の手順で構成ファイルを作成し、そこに格納します。

また、構成可能なプロパティの中には、特定の種類でしか使用できないものがあります。 以降のサブセクションでは、リポジトリの種類ごとにプロパティの一覧を示します。

### <a name="public-repository"></a>パブリック リポジトリ

パブリック リポジトリを使用する場合、構成可能なプロパティはより制限されます。

パブリック Git リポジトリを設定するために使用されるすべての構成可能なプロパティを、以下の表に示します。

> [!NOTE]
> 現在サポートされている名前付け規則は、ハイフン (-) を使用して単語を区切ることだけです。 たとえば、*default-label* は使用できますが、*defaultLabel* は使用できません。

| プロパティ        | 必須 | 特徴量                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | はい    | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まります。 |
| `default-label` | いいえ     | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `search-paths`  | いいえ     | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |

------

### <a name="private-repository-with-ssh-authentication"></a>SSH 認証を使用したプライベート リポジトリ

SSH を使用するプライベート Git リポジトリを設定するために使用されるすべての構成可能なプロパティを以下の表に示します。

> [!NOTE]
> 現在サポートされている名前付け規則は、ハイフン (-) を使用して単語を区切ることだけです。 たとえば、*default-label* は使用できますが、*defaultLabel* は使用できません。

| プロパティ                   | 必須 | 特徴量                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | はい    | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まる必要があります。 |
| `default-label`            | いいえ     | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `search-paths`             | いいえ     | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |
| `private-key`              | いいえ     | Git リポジトリにアクセスするための SSH 秘密キー。URI が *git@* または *ssh://* で始まる場合は "_必須_"。 |
| `host-key`                 | いいえ     | Git リポジトリ サーバーのホスト キーには、`host-key-algorithm` で対応されているアルゴリズム プレフィックスを含めないでください。 |
| `host-key-algorithm`       | いいえ     | ホスト キー アルゴリズム。*ssh-dss*、*ssh-rsa*、*ecdsa-sha2-nistp256*、*ecdsa-sha2-nistp384*、*ecdsa-sha2-nistp521* のいずれかを指定する必要があります。 `host-key` が存在する場合にのみ "*必須*"。 |
| `strict-host-key-checking` | いいえ     | プライベートな `host-key` が指定されたら、Config Server インスタンスが起動に失敗するかどうかを示します。 *true* (既定値) または *false* にする必要があります。 |

> [!NOTE]
> 指定されていない場合、Config Server は既定のラベルとして `master` (om Git 自体) を受け取ります。 しかし、GitHub では、最近既定のブランチが `master` から `main` に変更されました。 Azure Spring Cloud Config Server の障害を回避するには、Config Server を GitHub で設定するときに既定のラベルに注意してください (特に、新しく作成されたリポジトリの場合)。

-----

### <a name="private-repository-with-basic-authentication"></a>基本認証を使用したプライベート リポジトリ

基本認証を使用するプライベート Git リポジトリを設定するために使用されるすべての構成可能なプロパティを、以下の一覧に示します。

> [!NOTE]
> 現在サポートされている名前付け規則は、ハイフン (-) を使用して単語を区切ることだけです。 たとえば、*defaultLabel* とはせずに、*default-label* としてください。

| プロパティ        | 必須 | 特徴量                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | はい    | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まる必要があります。 |
| `default-label` | いいえ     | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `search-paths`  | いいえ     | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |
| `username`      | いいえ     | Git リポジトリ サーバーへのアクセスに使用されるユーザー名。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |
| `password`      | いいえ     | Git リポジトリ サーバーへのアクセスに使用されるパスワードまたは個人用アクセス トークン。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |

> [!NOTE]
> 多くの `Git` リポジトリ サーバーで、HTTP 基本認証のパスワードではなく、トークンの使用がサポートされています。 一部のリポジトリでは、トークンを無期限に保持できます。 しかし、一部の Git リポジトリ サーバー (Azure DevOps Server を含む) では、強制的にトークンの有効期限を数時間に設定しています。 トークンの有効期限が切れるリポジトリでは、Azure Spring Cloud でトークンベースの認証を使用しないでください。
> Github のパスワード認証のサポートが削除されたため、Github ではパスワード認証の代わりに個人用アクセス トークンを使用する必要があります。 詳細については、[トークン認証](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)に関するページをご覧ください。

### <a name="git-additional-repositories"></a>Git の追加リポジトリ

パターンを使用する Git リポジトリを設定するために使用されるすべての構成可能なプロパティを、以下の一覧に示します。

> [!NOTE]
> 現在サポートされている名前付け規則は、ハイフン (-) を使用して単語を区切ることだけです。 たとえば、*defaultLabel* とはせずに、*default-label* としてください。

| プロパティ                           | 必須         | 特徴量                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | いいえ             | 指定された名前の Git リポジトリの設定で構成されるマップ。 |
| `repos."uri"`                      | はい (`repos` の場合) | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まる必要があります。 |
| `repos."name"`                     | はい (`repos` の場合) | Git リポジトリでの識別名。`repos` が存在する場合にのみ "_必須_"。 たとえば、*team-A* や *team-B*。 |
| `repos."pattern"`                  | いいえ             | アプリケーション名の照合に使用される文字列の配列。 各パターンでは、ワイルドカードで `{application}/{profile}` の形式を使用します。 |
| `repos."default-label"`            | いいえ             | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `repos."search-paths`"             | いいえ             | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |
| `repos."username"`                 | いいえ             | Git リポジトリ サーバーへのアクセスに使用されるユーザー名。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |
| `repos."password"`                 | いいえ             | Git リポジトリ サーバーへのアクセスに使用されるパスワードまたは個人用アクセス トークン。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |
| `repos."private-key"`              | いいえ             | Git リポジトリにアクセスするための SSH 秘密キー。URI が *git@* または *ssh://* で始まる場合は "_必須_"。 |
| `repos."host-key"`                 | いいえ             | Git リポジトリ サーバーのホスト キーには、`host-key-algorithm` で対応されているアルゴリズム プレフィックスを含めないでください。 |
| `repos."host-key-algorithm"`       | いいえ             | ホスト キー アルゴリズム。*ssh-dss*、*ssh-rsa*、*ecdsa-sha2-nistp256*、*ecdsa-sha2-nistp384*、*ecdsa-sha2-nistp521* のいずれかを指定する必要があります。 `host-key` が存在する場合にのみ "*必須*"。 |
| `repos."strict-host-key-checking"` | いいえ             | プライベートな `host-key` が指定されたら、Config Server インスタンスが起動に失敗するかどうかを示します。 *true* (既定値) または *false* にする必要があります。 |

次の表に、 **[追加のリポジトリ]** セクションの例をいくつか示します。 詳細については、Spring のドキュメントの「[パターン マッチングと複数のリポジトリ](https://cloud.spring.io/spring-cloud-config/reference/html/#_pattern_matching_and_multiple_repositories)」を参照してください。

| パターン                        | 説明 |
| :------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| *test-config-server-app-0/\**   | パターンとリポジトリの URI は、`test-config-server-app-0` という名前の Spring boot アプリケーションを任意のプロファイルと照合します。  |
| *test-config-server-app-1/dev*  | パターンとリポジトリの URI は、`test-config-server-app-1` という名前の Spring boot アプリケーションを dev プロファイルと照合します。  |
| *test-config-server-app-2/prod* | パターンとリポジトリの URI は、`test-config-server-app-2` という名前の Spring boot アプリケーションを prod プロファイルと照合します。 |

:::image type="content" source="media/spring-cloud-tutorial-config-server/additional-repositories.png" lightbox="media/spring-cloud-tutorial-config-server/additional-repositories.png" alt-text="[追加のリポジトリ] テーブルの [パターン] 列が強調表示されている [構成サーバー] ページを示す Azure portal のスクリーンショット":::

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>自分の Config Server リポジトリを Azure Spring Cloud に接続する

自分の構成ファイルがリポジトリに保存されているので、Azure Spring Cloud をそれに接続する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure Spring Cloud の **[概要]** ページに移動します。

3. 左側のナビゲーション ウィンドウで **[Config Server]** を選択します。

4. **[既定のリポジトリ]** セクションで **[URI]** を "https://github.com/Azure-Samples/piggymetrics-config" に設定します。

5. **[検証]** を選択します。

    ![構成サーバーへの移動](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

6. 検証が完了したら、 **[適用]** を選択して変更を保存します。

    ![構成サーバーの検証](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

7. 構成の更新に数分かかる場合があります。

    ![構成サーバーの更新](media/spring-cloud-quickstart-launch-app-portal/updating-config.png)

8. 構成が完了すると、通知が届きます。

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>リポジトリの情報を Azure portal に直接入力する

#### <a name="default-repository"></a>既定のリポジトリ

* **パブリック リポジトリ**: **[既定のリポジトリ]** セクションで、 **[URI]** ボックスにリポジトリの URI を貼り付けます。  **[ラベル]** を「**config**」に設定します。 **[認証]** の設定が **[パブリック]** であることを確認し、 **[適用]** を選択して完了します。

* **プライベート リポジトリ**: Azure Spring Cloud では、基本的なパスワード (トークン) ベースの認証と SSH がサポートされています。

    * **基本認証**: **[既定のリポジトリ]** セクションの **[URI]** ボックスにリポジトリの URI を貼り付けてから、 **[認証]** ("鉛筆" アイコン) ボタンを選択します。 **[認証の編集]** ペインの **[認証の種類]** ボックスの一覧から **[HTTP 基本]** を選択し、ユーザー名とパスワード (トークン) を入力して、Azure Spring Cloud へのアクセスを許可します。 **[OK]** を選択し、 **[適用]** を選択して、Config Server インスタンスの設定を完了します。

    ![[認証の編集] ペインの基本認証](media/spring-cloud-tutorial-config-server/basic-auth.png)

    > [!CAUTION]
    > 一部の Git リポジトリ サーバーでは、**基本認証** に *personal-token* や *access-token* (パスワードなど) が使用されます。 その種のトークンは、有効期限切れになることがないため、Azure Spring Cloud でパスワードとして使用できます。 ただし、Bitbucket や Azure DevOps Server などの他の Git リポジトリ サーバーでは、*access-token* が 1 時間または 2 時間以内に期限切れになります。 つまり、それらのリポジトリ サーバーを Azure Spring Cloud で使用する場合、このオプションは非現実的です。
    > Github のパスワード認証のサポートが削除されたため、Github ではパスワード認証の代わりに個人用アクセス トークンを使用する必要があります。 詳細については、[トークン認証](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)に関するページをご覧ください。

    * **SSH**: **[既定のリポジトリ]** セクションの **[URI]** ボックスにリポジトリの URI を貼り付けてから、 **[認証]** ("鉛筆" アイコン) ボタンを選択します。 **[認証の編集]** ペインの **[認証の種類]** ボックスの一覧から **[SSH]** を選択し、**秘密キー** を入力します。 必要に応じて、**ホスト キー** と **ホスト キー アルゴリズム** を指定します。 Config Server リポジトリには公開キーを含めるようにしてください。 **[OK]** を選択し、 **[適用]** を選択して、Config Server インスタンスの設定を完了します。

    ![[認証の編集] ペインの SSH 認証](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="additional-repositories"></a>追加のリポジトリ

オプションの **追加のリポジトリ** を使用してサービスを構成する場合は、**既定のリポジトリ** と同じように、**URI** と **認証** を指定します。 パターンの **名前** を必ず指定し、 **[適用]** を選択してインスタンスに接続します。

### <a name="enter-repository-information-into-a-yaml-file"></a>リポジトリの情報を YAML ファイルに入力する

リポジトリの設定を含む YAML ファイルを記述した場合は、ローカル コンピューターから Azure Spring Cloud に YAML ファイルを直接インポートできます。 基本認証を使用するプライベート リポジトリ用の簡単な YAML ファイルは次のようになります。

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

**[設定のインポート]** ボタンを選択し、プロジェクト ディレクトリから YAML ファイルを選択します。 **[インポート]** を選択すると、 **[通知]** から `async` 操作がポップアップ表示されます。 1 - 2 分後に、成功したことが示されるはずです。

![Config Server の [通知] ペイン](media/spring-cloud-tutorial-config-server/local-yml-success.png)

YAML ファイルの情報が Azure portal に表示されます。 完了するには **[適用]** を選択します。

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Azure Spring Cloud 構成向け Azure Repos の使用

Azure Spring Cloud では、SSH または HTTP 基本認証によってセキュリティ保護されているパブリックの Git リポジトリにアクセスできます。 Azure Repos による作成と管理が簡単な最後のオプションを使用します。

### <a name="get-repo-url-and-credentials"></a>リポジトリ URL と資格情報を取得する

1. プロジェクトの Azure Repos ポータルで、 **[複製]** ボタンを選択します。

    ![[複製] ボタン](media/spring-cloud-tutorial-config-server/clone-button.png)

1. テキストボックスから複製 URL をコピーします。 この URL は通常、次の形式になります。

    ```text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    `@` を含め、`https://` の後と `dev.azure.com` の前にあるものをすべて削除します。 結果的に出来上がる URL は次の形式になります。

    ```text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    次のセクションで使用するためにこの URL を保存します。

1. **[Git 資格情報の生成]** を選択します。 ユーザー名とパスワードが表示されます。 次のセクションで使用するためにこれらを保存します。

### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Git リポジトリにアクセスするように Azure Spring Cloud を構成する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure Spring Cloud の **[概要]** ページに移動します。

1. 構成するサービスを選択します。

1. サービス ページの左ペインで、 **[設定]** の **[Config Server]** タブを選択します。前に作成したリポジトリを構成します。

   - 前のセクションから保存したリポジトリ URL を追加します。
   - **[認証]** を選択してから、 **[HTTP Basic]** を選択します。
   - __ユーザー名__ は前のセクションで保存したユーザー名です。
   - __パスワード__ は前のセクションで保存したパスワードです。
   - **[適用]** を選択し、処理が正常に完了するまで待ちます。

   ![Spring Cloud Config Server](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-configuration"></a>構成を削除する

**[Config Server]** タブに表示される **[リセット]** ボタンを選択すると、既存の設定を完全に消去できます。 GitHub から Azure DevOps への移動など、Config Server インスタンスを別のソースに接続する場合は、Config Server の設定を削除します。

## <a name="config-server-refresh"></a>Config Server の更新

プロパティが変更された場合、変更を行う前に、それらのプロパティを使用するサービスに通知する必要があります。 Spring Cloud の既定のソリューションでは、[更新イベント](https://spring.io/guides/gs/centralized-configuration/)を手動でトリガーします。これは、アプリ インスタンスが多数ある場合は実現できない可能性があります。 または Azure Spring Cloud において、構成クライアントが内部更新に基づいて変更をポーリングすることで、構成サーバーから値を自動的に更新できます。

- まず、spring-cloud-starter-azure-spring-cloud-client を、pom.xml の依存関係セクションに含めます。

  ```xml
  <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
      <version>2.4.0</version>
  </dependency>
  ```

- 2 つ目は自動更新を有効にし、application.yml で適切な更新間隔を設定します。 この例では、クライアントは 5 秒ごとに構成変更をポーリングします。これは、更新間隔に設定できる最小値です。
既定では、自動更新は false に設定され、refresh-interval は 60 秒に設定されます。

  ```yml
  spring:
    cloud:
      config:
        auto-refresh: true
        refresh-interval: 5
  ```

- 最後に、@refreshScope をコードに追加します。 この例では、変数 connectTimeout は5秒ごとに自動的に更新されます。

  ```java
  @RestController
  @RefreshScope
  public class HelloController {
      @Value("${timeout:4000}")
      private String connectTimeout;
  }
  ```

## <a name="next-steps"></a>次のステップ

この記事では、Spring Cloud Config Server インスタンスを有効にし、構成する方法を学習しました。 アプリケーション管理の詳細については、「[Azure Spring Cloud でアプリケーションをスケーリングする](./how-to-scale-manual.md)」を参照してください。
