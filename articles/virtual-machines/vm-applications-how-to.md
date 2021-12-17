---
title: VM アプリケーション パッケージの作成とデプロイ (プレビュー)
description: Azure Compute Gallery を使用して VM アプリケーションを作成およびデプロイする方法について説明します。
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/02/2021
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b98d8da852ed280638af33c6e9c52e1ea6963fbb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709028"
---
# <a name="create-and-deploy-vm-applications-preview"></a>VM アプリケーションの作成とデプロイ (プレビュー)

VM アプリケーションは、仮想マシンのアプリケーションの管理、共有、およびグローバル配布を簡素化する Azure Compute Gallery (旧称 Shared Image Gallery) のリソースの種類です。


> [!IMPORTANT]
> **Azure Compute Gallery の VM アプリケーション** は、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のものがあることを確認してください。


この記事では、既に Azure Compute Gallery があることを前提としています。 ギャラリーをまだ作成していない場合は、最初にギャラリーを作成します。 詳細については、「[リソースを格納および共有するためのギャラリーを作成する](create-gallery.md)」を参照してください。

アプリケーションを Azure Storage アカウントのコンテナーにアップロードしている必要があります。 アプリケーションはブロック BLOB またはページ BLOB に格納できます。 ページ BLOB を使用する場合は、ファイルをアップロードする前にバイトで揃える必要があります。 ファイルをバイトに揃えるサンプルを次に示します。

```azurepowershell-interactive
$inputFile = <the file you want to pad>

$fileInfo = Get-Item -Path $inputFile

$remainder = $fileInfo.Length % 512

if ($remainder -ne 0){

    $difference = 512 - $remainder

    $bytesToPad = [System.Byte[]]::CreateInstance([System.Byte], $difference)

    Add-Content -Path $inputFile -Value $bytesToPad -Encoding Byte
    }
```

ファイルが公開されていることを確認する必要があります。または、ストレージ アカウント内のファイルの SAS URI が必要です。 [Azure Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md) を使用すると、SAS URI をまだ作成していない場合は、迅速に作成できます。

## <a name="create-the-vm-application"></a>VM アプリケーションを作成する

VM アプリケーションの定義とバージョンを作成するには、次のオプションを選択します。

### <a name="portal"></a>[ポータル](#tab/portal)


