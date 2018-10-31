---
title: Terratest を使用して Azure で Terraform モジュールをテストする
description: Terratest を使用して Terraform モジュールをテストする方法を学習します。
services: terraform
ms.service: terraform
keywords: Terraform, DevOps, ストレージ アカウント, Azure, Terratest, 単体テスト, 統合テスト
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638713"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Terratest を使用して Azure で Terraform モジュールをテストする

Terraform モジュールは、再利用可能、構成可能、かつテスト可能なコンポーネントを作成するために使用されます。 これらにより、"コードとしてのインフラストラクチャ" の環境にカプセル化が組み込まれます。

他のソフトウェア コンポーネントと同様、品質保証は、Terraform モジュールにおいて重要な役割を果たします。 残念ながら、Terraform モジュールで単体テストと統合テストを作成する方法が説明されている利用可能なドキュメントはほとんどありません。 このチュートリアルでは、Microsoft が [Azure Terraform モジュール](https://registry.terraform.io/browse?provider=azurerm)を構築する際に採用したテスト インフラストラクチャとベスト プラクティスを紹介します。

Microsoft は、最も一般的なテスト インフラストラクチャをすべて検討したうえで、[Terratest](https://github.com/gruntwork-io/terratest) を使用することを選択しました。 Terratest は Go ライブラリとして実装されています。 これは、特定の仮想マシンへの HTTP 要求の発行や SSH など、一般的なインフラストラクチャ テスト タスク用のヘルパー関数とパターンのコレクションを提供します。 Terratest の主なメリットをいくつか次に示します。

- **インフラストラクチャを確認するのに便利なヘルパーを提供します。** この機能は、実際の環境で実際のインフラストラクチャを確認したい場合に便利です。
- **フォルダー構造が明確に構成されています。** テスト ケースは明確に構成され、[Terraform モジュールの標準のフォルダー構造](https://www.terraform.io/docs/modules/create.html#standard-module-structure)に従います。
- **すべてのテスト ケースが Go で記述されます。** ほとんどの Terraform 開発者は既に Go 開発者であるため、Terratest を使用すれば、さらに別のプログラミング言語を学習する必要はありません。 また、Terratest でテスト ケースを実行するために必要な依存関係は、Go と Terraform のみです。
- **このインフラストラクチャは、高い拡張性を備えています。** 追加の機能 (Azure に固有の機能など) を Terratest の上に拡張するのは難しくありません。

## <a name="prerequisites"></a>前提条件

この実践的なガイドは、プラットフォームに依存せず、Windows、Linux、または macOS 上で実行できます。 続行する前に、次のソフトウェアをインストールします。

- **Go プログラミング言語**: Terraform テスト ケースは [Go](https://golang.org/dl/) で記述されます。
- **dep**: [dep](https://github.com/golang/dep#installation) は、Go 向けの依存関係管理ツールです。
- **Azure CLI**: [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) は、Azure リソースを管理するためのコマンドライン ツールです  (Terraform では、サービス プリンシパル経由または [Azure CLI を介した](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html) Azure への認証がサポートされます)。
- **mage**: [mage 実行可能ファイル](https://github.com/magefile/mage/releases)を使用して、Terratest ケースの実行を簡略化する方法を学習します。 

## <a name="create-a-static-webpage-module"></a>静的 Web ページ モジュールを作成する

このチュートリアルでは、単一の HTML ファイルを Azure Storage Blob にアップロードして、静的 Web ページをプロビジョニングする Terraform モジュールを作成します。 このモジュールにより、世界中のユーザーが、このモジュールから返された URL を通じてこの Web ページにアクセスできるようになります。

> [!NOTE]
> このセクションで説明されるすべてのファイルは、[GoPath](https://github.com/golang/go/wiki/SettingGOPATH) の下に作成する必要があります。

まず、GoPath の `src` フォルダーの下に `staticwebpage` という名前の新しいフォルダーを作成します。 このチュートリアルの全体的なフォルダー構造を次に示します  (アスタリスク `(*)` が付いたファイルは、このセクションの重要な項目です)。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

静的 Web ページ モジュールは `./variables.tf` で宣言されている 3 つの入力を受け取ります。

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

前述のとおり、`./outputs.tf` で宣言された URL もこのモジュールによって出力されます。

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

これにより、このモジュールのメイン ロジックに移ります。 合計で 4 つのリソースがプロビジョニングされます。
- `website_name` 入力に `-staging-rg` が追加された名前のリソース グループ。
- `website_name` 入力に `data001` が追加された名前のストレージ アカウント。 ただし、ストレージ アカウントの名前の制限に従うために、モジュールによってすべての特殊文字が削除され、名前全体が小文字になります。
- 上記のストレージ アカウントで作成された固定の名前付きコンテナー `wwwroot`。
- `html_path` 入力から読み込まれ、`wwwroot/index.html` にアップロードされる単一の HTML ファイル。

静的 Web ページ モジュールのロジックは `./main.tf` で実装されています。

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>単体テスト

Terratest はもともと統合テスト用に設計されたツールです。つまり、Terratest では、実際の環境で実際のリソースがプロビジョニングされます。 特にプロビジョニングするリソースが大量にある場合、このようなジョブは非常に大きくなることがあります。 前のセクションで説明されているストレージ アカウントの命名変換ロジックがその良い例です。ここでは実際にリソースをプロビジョニングする必要があるわけではありません。命名変換ロジックが正しいことを確認したいだけです。

Terratest の柔軟性のおかげで、これは単体テストを使用して簡単に実現できます。 単体テストは、`terraform init` コマンドと `terraform plan` コマンドを実行するだけでローカルで実行されるテスト ケースです (ただしインターネット アクセスは必要です)。単体テスト ケースでは、`terraform plan` の出力が解析され、比較対象となる属性値が検索されます。

このセクションの残りの部分では、ストレージ アカウントの命名変換ロジックが正しいことを確認するために Terratest を使用して単体テストを実装する方法について説明します。 注目するのは、アスタリスク `(*)` の付いたファイルのみです。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

まず、空の HTML ファイル `./test/fixtures/storage-account-name/empty.html` は単なるプレースホルダーです。

ファイル `./test/fixtures/storage-account-name/main.tf` は、テスト ケースのスケルトンです。 ここで 1 つの入力 `website_name` を受け取ります。これは単体テストの入力でもあります。 そのロジックを次に示します。

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

最後に、主要なコンポーネントは、単体テストの実装です (`./test/storage_account_name_unit_test.go`)。

Go 開発者の方であれば、型 `*testing.T` の引数を受け取ることでこれが従来の Go テスト関数のシグネチャと一致することがわかります。

単体テストの本文には、合計で 5 つのケースが変数 `testCases` (キーは入力、値は期待される出力) に定義されています。 それぞれの単体テスト ケースについて、最初にテスト フィクスチャ フォルダー (`./test/fixtures/storage-account-name/`) をターゲットに `terraform init` を実行します。 

その後、`terraform plan` コマンドと共に特定のテスト ケース入力 (`tfOptions` の `website_name` 定義に注目してください) を使用して、結果を `./test/fixtures/storage-account-name/terraform.tfplan` に保存します (全体のフォルダー構造にはこれは表示されていません)。

次に、この結果ファイルは、公式の Terraform プラン パーサーを使用して、コードが判読できる構造に解析されます。

ここで、対象となる属性 (この場合は `azurerm_storage_account` の `name`) を探し、期待される出力と比較します。

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

単体テストを実行するには、コマンド ラインで次の手順を完了する必要があります。

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

約 1 分後に従来の Go テストの結果が表示されます。

### <a name="integration-test"></a>統合テスト

単体テストとは対照的に、統合テストは、エンドツーエンドの観点から実際の環境にリソースをプロビジョニングするために必要です。 Terratest は、そのような目的にも適しています。 Terraform モジュールのベスト プラクティスでは、いくつかのエンドツーエンドのサンプルが含まれた `examples` フォルダーも推奨されているので、これらのサンプルを統合テストとしてテストしてみましょう。 このセクションでは、それぞれアスタリスク `(*)` が付いた 3 つのファイルに焦点を当てます。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

まずサンプルから見ていきましょう。 `hello-world/` という名前の新しいサンプル フォルダーが `./examples/` フォルダーに作成されています。 ここで、アップロードする単純な HTML ページ `./examples/hello-world/index.html` を提供します。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

Terraform サンプル `./examples/hello-world/main.tf` は単体テストで示されているものと似ていますが、`homepage` という名前のアップロードされた HTML の URL も出力するという 1 つの大きな違いがあります。

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Terratest と従来の Go テスト関数は、統合テスト ファイル `./test/hello_world_example_test.go` に再度出現します。

単体テストとは異なり、統合テストでは実際のリソースが Azure に作成されます。これが、名前が競合しないように注意する必要がある理由です  (ストレージ アカウント名のようないくつかのグローバルに一意な名前には特に注意してください)。 したがって、テスト ロジックの最初のステップは、TerraTest によって提供される `UniqueId()` 関数を使用してランダムな `websiteName` を生成することです。 この関数により、小文字、大文字、または数字を含むランダムな名前が生成されます。 `tfOptions` により、すべての Terraform コマンドのターゲットが `./examples/hello-world/` フォルダーに設定され、さらに `website_name` がランダム化された `websiteName` に設定されます。

次に、`terraform init`、`terraform apply`、および `terraform output` が 1 つずつ実行されます。 Terratest から提供されるもう 1 つのヘルパー関数 `HttpGetWithCustomValidation()` は、`terraform output` によって返された出力 `homepage` URL に HTML がアップロードされていることを確認するために使用しました。その方法として、HTTP GET 状態コードを `200` と比較し、HTML コンテンツ内のいくつかのキーワードを検索しています。 最後に、Go の `defer` 機能を利用して、`terraform destroy` が実行されることが "約束" されています。

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

統合テストを実行するには、コマンド ラインで次の手順を完了する必要があります。

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

約 2 分後に従来の Go テストの結果が表示されます。 もちろん、以下を実行して、単体テストと統合テストの両方を実行することもできます。

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

統合テストは単体テストよりもはるかに時間がかかることがわかります (1 つの統合ケースで 2 分、5 つの単体ケースで 1 分)。 しかし、単体テストをいつ使用し、統合テストをいつ利用するかについては、お客様の判断になります。 通常は、Terraform HCL 関数を使用する複雑なロジックに対して単体テストを使用し、統合テストについてはユーザーのエンドツーエンドの観点から使用することをお勧めします。

## <a name="use-mage-to-simplify-running-terratest-cases"></a>mage を使用して Terratest ケースの実行を簡略化する 

ここまで見てきたとおり、シェル内でテスト ケースを実行するのは、異なるディレクトリに移動して異なるコマンドを実行する必要があるため、簡単な作業ではありません。 これが、プロジェクトにビルド システムを導入する理由です。 このセクションでは、Go ビルド システムの mage を使用してこのジョブを行います。

mage が必要とするのは、プロジェクトのルート ディレクトリ内の `magefile.go` のみです (下図の中で `(+)` とマークされています)。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

`./magefile.go` の 1 つの例を次に示します。 Go で記述されたこのビルド スクリプトには、5 つのビルド ステップが実装されています。
- `Clean`: このステップでは、テストの実行中に生成されたファイルや一時ファイルをすべて削除します。
- `Format`: このステップでは、`terraform fmt` と `go fmt` を実行してコード ベースを書式設定します。
- `Unit`: このステップでは、`./test/` フォルダーの下にある (関数名規則 `TestUT_*` が使用される) すべての単体テストを実行します。
- `Integration`: `Unit` と似ていますが、単体テストではなく統合テスト (`TestIT_*`) を実行します。
- `Full`: このステップでは、`Clean`、`Format`、`Unit', and `、Integration を順に実行します。

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build/test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

前のステップの実行方法と同様に、次のコマンドを使用して完全なテスト スイートを実行できます。

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

最後のコマンド ラインを `mage unit` や `mage clean` のような任意の mage のステップに自由に置き換えてください。 ここにはまだ多数のコマンド ラインがあると考える方もいることでしょう。`az login` のほかに `dep` コマンドも magefile に埋め込むのは良い考えです。 ただし、そのコードはここでは示しません。 mage を使用するもう 1 つのステップでは、Go パッケージ システムを使用してステップを共有できる可能性があります。 そのため、共通の実装を参照して依存関係 (`mg.Deps()`) を宣言するだけで、すべてのモジュールにわたって magefile を簡略化できる可能性があります。

> [!NOTE]
> **オプション: 受け入れテストを実行するようにサービス プリンシパル環境変数を設定する**
> 
> テストの前に `az login` を実行する代わりに、サービス プリンシパル環境変数を設定して Azure 認証を行うことができます。 Terraform から、[環境変数名の一覧](https://www.terraform.io/docs/providers/azurerm/index.html#testing)が公開されています  (必要になるのは、これらの環境変数のうち最初の 4 つのみです)。[これらの環境変数の値を取得する](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)方法が説明された詳しい手順も Terraform から公開されています。

## <a name="next-steps"></a>次の手順

Terratest の詳細については、[GitHub のページ](https://github.com/gruntwork-io/terratest)を参照してください。 mage については、[GitHub のページ](https://github.com/magefile/mage)と [mage のホーム ページ](https://magefile.org/)に有用な情報があります。
