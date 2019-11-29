---
title: チュートリアル - Terraform を使用して Azure デプロイ スロットでインフラストラクチャをプロビジョニングする
description: Terraform と Azure プロバイダーのデプロイ スロットの使用に関するチュートリアル
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 68c790b4fad442d94e6ac82d1a545b8554d2dd4f
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159186"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>チュートリアル:Terraform を使用して Azure デプロイ スロットでインフラストラクチャをプロビジョニングする

[Azure デプロイ スロット](/azure/app-service/deploy-staging-slots)を使用し、アプリの異なるバージョン間をスワップできます。 この機能によって、デプロイ分断の影響が最小限に抑えられます。 

この記事では、GitHub と Azure を使用して 2 つのアプリをデプロイする方法を説明しながら、デプロイ スロットの使用例を紹介します。 1 つのアプリが運用スロットでホストされています。 2 番目のアプリはステージング スロットでホストされています。 ("運用" と "ステージング" という名前は任意です。 これらは、実際のシナリオに適したものにすることができます。)デプロイ スロットを構成したら、必要に応じて Terraform を使用して 2 つのスロット間をスワップします。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

- **GitHub アカウント**:テスト GitHub リポジトリをフォークして使用するには、[GitHub](https://www.github.com) アカウントが必要です。

## <a name="create-and-apply-the-terraform-plan"></a>Terraform プランの作成と適用

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. [Azure Cloud Shell](/azure/cloud-shell/overview) を開きます。 前に環境を選択しなかった場合、環境として **Bash** を選択します。

    ![Cloud Shell のプロンプト](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. ディレクトリを `clouddrive` ディレクトリに変更します。

    ```bash
    cd clouddrive
    ```

1. `deploy` という名前のディレクトリを作成します。

    ```bash
    mkdir deploy
    ```

1. `swap` という名前のディレクトリを作成します。

    ```bash
    mkdir swap
    ```

1. `ls` bash コマンドを使用し、両方のディレクトリが正常に作成されたことを確認します。

    ![ディレクトリを作成した後のクラウド シェル](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. ディレクトリを `deploy` ディレクトリに変更します。

    ```bash
    cd deploy
    ```

1. Cloud Shell で、`deploy.tf` という名前のファイルを作成します。

    ```bash
    code deploy.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

1. これでファイルが作成されたので、その内容を確認してください。

    ```bash
    cat deploy.tf
    ```

1. Terraform を初期化します。

    ```bash
    terraform init
    ```

1. Terraform プランを作成します。

    ```bash
    terraform plan
    ```

1. `deploy.tf` 構成ファイルに定義されているリソースをプロビジョニングします。 (プロンプトに `yes` と入力してアクションを確認します)。

    ```bash
    terraform apply
    ```

1. Cloud Shell ウィンドウを閉じます。

1. Azure Portal のメイン メニューで、 **[リソース グループ]** を選択します。

    ![ポータルの "リソース グループ"](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. **[リソース グループ]** タブで、 **[slotDemoResourceGroup]** を選択します。

    ![Terraform によって作成されたリソース グループ](./media/terraform-slot-walkthru/resource-group.png)

Terraform によって作成されたすべてのリソースが表示されます。

![Terraform によって作成されたリソース](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>テスト プロジェクトをフォークする

デプロイ スロットの作成、スワップ イン、スワップ アウトをテストする前に、GitHub のテスト プロジェクトをフォークする必要があります。

1. [GitHub 上にある awesome-terraform リポジトリ](https://github.com/Azure/awesome-terraform)にアクセスします。

1. **awesome-terraform** リポジトリをフォークします。

    ![GitHub の awesome-terraform リポジトリをフォークする](./media/terraform-slot-walkthru/fork-repo.png)

1. 環境に合わせて、プロンプトに従ってフォークします。

## <a name="deploy-from-github-to-your-deployment-slots"></a>GitHub からデプロイ スロットにデプロイする

テスト プロジェクト リポジトリをフォークしたら、次の手順でデプロイ スロットを構成します。

1. Azure Portal のメイン メニューで、 **[リソース グループ]** を選択します。

1. **[slotDemoResourceGroup]** を選択します。

1. **[slotAppService]** を選択します。

1. **[デプロイ オプション]** を選択します。

    ![App Service リソースのデプロイ オプション](./media/terraform-slot-walkthru/deployment-options.png)

1. **[デプロイ オプション]** タブで **[ソースの選択]** を選択し、 **[GitHub]** を選択します。

    ![デプロイ ソースを選択する](./media/terraform-slot-walkthru/select-source.png)

1. Azure が接続し、すべてのオプションが表示されたら、 **[承認]** を選択します。

1. **[承認]** タブで **[承認]** を選択し、Azure から GitHub アカウントにアクセスするために必要な資格情報を入力します。 

1. Azure で GitHub の資格情報が検証されると、承認プロセスが完了したことを示すメッセージが表示されます。 **[OK]** を選択して **[承認]** タブを閉じます。

1. **[組織の選択]** を選択し、組織を選択します。

1. **[プロジェクトの選択]** を選択します。

1. **[プロジェクトの選択]** タブで **awesome-terraform** プロジェクトを選択します。

    ![awesome-terraform プロジェクトを選択する](./media/terraform-slot-walkthru/choose-project.png)

1. **[ブランチの選択]** を選択します。

1. **[ブランチの選択]** タブで **[master]** を選択します。

    ![master ブランチを選択する](./media/terraform-slot-walkthru/choose-branch-master.png)

1. **[デプロイ オプション]** タブで **[OK]** を選択します。

この時点で、 運用スロットのデプロイが完了しました。 ステージング スロットをデプロイするには、次の変更を加えて前の手順を実行します。

- 手順 3 で、**slotAppServiceSlotOne** リソースを選択します。

- 手順 13 で、master ブランチではなく working ブランチを選択します。

    ![working ブランチを選択する](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>アプリのデプロイをテストする

前のセクションでは、GitHub の異なるブランチからデプロイするように **slotAppService** と **slotAppServiceSlotOne** の 2 つのスロットを設定しました。 Web アプリケーションをプレビューして、正常にデプロイされたことを確認しましょう。

1. Azure Portal のメイン メニューで、 **[リソース グループ]** を選択します。

1. **[slotDemoResourceGroup]** を選択します。

1. **[slotAppService]** または **[slotAppServiceSlotOne]** を選択します。

1. [概要] ページで **[URL]** を選択します。

    ![[概要] タブで [URL] を選択してアプリをレンダリングする](./media/terraform-slot-walkthru/resource-url.png)

1. 選択したアプリに応じて、次の結果が表示されます。
    - **slotAppService** Web アプリ - ページ タイトルが **Slot Demo App 1** という青色のページです。 
    - **slotAppServiceSlotOne** Web アプリ - ページ タイトルが **Slot Demo App 2** という緑色のページです。

    ![アプリをプレビューしてアプリが正しくデプロイされたことをテストする](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>2 つのデプロイ スロットをスワップする

2 つのデプロイ スロットのスワップをテストするには、次の手順を実行します。
 
1. **slotAppService** (ページが青色のアプリケーション) を実行しているブラウザー タブに切り替えます。 

1. 別のタブで Azure Portal に戻ります。

1. Cloud Shell を開きます。

1. ディレクトリを **clouddrive/swap** ディレクトリに変更します。

    ```bash
    cd clouddrive/swap
    ```

1. Cloud Shell で、`swap.tf` という名前のファイルを作成します。

    ```bash
    code swap.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

1. Terraform を初期化します。

    ```bash
    terraform init
    ```

1. Terraform プランを作成します。

    ```bash
    terraform plan
    ```

1. `swap.tf` 構成ファイルに定義されているリソースをプロビジョニングします。 (プロンプトに `yes` と入力してアクションを確認します)。

    ```bash
    terraform apply
    ```

1. Terraform がスロットをスワップしたら、ブラウザーに戻ります。 ページを更新します。 

**slotAppServiceSlotOne** ステージング スロットの Web アプリは、production スロットとスワップされ、緑色にレンダリングされます。 

![デプロイ スロットのスワップの完了](./media/terraform-slot-walkthru/slots-swapped.png)

アプリの元の production バージョンに戻るには、`swap.tf` 構成ファイルから作成した Terraform プランを再適用します。

```bash
terraform apply
```

アプリがスワップされると、元の構成が表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure での Terraform の使用について詳細を参照](/azure/terraform)