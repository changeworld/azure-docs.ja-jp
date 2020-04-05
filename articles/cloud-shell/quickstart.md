---
title: Azure Cloud Shell のクイックスタート - Bash
description: Azure Cloud Shell でブラウザーで Bash コマンド ラインを使用する方法について説明します。
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458071"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Azure Cloud Shell の Bash のクイックスタート

このドキュメントでは、[Azure Portal](https://ms.portal.azure.com/) で Azure Cloud Shell の Bash を使う方法について詳しく説明します。

> [!NOTE]
> [Azure Cloud Shell の PowerShell](quickstart-powershell.md) のクイックスタートもあります。

## <a name="start-cloud-shell"></a>Cloud Shell の起動
1. Azure Portal 上部のナビゲーションから **Cloud Shell** を起動します。 <br>
![](media/quickstart/shell-icon.png)

2. ストレージ アカウントと Microsoft Azure ファイル共有を作成するためのサブスクリプションを選択します。
3. [Create storage]\(ストレージの作成\) を選択します。

> [!TIP]
> Azure CLI では、セッションごとにユーザー認証が自動的に行われます。

### <a name="select-the-bash-environment"></a>Bash 環境を選ぶ
シェル ウィンドウの左側にある環境ドロップダウンで [`Bash`] が選択されていることを確認します。 <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>サブスクリプションの設定
1. 自分が利用できるサブスクリプションを一覧表示します。
   ```azurecli-interactive
   az account list
   ```

2. 優先するサブスクリプションを設定します。 <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> 設定したサブスクリプションは、`/home/<user>/.azure/azureProfile.json` を使って今後のセッション用に記憶されます。

### <a name="create-a-resource-group"></a>リソース グループを作成する
"MyRG" という名前の新しいリソース グループを WestUS に作成します。
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux VM の作成
新しいリソース グループに Ubuntu VM を作成します。 Azure CLI によって SSH キーが作成され、キーが VM に設定されます。 <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> `--generate-ssh-keys` を使用すると、Azure CLI によって VM と `$Home` ディレクトリに公開キーと秘密キーが作成され、設定されます。 既定では、キーは Cloud Shell で `/home/<user>/.ssh/id_rsa` と `/home/<user>/.ssh/id_rsa.pub` に配置されます。 `.ssh` フォルダーは、`$Home` の永続化に使用される、接続したファイル共有の 5 GB イメージに永続化されます。

この VM でのユーザー名が、Cloud Shell で使用されるユーザー名になります ($User@Azure:)。

### <a name="ssh-into-your-linux-vm"></a>Linux VM への SSH 接続
1. Azure Portal の検索バーで VM 名を検索します。
2. [接続] をクリックして、VM 名とパブリック IP アドレスを取得します。 <br>
   ![](media/quickstart/sshcmd-copy.png)

3. `ssh` コマンドを使用して VM への SSH 接続を確立します。
   ```
   ssh username@ipaddress
   ```

SSH 接続を確立すると、Ubuntu のウェルカム プロンプトが表示されます。 <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>クリーンアップしています 
1. SSH セッションを終了します。
   ```
   exit
   ```

2. リソース グループとそこに含まれるリソースを削除します。
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>次のステップ
[Cloud Shell の Bash でのファイルの永続化については、こちらを参照してください](persisting-shell-storage.md) <br>
[Azure CLI について](https://docs.microsoft.com/cli/azure/) <br>
[Azure Files ストレージについて](../storage/files/storage-files-introduction.md) <br>
