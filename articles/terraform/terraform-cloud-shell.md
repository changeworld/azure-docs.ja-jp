---
title: チュートリアル - Terraform 用に Azure Cloud Shell を構成する
description: このチュートリアルでは、Terraform と Azure Cloud Shell を使用して、認証とテンプレートの構成を省力化します。
keywords: Azure DevOps Terraform Cloud Shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945329"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>チュートリアル:Terraform 用に Azure Cloud Shell を構成する

Terraform は、macOS、Windows、または Linux の Bash コマンド ラインで適切に動作します。 [Azure Cloud Shell](/azure/cloud-shell/overview) の Bash エクスペリエンスで Terraform 構成を実行すると、独自の利点がいくつかあります。 このチュートリアルでは、Cloud Shell を使用して Azure にデプロイする Terraform スクリプトを記述する方法について説明します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>資格情報の自動構成

Terraform をインストールすると、Cloud Shell ですぐに使用できます。 Cloud Shell にログインしてインフラストラクチャを管理すると、Terraform スクリプトは Azure で認証されます。追加の構成は必要ありません。 自動認証は、次の 2 つの手動プロセスをバイパスします。
- Active Directory サービス プリンシパルの作成
- Azure Terraform プロバイダー変数の構成


## <a name="use-modules-and-providers"></a>モジュールとプロバイダーの使用

Azure Terraform モジュールには、Azure リソースにアクセスして変更するための資格情報が必要です。 Cloud Shell で Terraform モジュールを使用するには、次のコードを追加します。


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

ユーザーが `terraform` CLI コマンドのいずれかを使用するときに、Cloud Shell は環境変数を介して `azurerm` プロバイダーに必要な値を渡します。

## <a name="other-cloud-shell-developer-tools"></a>その他の Cloud Shell 開発者用ツール

ファイルとシェルの状態は、複数の Cloud Shell セッションにまたがって Azure Storage に維持されます。 ローカル コンピューターのファイルをコピーし、Cloud Shell にアップロードするには、[Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) を使用します。

Azure CLI は Cloud Shell で使用でき、`terraform apply` または `terraform destroy` が完了した後の構成のテストと作業のチェックに適したツールです。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [モジュール レジストリを使用した小規模な VM クラスターの作成](terraform-create-vm-cluster-module.md)