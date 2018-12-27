---
title: Azure DevTest Labs でキー コンテナーにシークレットを格納する | Microsoft Docs
description: Azure Key Vault にシークレットを格納し、仮想マシン、数式、または環境の作成時にそれらを使用する方法について説明します。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: d87c8a46459a9b4bf80bef895ec97e436d38e699
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186834"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Azure DevTest Labs でキー コンテナーにシークレットを格納する
Azure DevTest Labs の使用時には複雑なシークレット (Windows 仮想マシンのパスワード、Linux 仮想マシンの SSH 公開キー、または成果物を使用して Git リポジトリを複製するための個人用アクセス トークン) の入力が必要な場合があります。 通常、シークレットは長いランダムな文字列です。 そのため、シークレットの入力は面倒で、特に同じシークレットを複数回使用する場合は注意が必要です。

この問題を解決してシークレットを安全な場所に保管するために、DevTest Labs では [Azure Key Vault](../key-vault/key-vault-overview.md) へのシークレットの格納をサポートしています。 ユーザーが初めてシークレットを保存する際に、DevTest Labs サービスは、ラボが格納されているのと同じリソース グループ内にキー コンテナーを自動的に作成してシークレットを保存します。 DevTest Labs では、ユーザーごとに個別のキー コンテナーが作成されます。 

## <a name="save-a-secret-in-azure-key-vault"></a>Azure Key Vault にシークレットを保存する
Azure Key Vault にシークレットを保存するには、次の手順を実行します。

1. 左側のメニューの **[自分のシークレット]** を選択します。
2. シークレットの**名前**を入力します。 仮想マシン、数式、または環境の作成時に、この名前がドロップダウン リストに表示されます。 
3. **値**としてシークレットを入力します。

    ![シークレットの格納](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Azure Key Vault からシークレットを使用する
仮想マシン、数式、または環境を作成するためにシークレットの入力が必要な場合は、シークレットを手動で入力するか、保存したシークレットをキー コンテナーから選択できます。 キー コンテナーに格納されたシークレットを使用するには、次の操作を実行します。

1. **[保存されているシークレットを使用する]** を選択します。 
2. **[シークレットの選択]** のドロップダウン リストからシークレットを選択します。 

    ![仮想マシンでのシークレットの使用](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートでシークレットを使用する
次の例に示すように、仮想マシンの作成に使用する Azure Resource Manager テンプレートでシークレット名を指定できます。

![数式または環境でのシークレットの使用](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>次の手順

- [Create a VM using the secret](devtest-lab-add-vm.md) (シークレットを使用して VM を作成する) 
- [Create a formula using the secret](devtest-lab-manage-formulas.md) (シークレットを使用して数式を作成する)
- [Create an environment using the secret](devtest-lab-create-environment-from-arm.md) (シークレットを使用して環境を作成する)
