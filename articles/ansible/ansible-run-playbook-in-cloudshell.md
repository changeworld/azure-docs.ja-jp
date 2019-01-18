---
title: Azure Cloud Shell での Bash を使用した Ansible の実行
description: Azure Cloud Shell で Bash を使用してさまざまな Ansible タスクを実行する方法について説明します
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, プレイブック, Bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: quickstart
ms.openlocfilehash: 61b23b5bc2620c82051b0ba1de4bb54b44a495e0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359189"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Azure Cloud Shell での Bash を使用した Ansible の実行

このチュートリアルでは、Cloud Shell 内で Bash を使用して、Ansible ワークスペースとして Azure サブスクリプションを構成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

- **Azure Cloud Shell の構成** - Azure Cloud Shell を初めて使う場合は、Cloud Shell を起動および構成する方法について、「[Azure Cloud Shell の Bash のクイックスタート](https://docs.microsoft.com/azure/cloud-shell/quickstart)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>資格情報の自動構成

Cloud Shell にサインインするとき、Ansible は Azure で認証され、追加構成なしでインフラストラクチャを管理できます。 複数のサブスクリプションを持っている場合は、`AZURE_SUBSCRIPTION_ID` 環境変数をエクスポートすることにより、Ansible で使用するサブスクリプションを選択できます。 すべての Azure サブスクリプションを一覧表示するには、次のコマンドを実行します。

```azurecli-interactive
az account list
```

使用するサブスクリプションの **id** を使用して、**AZURE_SUBSCRIPTION_ID** を次のように設定します。

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>構成を確認する
正しく構成されたことを確認するには、Ansible を使用してリソース グループを作成します。

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Ansible を使用して Azure で基本的な仮想マシンを作成する](/azure/virtual-machines/linux/ansible-create-vm)