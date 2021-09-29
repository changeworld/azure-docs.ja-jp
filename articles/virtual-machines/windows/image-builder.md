---
title: Azure Image Builder で Windows VM を作成する
description: Azure Image Builder で Windows VM を作成します。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/23/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 83deba3d6fe63f5a63bda04556b911168527b065
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553063"
---
# <a name="create-a-windows-vm-with-azure-image-builder"></a>Azure Image Builder で Windows VM を作成する

**適用対象:** :heavy_check_mark: Windows VM 

この記事では、Azure VM Image Builder を使用して、カスタマイズされた Windows イメージを作成する方法について説明します。 この記事の例では、イメージのカスタマイズに[カスタマイザー](../linux/image-builder-json.md#properties-customize)を使用します。
- PowerShell (ScriptUri) - [PowerShell スクリプト](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)をダウンロードし、実行します。
- Windows の再起動 - VM を再起動します。
- PowerShell (インライン) - 特定のコマンドを実行します。 この例では、`mkdir c:\\buildActions` を使用して、VM 上にディレクトリを作成します。
- ファイル - GitHub から VM 上にファイルをコピーします。 この例では、[index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) を VM 上の `c:\buildArtifacts\index.html` にコピーします。
- buildTimeoutInMinutes - ビルドの実行時間を長くできるようにビルド時間を増やします。既定値は 240 分で、実行時間の長いビルド用にビルド時間を長くすることができます。
- vmProfile - vmSize プロパティと Network プロパティを指定します
- osDiskSizeGB - イメージのサイズを増やすことができます
- identity - ビルド中に使用するために Azure Image Builder の ID を提供します


`buildTimeoutInMinutes` を指定することもできます。 既定値は 240 分で、実行時間の長いビルド用にビルド時間を長くすることができます。 許容される最小値は 6 分です。これより小さい値を指定すると、エラーが発生します。

サンプルの .json テンプレートを使用して、イメージを構成します。 使用する .json ファイルは、[helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json) です。 



> [!NOTE]
> Windows ユーザーの場合、以下の Azure CLI の例は、Bash を使用して [Azure Cloud Shell](https://shell.azure.com) で実行できます。


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


## <a name="set-variables"></a>変数の設定

いくつかの情報を繰り返し使用するので、その情報を格納するいくつかの変数を作成します。


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

サブスクリプション ID の変数を作成します。

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```
## <a name="create-a-resource-group"></a>リソース グループを作成する
このリソース グループは、イメージ構成テンプレート成果物およびイメージを格納するために使用されます。


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>ユーザー割り当て ID を作成し、リソース グループにアクセス許可を設定する
Image Builder は、指定された[ユーザー ID](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) を使用して、リソース グループにイメージを挿入します。 この例では、イメージの配布を実行するためのきめ細かなアクションを含む Azure ロール定義を作成します。 このロール定義はその後、ユーザー ID に割り当てられます。

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>ユーザー割り当てマネージド ID を作成し、アクセス許可を付与する 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>イメージ構成テンプレートの例をダウンロードする

パラメーター化されたイメージ構成テンプレートが作成されており、試行できます。 .json ファイルの例をダウンロードし、前に設定した変数を使用して構成します。

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

この例は、ターミナルで `vi` などのテキスト エディターを使用して変更できます。

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> ソース イメージの場合、必ず[バージョンを指定](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)する必要があり、`latest` は使用できません。
> イメージの配布先となるリソース グループを追加または変更する場合、リソース グループに対して[アクセス許可が設定されていること](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group)を確認する必要があります。
 
## <a name="create-the-image"></a>イメージの作成

VM Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

完了すると成功メッセージがコンソールに返され、`$imageResourceGroup` に `Image Builder Configuration Template` が作成されます。 [非表示の型の表示] を有効にすると、Azure portal でリソース グループにこのリソースが表示されます。

また、バックグラウンドでは、Image Builder により、サブスクリプションにステージング リソース グループが作成されます。 このリソース グループがイメージのビルドに使用されます。 形式は次のとおりです: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> ステージング リソース グループは直接削除しないでください。 最初にイメージ テンプレート成果物を削除します。これにより、ステージング リソース グループが削除されます。

イメージ構成テンプレートの送信中にサービスによって障害が報告された場合、次を実行します。
-  [トラブルシューティング](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors)の手順を確認します。 
- 送信を再試行する前に、次のスニペットを使用してテンプレートを削除する必要があります。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

## <a name="start-the-image-build"></a>イメージのビルドを開始する
[az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) を使用してイメージのビルド プロセスを開始します。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

ビルドが完了するまで待ちます。 これには約 15 分かかります。

エラーが発生した場合は、これらの[トラブルシューティング](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors)の手順を確認してください。


## <a name="create-the-vm"></a>VM の作成

ビルドしたイメージを使用して VM を作成します。 *\<password>* を VM 上の `aibuser` の自身のパスワードに置き換えます。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>カスタマイズの確認

VM を作成したときに設定したユーザー名とパスワードを使用して、VM へのリモート デスクトップ接続を作成します。 VM 内で、コマンド プロンプトを開き、次のように入力します。

```console
dir c:\
```

イメージのカスタマイズ中に作成された次の 2 つのディレクトリが表示されます。
- buildActions
- buildArtifacts

## <a name="clean-up"></a>クリーンアップ

完了したら、リソースを削除します。

### <a name="delete-the-image-builder-template"></a>Image Builder テンプレートを削除する

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>ロールの割り当て、ロールの定義、ユーザー ID を削除します。
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>イメージ リソース グループを削除する

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>次のステップ

この記事で使用されている .json ファイルのコンポーネントの詳細については、[Image Builder テンプレートのリファレンス](../linux/image-builder-json.md)に関するページを参照してください。
