---
title: Azure Image Builder を使用して既存のイメージ バージョンから新しい VM イメージ バージョンを作成する
description: Linux で Azure Image Builder を使用して既存のイメージ バージョンから新しい VM イメージ バージョンを作成します。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: c0348e159b14e400e7787da7e1c04de375f96815
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437146"
---
# <a name="create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Linux で Azure Image Builder を使用して既存のイメージ バージョンから新しい VM イメージ バージョンを作成する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

この記事では、[Azure Compute Gallery](../shared-image-galleries.md) (旧称 Shared Image Gallery) で既存のイメージ バージョンを取得し、それを更新し、新しいイメージ バージョンとしてギャラリーに公開する方法について説明します。

サンプルの .json テンプレートを使用して、イメージを構成します。 使用する .json ファイルは、[helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) です。 


## <a name="register-the-features"></a>機能の登録
Azure Image Builder を使用するには、機能を登録する必要があります。

登録を確認します。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

登録されていない場合、次のコマンドを実行します。

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables-and-permissions"></a>変数とアクセス許可の設定

[イメージの作成と Azure Compute Gallery への配布](image-builder-gallery.md)に関するページを使用して Azure Compute Gallery を作成した場合は、必要とする変数のいくつかが既に作成されています。 それ以外の場合は、この例で使用するいくつかの変数を設定してください。


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the Azure Compute Gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

サブスクリプション ID の変数を作成します。

```console
subscriptionID=$(az account show --query id --output tsv)
```

更新するイメージ バージョンを取得します。

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o tsv)
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>ユーザー割り当て ID を作成し、リソース グループにアクセス許可を設定する
前の例でユーザー ID を設定したので、必要なのはそのリソース ID を取得することだけです。その後、それをテンプレートに追加します。

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

既に自身の Azure Compute Gallery があり、以前の例に従わなかった場合、Image Builder がリソース グループにアクセスできるようにアクセス許可を割り当てる必要があります。これによりギャラリーにアクセスできます。 [イメージを作成して Azure Compute Gallery に配布する方法](image-builder-gallery.md)に関する例の手順をご確認ください。


## <a name="modify-helloimage-example"></a>helloImage の例の変更
.json ファイル [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) と [Image Builder テンプレートのリファレンス](image-builder-json.md)を開いて、使用しようとしている例を確認できます。 


.json の例をダウンロードし、変数を使用して構成します。 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>イメージの作成

VM Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

イメージのビルドを開始します。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

イメージがビルドされてレプリケートされるまで待ってから、次の手順に進みます。


## <a name="create-the-vm"></a>VM の作成

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM のパブリック IP アドレスを使用して VM への SSH 接続を作成します。

```console
ssh azureuser@<pubIp>
```

SSH 接続が確立されるとすぐに、イメージが「当日のメッセージ」でカスタマイズされたことがわかります。

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

`exit` と入力して SSH 接続を閉じます。

ギャラリーで使用できるようになったイメージ バージョンを一覧表示することもできます。

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>次のステップ

この記事で使用されている .json ファイルのコンポーネントの詳細については、[Image Builder テンプレートのリファレンス](../linux/image-builder-json.md)に関するページを参照してください。