1. [Azure portal](https://portal.azure.com) に移動し、**Azure Compute Gallery** を検索して選択します。
1. 一覧から使うギャラリーを選びます。
1. ギャラリーのページで、ページの上部にある **[追加]** を選択し、ドロップダウンから **[VM イメージ定義]** を選択します。 **[VM アプリケーション定義の作成]** ページが開きます。
1. **[基本]** タブで、アプリケーションの名前を入力し、Linux または Windows を実行している VM 用のアプリケーションであるかどうかを選択します。
1. VM アプリケーション定義に対して次のいずれかのオプション設定を指定する場合は、 **[発行オプション]** タブを選択します。
    - VM アプリケーション定義の説明。
    - 有効期限の終了日
    - 使用許諾契約書へのリンク
    - プライバシーに関する声明の URI
    - リリース ノートの URI
1. 完了したら、 **[確認および作成]** を選択します。
1. 確認が完了したら、 **[作成]** を選択して定義をデプロイします。
1. デプロイが完了したら、**[リソースに移動]** を選択します。


これで、VM を作成して、ポータルを使用して VM アプリケーションをデプロイできるようになりました。 通常どおりに VM を作成し、 **[詳細設定]** タブで **[Select a VM application to install]\(インストールする VM アプリケーションの選択\)** を選択します。

:::image type="content" source="media/vmapps/advanced-tab.png" alt-text="VM アプリケーションのインストールを選択で着い [詳細設定] タブのスクリーンショット。":::

一覧から VM アプリケーションを選択し、ページの下部にある **[保存]** を選択します。

:::image type="content" source="media/vmapps/select-app.png" alt-text="VM にインストールする VM アプリケーションの選択を示すスクリーンショット。":::

複数の VM アプリケーションがインストールされている場合は、 **[詳細設定]** タブで各 VM アプリケーションのインストール順序を設定し直すことができます。

### <a name="cli"></a>[CLI](#tab/cli)

VM アプリケーションには [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.30.0 以降が必要です。

[az sig gallery-application create](/cli/azure/sig/gallery-application#az_sig_gallery_application_create) を使用して VM アプリケーション定義を作成します。 この例では、Linux ベースの VM 用に *myApp* という名前の VM アプリケーション定義を作成します。

```azurecli-interactive
az sig gallery-application create \
    --application-name myApp \
    --gallery-name myGallery \
    --resource-group myResourceGroup \
    --os-type Linux \
    --location "East US"
```

[az sig gallery-application version create](/cli/azure/sig/gallery-application/version#az_sig_gallery_application_version_create) を使用して、VM アプリケーションのバージョンを作成します。 バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

パラメーターの値を独自の値に置き換えます。

```azurecli-interactive
az sig gallery-application version create \
   --version-name 1.0.0 \
   --application-name myApp \
   --gallery-name myGallery \
   --location "East US" \
   --resource-group myResourceGroup \
   --package-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>" \
   --install-command "mv myApp .\myApp\myApp" \
   --remove-command "rm .\myApp\myApp" \
   --update-command  "mv myApp .\myApp\myApp \
   --default-configuration-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>"\
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

`New-AzGalleryApplication` を使用して VM アプリケーション定義を作成します。 この例では、*myApp* という名前の Linux アプリを *myGallery* Azure Compute Gallery、*myGallery* リソース グループに作成します。自分で使用するために VM アプリケーションについての簡単な説明を付けました。 必要に応じて値を置き換えます。

```azurepowershell-interactive
$galleryName = myGallery
$rgName = myResourceGroup
$applicationName = myApp
New-AzGalleryApplication `
  -ResourceGroupName $rgName `
  -GalleryName $galleryName `
  -Name $applicationName `
  -SupportedOSType Linux `
  -Description "Backend Linux application for finance."
```

`New-AzGalleryApplicationVersion` を使用して、アプリケーションのバージョンを作成します。 バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、バージョン番号 *1.0.0* を作成しています。 必要に応じて、変数の値を置き換えます。

```azurepowershell-interactive
$version = 1.0.0
New-AzGalleryApplicationVersion `
   -ResourceGroupName $rgName `
   -GalleryName $galleryName `
   -GalleryApplicationName $applicationName `
   -Name $version `
   -PackageFileLink "https://<storage account name>.blob.core.windows.net/<containder name>/<filename>" `
   -Location "East US" `
   -Install myApp.exe /silent `
   -Remove myApp.exe /uninstall `
```


既存の VM にアプリケーションを追加するには、アプリケーションのバージョンを取得し、それを使用して VM アプリケーションのバージョン ID を取得します。 ID を使用して、アプリケーションを VM 構成に追加します。

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $rgname -Name myVM
$vmapp = Get-AzGalleryApplicationVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryname `
   -ApplicationName $applicationname `
   -Version $version

$vm = Add-AzVmGalleryApplication `
   -VM $vm `
   -Id $vmapp.Id

Update-AzVm -ResourceGroupName $rgname -VM $vm
```


### <a name="rest"></a>[REST](#tab/rest2)

アプリケーション定義を作成します。


```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>?api-version=2019-03-01

{
    "location": "West US",
    "name": "myApp",
    "properties": {
        "supportedOSType": "Windows | Linux",
        "endOfLifeDate": "2020-01-01"
    }
}

```

| フィールド名 | 説明 | 制限事項 |
|--|--|--|
| name | ギャラリー内の VM アプリケーションの一意の名前 | 最大長は 117 文字です。 許可されている文字は、大文字または小文字、数字、ハイフン (-)、ピリオド (.)、アンダースコア (_) です。 名前の末尾にピリオド (.) を使用することはできません。 |
| supportedOSType | これが Windows か Linux アプリケーションか | Windows または Linux: |
| endOfLifeDate | アプリケーションの将来の有効期間の終了日。 これは参照専用であり、適用されていないことに注意してください。 | 有効な未来の日付 |

VM アプリケーションのバージョンを作成します。

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>/versions/\<**versionName**\>?api-version=2019-03-01

{
  "location": "$location",
  "properties": {
    "publishingProfile": {
      "source": {
        "mediaLink": "$mediaLink",
        "defaultConfigurationLink": "$configLink"
      },
      "manageActions": {
        "install": "echo installed",
        "remove": "echo removed",
        "update": "echo update"
      },
      "targetRegions": [
        {
          "name": "$location1",
          "regionalReplicaCount": 1 
        },
        { "name": "$location1" }
      ]
    },
    "endofLifeDate": "datetime",
    "excludeFromLatest": "true | false"
  }
}

```

| フィールド名 | 説明 | 制限事項 |
|--|--|--|
| location | VM アプリケーションバージョンのソースの場所 | 有効な Azure リージョン |
| mediaLink | アプリケーションのバージョン パッケージが含まれている url | 有効な既存のストレージ url |
| defaultConfigurationLink | 省略可能。 既定の構成を含む url。デプロイ時にオーバーライドされる可能性があります。 | 有効な既存のストレージ url |
| インストール | アプリケーションをインストールするコマンド | 指定された OS の有効なコマンド |
| 削除 | アプリケーションを削除するコマンド | 指定された OS の有効なコマンド |
| 更新 | 省略可能。 アプリケーションを更新するコマンド。 指定されていない場合、更新プログラムが必要な場合は、古いバージョンが削除され、新しいバージョンがインストールされます。 | 指定された OS の有効なコマンド |
| targetRegions/name | レプリケート先のリージョンの名前 | Azure リージョンを検証する |
| targetRegions/regionalReplicaCount | 省略可能。 作成するリージョン内のレプリカの数。 既定値は 1 です。 | 1 から 3 までの整数 |
| endOfLifeDate | アプリケーション バージョンの将来の終了日。 これはお客様参照専用であり、適用されません。 | 有効な未来の日付 |
| excludeFromLatest | 指定した場合、このバージョンは最新版と見なされません。 | ｔrue または false |




VM アプリケーションのバージョンを VM に追加するには、VM で PUT を実行します。

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/virtualMachines/\<**VMName**\>?api-version=2019-03-01

{
  "properties": {
    "applicationProfile": {
      "galleryApplications": [
        {
          "order": 1,
          "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
          "configurationReference": "{path to configuration storage blob}"
        }
      ]
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


VM アプリケーションを均一スケール セットに適用するには、次の手順を実行します。

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/
virtualMachineScaleSets/\<**VMSSName**\>?api-version=2019-03-01

{
  "properties": {
    "virtualMachineProfile": {
      "applicationProfile": {
        "galleryApplications": [
          {
            "order": 1,
            "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
            "configurationReference": "{path to configuration storage blob}"
          }
        ]
      }
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


| フィールド名 | 説明 | 制限事項 |
|--|--|--|
| 順序 | 省略可能。 アプリケーションをデプロイする順序。 以下を参照してください。 | 整数の検証 |
| packageReferenceId | ギャラリー アプリケーションのバージョンを参照する | 有効なアプリケーション バージョン リファレンス |
| configurationReference | 省略可能。 このデプロイの構成を含むストレージ BLOB の完全な URL。 これにより、前に defaultConfiguration に指定された値がオーバーライドされます。 | 有効なストレージ BLOB リファレンス |

順序フィールドを使用して、アプリケーション間の依存関係を指定できます。 順序の規則は次のとおりです。

| ケース | インストールの意味 | 失敗の意味 |
|--|--|--|
| 順序が指定されていません | 順序付けられていないアプリケーションは、順序付けされたアプリケーションの後にインストールされます。 順序指定されていないアプリケーション間でのインストール順序は保証されません。 | 順序付けされていないアプリケーションや順序指定されていないアプリケーションのインストールエラーは、順序付けられていないアプリケーションのインストールに影響を与える可能性があります。 |
| 重複する順序値 | アプリケーションは、同じ順序の他のアプリケーションと比較して、任意の順序でインストールされます。 同じ順序のすべてのアプリケーションは、下位のアプリケーションの後にインストールされ、順序が高いアプリケーションの前にインストールされます。 | 低い順序の以前のアプリケーションをインストールできなかった場合、この順序のアプリケーションはインストールされません。 この順序のアプリケーションのインストールに失敗した場合、上位の順序のアプリケーションはインストールされません。 |
| 順序の増加 | アプリケーションは、順序が低いアプリケーションの後、順序が高いアプリケーションの前にインストールされます。 | 下位のアプリケーションをインストールできなかった場合、このアプリケーションはインストールされません。 このアプリケーションのインストールに失敗した場合、上位の順序のアプリケーションはインストールされません。 |

応答には、完全な VM モデルが含まれます。 関連する部分を次に示します。

```rest
{
  "name": "{vm name}",
  "id": "{vm id}",
  "type": "Microsoft.Compute/virtualMachines",
  "location": "{vm location}",
  "properties": {
    "applicationProfile": {
      "galleryApplications": ""
    },
    "provisioningState": "Updating"
  },
  "resources": [
    {
      "name": "VMAppExtension",
      "id": "{extension id}",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "centraluseuap",
      "properties": "@{autoUpgradeMinorVersion=True; forceUpdateTag=7c4223fc-f4ea-4179-ada8-c8a85a1399f5; provisioningState=Creating; publisher=Microsoft.CPlat.Core; type=VMApplicationManagerLinux; typeHandlerVersion=1.0; settings=}"
    }
  ]
}

```

VM アプリケーションがまだ VM にインストールされていない場合、値は空になります。 

---



## <a name="next-steps"></a>次の手順
[VM アプリケーション](vm-applications.md)の詳細について参照してください。