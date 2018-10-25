---
title: Azure Container Registry タスクの参照 - YAML
description: YAML で ACR タスク用のタスクを定義するための参照 (タスクのプロパティ、ステップの種類、ステップのプロパティ、およびビルトイン変数など)。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: a26b766c18ea2b14796af796abe7f36aa4fc1c66
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854951"
---
# <a name="acr-tasks-reference-yaml"></a>ACR タスクの参照: YAML

ACR タスクでの複数ステップのタスク定義では、コンテナーのビルド、テスト、および修正プログラムの適用に重点を置いたコンテナーを中心としたコンピューティング プリミティブを提供します。 この記事では、複数ステップのタスクを定義する YAML ファイルのコマンド、パラメーター、プロパティ、および構文について説明します。

この記事には、ACR タスク用の複数ステップのタスクの YAML ファイルを作成するための参照が含まれています。 ACR タスクの概要については、[ACR タスクの概要](container-registry-tasks-overview.md)に関するページを参照してください。

> [!IMPORTANT]
> ACR タスクの複数ステップのタスクの機能は、現在プレビュー段階です。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml ファイルの形式

ACR タスクでは、標準の YAML 構文で複数ステップのタスク宣言をサポートしています。 タスクのステップは YAML ファイルで定義します。このファイルは、後から手動で実行することも、Git コミットまたはベース イメージの更新時に自動的にトリガーすることもできます。 この記事では、ステップを含むファイルとして `acr-task.yaml` を参照していますが、ACR タスクは、[サポートされる拡張子](#supported-task-filename-extensions)を持つ任意の有効なファイル名をサポートしています。

最上位レベルの `acr-task.yaml` プリミティブは、**タスク プロパティ**、**ステップの種類**、および**ステップ プロパティ**です。

* [タスク プロパティ](#task-properties)は、タスクの実行を通じてすべてのステップに適用されます。 次の 3 つのグローバル タスク プロパティがあります。
  * version
  * stepTimeout
  * totalTimeout
* [タスク ステップの種類](#task-step-types)は、タスクで実行できるアクションの種類を表します。 次の 3 つのステップの種類があります。
  * build
  * push
  * cmd
* [タスク ステップ プロパティ](#task-step-properties)は、個々のステップに適用されるパラメーターです。 次を含む複数のステップ プロパティがあります。
  * startDelay
  * timeout
  * when
  * その他。

いくつかの共通のステップ プロパティを含む、`acr-task.yaml` ファイルの基本形式を次に示します。 これは、利用可能なすべてのステップ プロパティまたはステップの種類の使用方法の完全に表しているわけではありませんが、基本的なファイル形式の簡単な概要を提供します。

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
      startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>サポートされているタスクのファイル名拡張子

ACR タスクでは、タスク ファイルとして処理する、複数のファイル名拡張子 (`.yaml` など) が予約されています。 次の一覧内に*ない*拡張子は、ACR タスクではすべて Dockerfile と見なされます: .yaml .yml、.toml、.json、.sh、.bash、.zsh、.ps1、.ps、.cmd、.bat、.ts、.js、.php、.py、.rb .lua

YAML は、ACR タスクで現在サポートされている唯一のファイル形式です。 その他のファイル名拡張子は、将来のサポートの可能性のために予約されています。

## <a name="run-the-sample-tasks"></a>サンプル タスクを実行する

この記事の以降のセクションには、参照されるいくつかのサンプル タスク ファイルがあります。 サンプル タスクは、パブリックの GitHub リポジトリ ([Azure-Samples/acr-tasks][acr-tasks]) にあります。 これらは Azure CLI コマンド [az acr run][az-acr-run] で実行できます。 サンプル コマンドは、次のようになります。

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

サンプル コマンドの書式設定では、Azure CLI で既定のレジストリが構成されていることを前提としているため、`--registry` パラメーターが省略されています。 既定のレジストリを構成するには、`acr=REGISTRY_NAME` 値を受け入れる `--defaults` パラメーターを使用して、[az configure][az-configure] コマンドを使用します。

たとえば、Azure CLI を "myregistry" という名前の既定のレジストリで構成するには、次のようにします。

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>タスク プロパティ

タスク プロパティは通常、`acr-task.yaml` ファイルの上部に表示されます。これらは、タスクの完全な実行を通じて適用されるグローバル プロパティです。 これらのグローバル プロパティの一部は、個々のステップ内でオーバーライドできます。

| プロパティ | type | 省略可能 | 説明 | オーバーライドのサポート | 既定値 |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | いいえ  | ACR タスク サービスによって解析される `acr-task.yaml` ファイルのバージョン。 ACR タスクは、下位互換性の維持に努めていますが、この値により ACR タスクが定義されたバージョン内で互換性を維持することが可能になります。 | いいえ  | なし |
| `stepTimeout` | int (秒) | [はい] | ステップが実行できる最大秒数。 このプロパティは、ステップの [timeout](#timeout) プロパティを設定することで、ステップ内でオーバーライドできます。 | [はい] | 600 (10 分) |
| `totalTimeout` | int (秒) | [はい] | タスクが実行できる最大秒数。 "実行" には、成功か失敗かにかかわらず、タスク内のすべてのステップを実行して完了するまでが含まれます。 また、検出されたイメージの依存関係とタスクの実行状態など、タスク出力の印刷も含まれています。 | いいえ  | 3600 (1 時間) |

## <a name="task-step-types"></a>タスク ステップの種類

ACR タスクでは、次の 3 種類のステップがサポートされています。 ステップの種類はそれぞれ複数のプロパティをサポートしており、各ステップの種類のセクションで詳しく説明されています。

| ステップの種類 | 説明 |
| --------- | ----------- |
| [`build`](#build) | 使い慣れた `docker build` 構文を使用してコンテナー イメージをビルドします。 |
| [`push`](#push) | 新しくビルドされたイメージ、またはタグが付け直されたイメージのコンテナー レジストリへの `docker push` を実行します。 Azure Container Registry、その他のプライベート レジストリ、およびパブリック Docker Hub がサポートされています。
| [`cmd`](#cmd) | コンテナーの `[ENTRYPOINT]` に渡されるパラメーターを使用して、コンテナーをコマンドとして実行します。 `cmd` ステップの種類は、env や detach などのパラメーターと、その他の使い慣れた `docker run` コマンド オプションをサポートしており、コンテナーの同時実行を使用した単体テストと機能テストを有効にします。 |

## <a name="build"></a>build

コンテナー イメージをビルドします。 `build` ステップの種類は、マルチ テナントを表します。これはファースト クラスのプリミティブとしてクラウド内で `docker build` を実行する安全な手段です。

### <a name="syntax-build"></a>構文: build

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

`build` ステップの種類では、次のパラメーターがサポートされています。

| パラメーター | 説明 | 省略可能 |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | ビルドされたイメージの完全修飾 `image:tag` を定義します。<br /><br />イメージは機能テストなどの内部タスクの検証に使用することができるため、すべてのイメージでレジストリへの `push` が必要なわけではありません。 ただし、タスクの実行内でイメージをインスタンス化するには、イメージに参照する名前が必要です。<br /><br />`az acr build` とは異なり、ACR タスクの実行では、既定のプッシュ動作は提供されません。 ACR タスクでは、既定のシナリオは、イメージをビルド、検証してからプッシュできることを前提としています。 必要に応じてビルドされたイメージをプッシュする方法については、「[push](#push)」を参照してください。 | [はい] |
| `-f` &#124; `--file` | `docker build` に渡される Dockerfile を指定します。 指定しない場合、コンテキストのルートにある既定の Dockerfile が想定されます。 代替の Dockerfile を指定するには、コンテキストのルートへの相対ファイル名を渡します。 | [はい] |
| `context` | `docker build` に渡されるルート ディレクトリ。 各タスクのルート ディレクトリは、共有の [workingDirectory](#task-step-properties) に設定され、関連付けられた Git の複製されたディレクトリのルートが含まれます。 | いいえ  |

### <a name="properties-build"></a>プロパティ: build

`build` ステップの種類では、次のプロパティをサポートしています。これらプロパティの詳細については、この記事の「[タスク ステップ プロパティ](#task-step-properties)」を参照してください。

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 省略可能 |
| `entryPoint` | string | 省略可能 |
| `env` | [string, string, ...] | 省略可能 |
| `id` | string | 省略可能 |
| `ignoreErrors` | bool | 省略可能 |
| `keep` | bool | 省略可能 |
| `startDelay` | int (秒) | 省略可能 |
| `timeout` | int (秒) | 省略可能 |
| `when` | [string, string, ...] | 省略可能 |
| `workingDirectory` | string | 省略可能 |

### <a name="examples-build"></a>例: build

#### <a name="build-image---context-in-root"></a>イメージのビルド - ルートのコンテキスト

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>イメージのビルド - サブディレクトリのコンテキスト

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

1 つ以上のビルドされたイメージ、またはタグが付け直されたイメージをコンテナー レジストリにプッシュします。 Azure Container Registry などのプライベート レジストリや、パブリック Docker Hub へのプッシュをサポートします。

### <a name="syntax-push"></a>構文: push

`push` ステップの種類は、イメージのコレクションをサポートしています。 YAML コレクション構文では、インラインと入れ子になった形式をサポートしています。 1 つのイメージのプッシュは通常、インライン構文を使用して表されます。

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

わかりやすくするため、複数のイメージをプッシュするときに、入れ子になった構文を使用します。

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>プロパティ: push

`push` ステップの種類では、次のプロパティをサポートしています。 これらプロパティの詳細については、この記事の「[タスク ステップ プロパティ](#task-step-properties)」を参照してください。

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | 省略可能 |
| `id` | string | 省略可能 |
| `ignoreErrors` | bool | 省略可能 |
| `startDelay` | int (秒) | 省略可能 |
| `timeout` | int (秒) | 省略可能 |
| `when` | [string, string, ...] | 省略可能 |

### <a name="examples-push"></a>例: push

#### <a name="push-multiple-images"></a>複数のイメージをプッシュする

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>ビルド、プッシュ、実行

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` ステップの種類は、コンテナーを実行します。

### <a name="syntax-cmd"></a>構文: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>プロパティ: cmd

`cmd` ステップの種類では、次のプロパティをサポートしています。

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 省略可能 |
| `entryPoint` | string | 省略可能 |
| `env` | [string, string, ...] | 省略可能 |
| `id` | string | 省略可能 |
| `ignoreErrors` | bool | 省略可能 |
| `keep` | bool | 省略可能 |
| `startDelay` | int (秒) | 省略可能 |
| `timeout` | int (秒) | 省略可能 |
| `when` | [string, string, ...] | 省略可能 |
| `workingDirectory` | string | 省略可能 |

これらプロパティの詳細については、この記事の「[タスク ステップ プロパティ](#task-step-properties)」を参照してください。

### <a name="examples-cmd"></a>例: cmd

#### <a name="run-hello-world-image"></a>hello-world イメージの実行

このコマンドは、Docker Hub 上の [hello-world](https://hub.docker.com/_/hello-world/) イメージを参照する `hello-world.yaml` タスク ファイルを実行します。

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>bash イメージとエコー "hello world" の実行

このコマンドは、Docker Hub 上の [bash](https://hub.docker.com/_/bash/) イメージを参照する `bash-echo.yaml` タスク ファイルを実行します。

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>特定の bash イメージ タグを実行する

イメージの特定のバージョンを実行するには、`cmd` でタグを指定します。

このコマンドは、Docker Hub 上の [bash:3.0](https://hub.docker.com/_/bash/) イメージを参照する `bash-echo-3.yaml` タスク ファイルを実行します。

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>カスタム イメージを実行する

`cmd` ステップの種類は、標準の `docker run` 形式を使用してイメージを参照します。 レジストリで始まらないイメージは、docker.io から始まると見なされます。 前の例は、次のように表すこともできます。

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

標準の `docker run` イメージの参照規約を使用することで、`cmd` で任意のプライベート レジストリまたはパブリック Docker Hub に存在するイメージを実行できます。 ACR タスクが実行されているのと同じレジストリ内のイメージを参照している場合は、レジストリ資格情報を指定する必要はありません。

* Azure Container Registry に存在するイメージを実行する

    `[myregistry]` を実際のレジストリの名前に置き換えます。

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Run 変数でレジストリの参照を汎用化する

    `acr-task.yaml` ファイル内のレジストリ名をハードコーディングする代わりに、[Run 変数](#run-variables)を使用して移植性を高めることができます。 `Run.Registry` 変数は実行時に、タスクが実行されているレジストリの名前に展開されます。

    前のタスクを任意の Azure Container Registry で動作するように汎用化するには、イメージ名で [Run.Registry](#runregistry) 変数を参照します。

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>タスク ステップ プロパティ

各ステップの種類は、その種類に適した複数のプロパティをサポートしています。 次の表では、利用可能なすべてのステップのプロパティを定義します。 すべてのステップの種類が、すべてのプロパティをサポートしているわけではありません。 各ステップの種類でこれらのプロパティが利用可能かどうかを確認するには、「[cmd](#cmd)」、「[build](#build)」、「[push](#push)」 のステップの種類の参照セクションを参照してください。

| プロパティ | type | 省略可能 | 説明 |
| -------- | ---- | -------- | ----------- |
| `detach` | bool | [はい] | 実行時にコンテナーをデタッチする必要があるかどうか。 |
| `entryPoint` | string | [はい] | ステップのコンテナーの `[ENTRYPOINT]` をオーバーライドします。 |
| `env` | [string, string, ...] | [はい] | ステップの環境変数を定義する `key=value` 形式での文字列の配列。 |
| [`id`](#example-id) | string | [はい] | タスク内のステップを一意に識別します。 タスク内のその他のステップでは、`when` での依存関係のチェックなどのために、ステップの `id` を参照できます。<br /><br />`id` は実行中のコンテナーの名前でもあります。 タスク内のその他のコンテナーで実行されているプロセスは、その DNS ホスト名として、または docker ログ [id] などでアクセスするために `id` を参照できます。 |
| `ignoreErrors` | bool | [はい] | `true` に設定すると、実行中にエラーが発生したかどうかに関係なく、ステップは完了としてマークされます。 既定値: `false`。 |
| `keep` | bool | [はい] | 実行後にステップのコンテナーを保持する必要があるかどうか。 |
| `startDelay` | int (秒) | [はい] | ステップの実行を遅らせる秒数。 |
| `timeout` | int (秒) | [はい] | ステップが終了されるまでに実行できる最大秒数。 |
| [`when`](#example-when) | [string, string, ...] | [はい] | タスク内で 1 つ以上のその他のステップに対するステップの依存関係を構成します。 |
| `workingDirectory` | string | [はい] | ステップ用の作業ディレクトリを設定します。 既定では、ACR タスクは作業ディレクトリとしてルート ディレクトリを作成します。 ただし、ビルドに複数のステップがある場合は、同じ作業ディレクトリを指定することで、前のステップは後のステップと成果物を共有することができます。 |

### <a name="examples-task-step-properties"></a>例: タスク ステップ プロパティ

#### <a name="example-id"></a>例: id

機能テスト イメージをインスタンス化する 2 つのイメージをビルドします。 各ステップは、タスク内のその他のステップがその `when` プロパティ内で参照する一意の `id` で識別されます。

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>例: when

`when` プロパティは、タスク内のその他のステップに対するステップの依存関係を指定します。 次の 2 つのパラメーター値がサポートされます。

* `when: ["-"]`: 他のステップへの依存関係がないことを示します。 `when: ["-"]` を指定するステップは、すぐに実行が開始され、同時ステップの実行を有効にします。
* `when: ["id1", "id2"]`: ステップが `id` "id1" と `id` "id2" のステップに依存していることを示します。 このステップは、"id1" と "id2" の両方のステップが完了するまで実行されません。

`when` がステップで指定されていない場合、そのステップは `acr-task.yaml` ファイル内の前のステップの完了に依存します。

`when` を使用しないシーケンシャル ステップの実行:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

`when` を使用したシーケンシャル ステップの実行:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

並列イメージのビルド:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

並列イメージのビルドと依存のテスト:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run 変数

ACR タスクには、タスク ステップを実行するときに使用できる変数の既定のセットが含まれています。 これらの変数は、`{{.Run.VariableName}}` の形式を使用してアクセスできます。`VariableName` は次のいずれかです。

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Run&#46;ID

各 Run、`az acr run` の使用、または `az acr task create` を通じて作成されたタスクのトリガー ベースの実行は、一意の ID を持ちます。 ID は、現在実行中の Run を表します。

通常、イメージに一意にタグ付けするために使用されます。

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

レジストリの完全修飾サーバー名。 通常、タスクが実行されているレジストリをまとめて参照するために使用されます。

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

実行が開始された現在の UTC 時間。

## <a name="next-steps"></a>次の手順

複数ステップのタスクの概要については、「[Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md)」 (ACR タスクで複数ステップのビルド、テスト、修正プログラムの適用タスクを実行する) を参照してください。

シングル ステップのビルドについては、[ACR タスクの概要](container-registry-tasks-overview.md)に関するページを参照してください。

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure