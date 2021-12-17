---
title: キー コンテナーにシークレットを格納する
description: Azure Key Vault にシークレットを格納し、仮想マシン、数式、または環境の作成時にそれらを使用する方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2c1849b6bf2ab5876fcbe0960df9bb8a7004bb78
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398100"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Azure DevTest Labs でキー コンテナーにシークレットを格納する
Azure DevTest Labs を使用するときに、複雑なシークレットを入力しなければならない場合があります。 シークレットの例としては、Windows VM のパスワード、Linux VM のパブリック SSH キー、成果物を通じて Git リポジトリをクローンするための個人用アクセス トークンがあります。 通常、シークレットは長いランダムな文字列です。 シークレットの入力は面倒で、特に同じシークレットを複数回使用する場合は注意が必要です。

この問題を解決してシークレットを安全な場所に保管するために、DevTest Labs では [Azure Key Vault](../key-vault/general/overview.md) へのシークレットの格納をサポートしています。 ユーザーが初めてシークレットを保存するとき、DevTest Labs がラボと同じリソース グループに自動的にキー コンテナーを作成して、そこにシークレットを格納します。 DevTest Labs では、ユーザーごとに個別のキー コンテナーが作成されます。 

キー コンテナーにシークレットを作成するには、ラボ ユーザーが事前にラボ仮想マシンを作成しておく必要があります。 この要件は、DevTest Labs がラボ ユーザーを有効なユーザー ドキュメントに関連付ける必要があるためです。 その後、ユーザーが各自のキー コンテナーにシークレットを作成して格納できるようになります。


## <a name="save-a-secret-in-azure-key-vault"></a>Azure Key Vault にシークレットを保存する
Azure Key Vault にシークレットを保存するには、次の手順を実行します。

1. 左側のメニューの **[自分のシークレット]** を選択します。
2. シークレットの **名前** を入力します。 仮想マシン、数式、または環境の作成時に、この名前がドロップダウン リストに表示されます。 
3. **値** としてシークレットを入力します。

    ![シークレットの格納を示すスクリーンショット。](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Azure Key Vault からシークレットを使用する
仮想マシン、数式、または環境を作成するためにシークレットを入力する際は、シークレットを手動で入力するか、保存したシークレットをキー コンテナーから選択できます。 キー コンテナーに格納されたシークレットを使用するには、次の操作を実行します。

1. **[保存されているシークレットを使用する]** を選択します。 
2. **[シークレットの選択]** のドロップダウン リストからシークレットを選択します。 

    ![VM の作成時におけるシークレットの使用を示すスクリーンショット。](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートでシークレットを使用する
次の例に示すように、仮想マシンの作成に使用する Azure Resource Manager テンプレートでシークレット名を指定できます。

![数式または環境におけるシークレットの使用を示すスクリーンショット。](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>次のステップ

- [Create a VM using the secret](devtest-lab-add-vm.md) (シークレットを使用して VM を作成する) 
- [Create a formula using the secret](devtest-lab-manage-formulas.md) (シークレットを使用して数式を作成する)
- [Create an environment using the secret](devtest-lab-create-environment-from-arm.md) (シークレットを使用して環境を作成する)
