---
title: Windows ブート マネージャー エラー - 0xC0000428 無効なイメージ ハッシュ状態
titlesuffix: Azure Virtual Machines
description: この記事では、プレビュー イメージを使用していたがその試用期間が終了したことで、Azure 仮想マシン (VM) を起動できなくなったという問題を解決するための手順について説明します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: e58e349d7b7385ec913986c39462c17deadcb61d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969604"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Windows ブート マネージャー エラー - 0xC0000428 無効なイメージ ハッシュ状態

この記事では、プレビュー イメージを使用していたがその試用期間が終了したことで、Azure 仮想マシン (VM) を起動できなくなったという問題を解決するための手順について説明します。

## <a name="symptom"></a>症状

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、スクリーンショットに次のメッセージを含む Windows Boot Manager が表示されることがわかります。

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![図 1 に Windows Boot Manager を示します。status が "Ox0000428"、info が "Windows がこのファイルのデジタル署名を確認できません" となっているのがわかります。](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

または、次のメッセージが表示されます。

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![図 2 に Windows Boot Manager を示します。status が "Ox0000428"、info が "このファイルのデジタル署名を確認できませんでした" となっているのがわかります。](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>原因

VM を構築するために使用されたイメージが、RTM (工場出荷版) イメージでなく、有効期限が設定されたプレビュー イメージでした。 

プレビュー イメージには指定されたライフサイクルがあり、有効期限が過ぎたとき、つまりイメージの試用期間が終了すると、このスクリーンショットが表示されます。

### <a name="example-of-preview-images"></a>プレビュー イメージの例

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

プレビュー イメージの有効期限を延長することはできません。 プレビューの有効期限が切れると、VM を起動することはできなくなります。

- 製品によっては、試用期間が異なる場合があります。 たとえば、Windows プレビュー イメージの試用期間は 180 日間です。

- Azure では、プレビュー バージョンである Windows のすべてのイメージの説明の中に、運用環境での試用を意図するものではなく、指定された試用期間のみの使用、または "プレビュー リリース" としてのみの使用が可能であるという注記が含まれています。

## <a name="solution"></a>解決策

ご利用のイメージがプレビュー イメージである場合は、使用するイメージの有効期限を延長する方法がないので、プレビュー以外のイメージを使用して[新しい VM をデプロイ](../windows/quick-create-portal.md)する必要があります。 次の手順を使用すれば、プレビュー イメージを使用していたかどうかを容易に特定できると共に、この VM から新しい VM にデータを転送する場合に役立つリソースが得られます。 イメージがプレビュー イメージであることを明確に識別しても、それが現在有効期限切れになっている場合、イメージの回復は不可能です。

好みに応じて、プレビュー イメージかどうかを特定するために、Azure PowerShell または Azure CLI を使用してイメージに対してクエリを実行することもできます。 以下のコマンドを使用すれば、イメージがプレビュー イメージであることを確認できます。

### <a name="query-using-azure-powershell"></a>Azure PowerShell を使用してクエリを実行する

1. Windows PowerShell アプリケーションを開きます。
1. 次のコマンドを実行します。

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- 上記のコマンドでは、`<LOCATION>`、`<PUBLISHER NAME>`、`<OFFER NAME>`、および `<YEAR WHEN THIS IMAGE WAS RELEASED>` を、示されている情報に置き換えます。 また、“<” と “>” の記号も削除します。

  次の例を参照してください。

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Azure CLI を使用したクエリ

1. [Azure CLI のインストール](/cli/azure/install-azure-cli)がまだである場合は、それを行う必要があります。
1. ダウンロードが完了したら、コマンド プロンプトまたは PowerShell を使用して `az login` コマンドを入力し、次にアカウントの資格情報でサインインします。
1. ログインが完了したら、次のコマンドを入力します。

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- 上記のコマンドでは、`<LOCATION>`、`<PUBLISHER NAME>`、`<OFFER NAME>`、および `<YEAR WHEN THIS IMAGE WAS RELEASED>` を、示されている情報に置き換えます。 また、“<” と “>” の記号も削除します。

  次の例を参照してください。

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```