---
title: "Azure CLI を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する | Microsoft Docs"
description: "PowerShell を使用して Azure Policy の割り当てを作成し、準拠していないリソースを特定します。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 92b532691986e72eca68d9bc3033e20ff8ffef3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Azure CLI を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する

Azure のコンプライアンスを理解する第一歩は、現在のリソースの状態を把握することです。 このクイックスタートでは、ポリシー割り当てを作成して、ポリシー定義の *[Require SQL Server version 12.0]\(SQL Server バージョン 12.0 が必要\)* に準拠していないリソースを特定するプロセスについて順を追って説明します。 このプロセスを終了すると、異なるバージョンや本質的に準拠していないサーバーを適切に特定できるようになります。

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する方法について詳しく説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。
 
## <a name="opt-in-to-azure-policy"></a>Azure Policy を選択する

Azure Policy は現在、制限付きのプレビュー段階です。そのため、登録してアクセスを依頼する必要があります。

1. Azure Policy (https://aka.ms/getpolicy) にアクセスし、左側のウィンドウにある **[サインアップ]** を選択します。

   ![ポリシーの検索](media/assign-policy-definition/sign-up.png)

2. **[サブスクリプション]** 一覧から、使用したいサブスクリプションを選択して Azure Policy を選択します。 次に、**[登録]** を選択します。

   ![Azure Policy の使用を選択する](media/assign-policy-definition/preview-opt-in.png)

   提出された登録依頼が承認されるまでに数日かかる場合があります。 依頼が承認されると、サービスの利用を開始できることが電子メールで通知されます。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシー割り当てを作成し、[Require SQL Server version 12.0]\(SQL Server バージョン 12.0 が必要\) 定義を割り当てます。 このポリシー定義で、ポリシー定義に設定されている条件に準拠していないリソースを特定します。

次の手順で新しいポリシー割り当てを作成します。

すべてのポリシー定義を表示し、[Require SQL Server Version 12.0]\(SQL Server バージョン 12.0 が必要\) ポリシー定義を見つけます。

```azurecli
az policy definition list
```

Azure Policy には、使用できる組み込みのポリシー定義があらかじめ含まれています。 次のような組み込みのポリシー定義が表示されます。

- Enforce tag and its value (タグとその値を強制する)
- Apply tag and its value (タグとその値を適用する)
- Require SQL Server Version 12.0 (SQL Server バージョン 12.0 が必要)

次に、以下の情報を入力し、次のコマンドを実行してポリシー定義を割り当てます。

- ポリシー割り当ての表示用の**名前**。 この例では、「*SQL Server バージョン 12.0 が必要の割り当て*」と入力してみましょう。
- **ポリシー** - 割り当ての作成に使用している基のポリシー定義です。 この例では、*[Require SQL Server Version 12.0]\(SQL Server バージョン 12.0 が必要\)* ポリシー定義です。
- **スコープ** - スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループの範囲が含まれる可能性があります。

  Azure Policy を選択したときに登録したサブスクリプション (またはリソース グループ) を使用します。この例では、サブスクリプション ID **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** とリソース グループ名 **FabrikamOMS** を使用しています。 これらの値は、実際に使用するサブスクリプションの ID とリソース グループの名前に変更してください。 

コマンドは次のようになります。

```azurecli
az policy assignment create --name Require SQL Server version 12.0 Assignment --policy Require SQL Server version 12.0 --scope /subscriptions/ 
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

ポリシー割り当ては、特定のスコープ内で実行するように割り当てられたポリシーです。 このスコープは、管理グループからリソース グループの範囲になる可能性があります。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

この新しい割り当てに準拠していないリソースを表示するには:

1. Azure Policy ページに戻ります。
2. 左側のウィンドウで **[Compliance]\(コンプライアンス\)** を選択し、作成した**ポリシー割り当て**を検索します。

   ![ポリシーのコンプライアンス](media/assign-policy-definition/policy-compliance.png)

   この新しい割り当てに準拠していない既存のリソースがある場合、上図のように **[Non-compliant resources]\(準拠していないリソース\)** タブに表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のガイドは、このクイックスタートに基づいています。 引き続きチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 続行する予定がない場合は、次のコマンドを実行して、作成した割り当てを削除してください。

```azurecli
az policy assignment delete –name Require SQL Server version 12.0 Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

ポリシーの割り当てについて詳しく学び、**今後**作成されるリソースが準拠していることを確認するには、チュートリアルを続行してください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./create-manage-policy.md)

