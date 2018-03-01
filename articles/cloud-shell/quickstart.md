---
title: "Azure Cloud Shell の Bash のクイックスタート | Microsoft Docs"
description: "Cloud Shell の Bash のクイックスタート"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 69431979769a03b62a7f9fd7760e6eb614e37cd6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
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
> Azure CLI 2.0 では、ユーザーの認証が各セッションで自動的に行われます。

### <a name="select-the-bash-environment"></a>Bash 環境を選ぶ
1. シェル ウィンドウの左側で環境ドロップダウンを選びます <br>
![](media/quickstart/env-selector.png)
2. Bash を選びます

### <a name="set-your-subscription"></a>サブスクリプションの設定
1. 自分が利用できるサブスクリプションを一覧表示します。 <br>
`az account list`
2. 優先するサブスクリプションを設定します。 <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> 設定したサブスクリプションは、`/home/<user>/.azure/azureProfile.json` を使って今後のセッション用に記憶されます。

### <a name="create-a-resource-group"></a>リソース グループの作成
"MyRG" という名前の新しいリソース グループを WestUS に作成します。 <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Linux VM の作成
新しいリソース グループに Ubuntu VM を作成します。 Azure CLI 2.0 によって SSH キーが作成され、そのキーを使って VM が設定されます。 <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> VM の認証に使用される公開キーと秘密キーは、Azure CLI 2.0 によって既定で `/home/<user>/.ssh/id_rsa` と `/home/<user>/.ssh/id_rsa.pub` に格納されます。 .ssh フォルダーは、接続した Azure ファイル共有の 5 GB イメージに永続化されます。

この VM でのユーザー名が、Cloud Shell で使用されるユーザー名になります ($User@Azure:)。

### <a name="ssh-into-your-linux-vm"></a>Linux VM への SSH 接続
1. Azure Portal の検索バーで VM 名を検索します。
2. [接続] をクリックし、`ssh username@ipaddress` を実行します。

![](media/quickstart/sshcmd-copy.png)

SSH 接続を確立すると、Ubuntu のウェルカム プロンプトが表示されます。 <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>クリーンアップしています 
リソース グループとそこに含まれるリソースを削除するには、 <br>
`az group delete -n MyRG` を実行します。

## <a name="next-steps"></a>次の手順
[Cloud Shell の Bash でのファイルの永続化については、こちらを参照してください](persisting-shell-storage.md) <br>
[Azure CLI 2.0 について](https://docs.microsoft.com/cli/azure/) <br>
[Azure Files ストレージについて](../storage/files/storage-files-introduction.md) <br>
