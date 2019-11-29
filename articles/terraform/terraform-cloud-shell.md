---
title: チュートリアル - Terraform 用に Azure Cloud Shell を構成する
description: Terraform と Azure Cloud Shell を使用すると、認証とテンプレートの構成が簡単になります。
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159129"
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
}
```

ユーザーが `terraform` CLI コマンドのいずれかを使用するときに、Cloud Shell は環境変数を介して `azurerm` プロバイダーに必要な値を渡します。

## <a name="other-cloud-shell-developer-tools"></a>その他の Cloud Shell 開発者用ツール

ファイルとシェルの状態は、複数の Cloud Shell セッションにまたがって Azure Storage に維持されます。 ローカル コンピューターのファイルをコピーし、Cloud Shell にアップロードするには、[Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) を使用します。

Azure CLI は Cloud Shell で使用でき、`terraform apply` または `terraform destroy` が完了した後の構成のテストと作業のチェックに適したツールです。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [モジュール レジストリを使用した小規模な VM クラスターの作成](terraform-create-vm-cluster-module.md)
