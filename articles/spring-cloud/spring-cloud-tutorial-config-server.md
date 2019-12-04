---
title: Azure Spring Cloud で Config Server インスタンスを設定する | Microsoft Docs
description: このチュートリアルでは、Azure portal で自分の Azure Spring Cloud 向けに Spring Cloud Config Server インスタンスを設定する方法を説明します
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 7589a3a750e2fe04736bb3c8fc072c7a2c0a7358
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147541"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>チュートリアル:自分のサービス向けに Spring Cloud Config Server インスタンスを設定する

この記事では、Spring Cloud Config Server インスタンスを自分の Azure Spring Cloud サービスに接続する方法を紹介します。

Spring Cloud Config は、分散システムで外部化された構成用に、サーバー側とクライアント側のサポートを提供します。 Config Server インスタンスを使用すると、すべての環境にわたってアプリケーションの外部プロパティを一元的に管理できます。 詳細については、[Spring Cloud Config Server のリファレンス](https://spring.io/projects/spring-cloud-config)を参照してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 
* 既にプロビジョニングされ、実行されている Azure Spring Cloud サービス。 Azure Spring Cloud サービスを設定して起動するには、[Azure CLI を使用して Java Spring アプリケーションを起動する方法に関するクイックスタート](spring-cloud-quickstart-launch-app-cli.md)を参照してください。

## <a name="restriction"></a>Restriction

Git バックエンドで Config Server を使用する場合、いくつかの制限があります。 Config Server と Service Discovery にアクセスするため、一部のプロパティがお使いのアプリケーション環境に自動的に挿入されます。 これらのプロパティを Config Server ファイルからも構成すると、競合と予期しない動作が生じる場合があります。 プロパティには、次のようなものがあります。 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
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

| プロパティ        | 必須 | 機能                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | はい    | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まります。 |
| `default-label` | いいえ     | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `search-paths`  | いいえ     | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |

------

### <a name="private-repository-with-ssh-authentication"></a>SSH 認証を使用したプライベート リポジトリ

SSH を使用するプライベート Git リポジトリを設定するために使用されるすべての構成可能なプロパティを以下の表に示します。

> [!NOTE]
> 現在サポートされている名前付け規則は、ハイフン (-) を使用して単語を区切ることだけです。 たとえば、*default-label* は使用できますが、*defaultLabel* は使用できません。

| プロパティ                   | 必須 | 機能                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | はい    | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まる必要があります。 |
| `default-label`            | いいえ     | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `search-paths`             | いいえ     | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |
| `private-key`              | いいえ     | Git リポジトリにアクセスするための SSH 秘密キー。URI が *git@* または *ssh://* で始まる場合は "_必須_"。 |
| `host-key`                 | いいえ     | Git リポジトリ サーバーのホスト キーには、`host-key-algorithm` で対応されているアルゴリズム プレフィックスを含めないでください。 |
| `host-key-algorithm`       | いいえ     | ホスト キー アルゴリズム。*ssh-dss*、*ssh-rsa*、*ecdsa-sha2-nistp256*、*ecdsa-sha2-nistp384*、*ecdsa-sha2-nistp521* のいずれかを指定する必要があります。 `host-key` が存在する場合にのみ "*必須*"。 |
| `strict-host-key-checking` | いいえ     | プライベートな `host-key` が指定されたら、Config Server インスタンスが起動に失敗するかどうかを示します。 *true* (既定値) または *false* にする必要があります。 |

-----

### <a name="private-repository-with-basic-authentication"></a>基本認証を使用したプライベート リポジトリ

基本認証を使用するプライベート Git リポジトリを設定するために使用されるすべての構成可能なプロパティを、以下の一覧に示します。

> [!NOTE]
> 現在サポートされている名前付け規則は、ハイフン (-) を使用して単語を区切ることだけです。 たとえば、*defaultLabel* とはせずに、*default-label* としてください。

| プロパティ        | 必須 | 機能                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | はい    | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まる必要があります。 |
| `default-label` | いいえ     | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `search-paths`  | いいえ     | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |
| `username`      | いいえ     | Git リポジトリ サーバーへのアクセスに使用されるユーザー名。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |
| `password`      | いいえ     | Git リポジトリ サーバーへのアクセスに使用されるパスワード。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |

> [!NOTE]
> 多くの `Git` リポジトリ サーバーで、HTTP 基本認証のパスワードではなく、トークンの使用がサポートされています。 GitHub などの一部のリポジトリでは、トークンを無期限に保持できます。 しかし、一部の Git リポジトリ サーバー (Azure DevOps を含む) では、強制的にトークンの有効期限を数時間に設定しています。 トークンの有効期限が切れるリポジトリでは、Azure Spring Cloud でトークンベースの認証を使用しないでください。

### <a name="git-repositories-with-pattern"></a>パターンを使用する Git リポジトリ

パターンを使用する Git リポジトリを設定するために使用されるすべての構成可能なプロパティを、以下の一覧に示します。

> [!NOTE]
> 現在サポートされている名前付け規則は、ハイフン (-) を使用して単語を区切ることだけです。 たとえば、*defaultLabel* とはせずに、*default-label* としてください。

| プロパティ                           | 必須         | 機能                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | いいえ             | 指定された名前の Git リポジトリの設定で構成されるマップ。 |
| `repos."uri"`                      | はい (`repos` の場合) | Config Server バックエンドとして使用される Git リポジトリの URI は、*http://* 、*https://* 、*git@* 、*ssh://* のいずれかで始まる必要があります。 |
| `repos."name"`                     | はい (`repos` の場合) | Git リポジトリでの識別名。`repos` が存在する場合にのみ "_必須_"。 たとえば、*team-A* や *team-B*。 |
| `repos."pattern"`                  | いいえ             | アプリケーション名の照合に使用される文字列の配列。 各パターンでは、ワイルドカードで `{application}/{profile}` の形式を使用します。 |
| `repos."default-label"`            | いいえ             | Git リポジトリの既定のラベルは、リポジトリの "*ブランチ名*"、"*タグ名*"、"*コミット ID*" のいずれかにする必要があります。 |
| `repos."search-paths`"             | いいえ             | Git リポジトリのサブディレクトリを検索するために使用される文字列の配列。 |
| `repos."username"`                 | いいえ             | Git リポジトリ サーバーへのアクセスに使用されるユーザー名。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |
| `repos."password"`                 | いいえ             | Git リポジトリ サーバーへのアクセスに使用されるパスワード。Git リポジトリ サーバーで `Http Basic Authentication` がサポートされている場合は "_必須_"。 |
| `repos."private-key"`              | いいえ             | Git リポジトリにアクセスするための SSH 秘密キー。URI が *git@* または *ssh://* で始まる場合は "_必須_"。 |
| `repos."host-key"`                 | いいえ             | Git リポジトリ サーバーのホスト キーには、`host-key-algorithm` で対応されているアルゴリズム プレフィックスを含めないでください。 |
| `repos."host-key-algorithm"`       | いいえ             | ホスト キー アルゴリズム。*ssh-dss*、*ssh-rsa*、*ecdsa-sha2-nistp256*、*ecdsa-sha2-nistp384*、*ecdsa-sha2-nistp521* のいずれかを指定する必要があります。 `host-key` が存在する場合にのみ "*必須*"。 |
| `repos."strict-host-key-checking"` | いいえ             | プライベートな `host-key` が指定されたら、Config Server インスタンスが起動に失敗するかどうかを示します。 *true* (既定値) または *false* にする必要があります。 |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>自分の Config Server リポジトリを Azure Spring Cloud に接続する

自分の構成ファイルがリポジトリに保存されているので、Azure Spring Cloud をそれに接続する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure Spring Cloud の **[概要]** ページに移動します。

1. 構成するサービスを選択します。

1. サービス ページの左ペインで、 **[設定]** の **[Config Server]** タブを選択します。

![[Config Server] ウィンドウ](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>リポジトリの情報を Azure portal に直接入力する

#### <a name="default-repository"></a>既定のリポジトリ

* **パブリック リポジトリ**: **[既定のリポジトリ]** セクションで、 **[URI]** ボックスにリポジトリの URI を貼り付けます。  **[ラベル]** を「**config**」に設定します。 **[認証]** の設定が **[パブリック]** であることを確認し、 **[適用]** を選択して完了します。 

* **プライベート リポジトリ**: Azure Spring Cloud では、基本的なパスワード (トークン) ベースの認証と SSH がサポートされています。

    * **基本認証**: **[既定のリポジトリ]** セクションの **[URI]** ボックスにリポジトリの URI を貼り付けてから、 **[認証]** ("鉛筆" アイコン) ボタンを選択します。 **[認証の編集]** ペインの **[認証の種類]** ボックスの一覧から **[HTTP 基本]** を選択し、ユーザー名とパスワード (トークン) を入力して、Azure Spring Cloud へのアクセスを許可します。 **[OK]** を選択し、 **[適用]** を選択して、Config Server インスタンスの設定を完了します。

    ![[認証の編集] ペイン](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > GitHub など、一部の Git リポジトリ サーバーでは、**基本認証**に *personal-token* や *access-token* (パスワードなど) が使用されます。 その種のトークンは、有効期限切れになることがないため、Azure Spring Cloud でパスワードとして使用できます。 ただし、Bitbucket や Azure DevOps などの他の Git リポジトリ サーバーでは、*access-token* が 1 時間または 2 時間以内に期限切れになります。 つまり、それらのリポジトリ サーバーを Azure Spring Cloud で使用する場合、このオプションは非現実的です。

    * **SSH**: **[既定のリポジトリ]** セクションの **[URI]** ボックスにリポジトリの URI を貼り付けてから、 **[認証]** ("鉛筆" アイコン) ボタンを選択します。 **[認証の編集]** ペインの **[認証の種類]** ボックスの一覧から **[SSH]** を選択し、**秘密キー**を入力します。 必要に応じて、**ホスト キー**と**ホスト キー アルゴリズム**を指定します。 Config Server リポジトリには公開キーを含めるようにしてください。 **[OK]** を選択し、 **[適用]** を選択して、Config Server インスタンスの設定を完了します。

    ![[認証の編集] ペイン](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>パターン リポジトリ

オプションの**パターン リポジトリ**を使用してサービスを構成する場合は、**既定のリポジトリ**と同じように、**URI** と **認証**を指定します。 パターンの**名前**を必ず指定し、 **[適用]** を選択してインスタンスに接続します。 

### <a name="enter-repository-information-into-a-yaml-file"></a>リポジトリの情報を YAML ファイルに入力する

リポジトリの設定を含む YAML ファイルを記述した場合は、ローカル コンピューターから Azure Spring Cloud に YAML ファイルを直接インポートできます。 基本認証を使用するプライベート リポジトリ用の簡単な YAML ファイルは次のようになります。

```yml
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


## <a name="delete-your-app-configuration"></a>アプリ構成を削除する

構成ファイルを保存すると、 **[構成]** タブに **[Delete app configuration]\(アプリ構成の削除\)** ボタンが表示されます。このボタンを選択すると、既存の設定が完全に消去されます。 GitHub から Azure DevOps への移動など、Config Server インスタンスを別のソースに接続する場合にそれを選択してください。



## <a name="next-steps"></a>次の手順

このチュートリアルでは、Spring Cloud Config Server インスタンスを有効にし、構成する方法を学習しました。 アプリケーションの管理の詳細については、手動によるアプリのスケーリングに関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Spring Cloud でアプリケーションをスケーリングする](spring-cloud-tutorial-scale-manual.md)
