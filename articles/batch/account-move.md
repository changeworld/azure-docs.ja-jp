---
title: Azure Batch アカウントを別のリージョンに移動する
description: Azure Batch アカウントを別のリージョンに移動する方法を確認します。
ms.topic: how-to
ms.date: 05/05/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 9c0e45b62f6dd6152ab80beaa751925702b1fa37
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108777073"
---
# <a name="move-an-azure-batch-account-to-another-region"></a>Azure Batch アカウントを別のリージョンに移動する

既存の [Batch アカウント](accounts.md)をリージョン間で移動することが役に立つ場合があるシナリオがあります。 たとえば、ディザスター リカバリー計画の一部として、別のリージョンに移動することが必要な場合があります。

Azure Batch アカウントをリージョン間で直接移動することはできません。 ただし、Azure Resource Manager テンプレートを使用して、Batch アカウントの既存の構成をエクスポートすることはできます。 その後、Batch アカウントをテンプレートにエクスポートすることで別のリージョンにリソースをステージし、移動先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。 その後、アカウント内でジョブやその他の機能を再作成できます。

 Resource Manager とテンプレートの詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。

このトピックでは、Azure portal を使用して、リージョン間で Batch アカウントを移動する方法について説明します。

## <a name="prerequisites"></a>前提条件

- 「[Azure Storage アカウントを別のリージョンに移動する](../storage/common/storage-account-move.md)」の手順に従って、Batch アカウントに関連付けられているストレージ アカウントを、新しいターゲット リージョンに移動します。 必要に応じて、ストレージ アカウントを元のリージョンに残すこともできますが、Batch アカウントと同じリージョンにあると通常はパフォーマンスが向上するため、移動することをお勧めします。 以下の手順は、ストレージ アカウントが既に移行されていることを想定しています。
- お使いの Batch アカウントで使用されるサービスと機能が、ターゲット リージョンでサポートされていることをご確認ください。

## <a name="prepare"></a>準備

作業を開始するには、Resource Manager テンプレートをエクスポートして変更する必要があります。

### <a name="export-a-template"></a>テンプレートをエクスポートする

最初に、お使いの Batch アカウントの設定と情報を含むテンプレートをエクスポートします。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[すべてのリソース]** を選択してから、Batch アカウントを選択します。

3. **[Automation]**  >  **[テンプレートのエクスポート]** を選択します。

4. **[テンプレートのエクスポート]** ブレードで **[ダウンロード]** を選択します。

5. ポータルからダウンロードした .zip ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。

   この zip ファイルには、テンプレートとテンプレートをデプロイするためのスクリプトから構成される .json ファイルが含まれています。

### <a name="modify-the-template"></a>テンプレートの変更

次に、ターゲット リージョンに新しい Batch アカウントを作成できるように、テンプレートを読み込んで変更します。

1. Azure Portal で、 **[リソースの作成]** を選択します。

1. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。

1. **[Template deployment (deploy using custom templates)]\(テンプレートのデプロイ (カスタム テンプレートを使用してデプロイ)\)** を選択します。

1. **［作成］** を選択します

1. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。

1. **[ファイルの読み込み]** を選択し、前のセクションでダウンロードした **template.json** ファイルを選択します。

1. アップロードした **template.json** ファイル内で、Batch アカウント名に対して新しい **defaultValue** を入力して、ターゲット Batch アカウントに名前を付けます。 この例では、Batch アカウント名の **defaultValue** を `mytargetaccount` に設定します。 また、**defaultValue** 内の文字列を `mytargetstorageaccount` のリソース ID に置き換えます。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "batchAccounts_mysourceaccount_name": {
                "defaultValue": "mytargetaccount",
                "type": "String"
            }
        },
   ```

1. 次に、移行したストレージ アカウントのリソース ID を使用して、ストレージ アカウントの **defaultValue** を更新します。 この値を取得するには、Azure portal 内でストレージ アカウントに移動し、画面の上部付近にある **[JSON View]\(JSON ビュー\)** を選択し、 **[リソース ID]** の下に表示されている値をコピーします。 この例では、リソース グループ `mytargetresourcegroup` 内の `mytargetstorageaccount` という名前のストレージ アカウントのリソース ID を使用します。

   ```json
           "storageAccounts_mysourcestorageaccount_externalid": {
            "defaultValue": "/subscriptions/{subscriptionID}/resourceGroups/mytargetresourcegroup/providers/Microsoft.Storage/storageAccounts/mytargetstorageaccount",
            "type": "String"
        }
    },
   ```

1. 最後に、ターゲット リージョンを使用するように **location** プロパティを編集します。 この例では、ターゲット リージョンを `centralus` に設定します。

```json
    {
        "resources": [
            {
                "type": "Microsoft.Batch/batchAccounts",
                "apiVersion": "2021-01-01",
                "name": "[parameters('batchAccounts_mysourceaccount_name')]",
                "location": "centralus",  
```

リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です。 たとえば **米国中部** = **centralus** です。

## <a name="move"></a>詳細ビュー

テンプレートをデプロイして、ターゲット リージョンに新しいス Batch アカウントを作成します。

1. 変更が完了したので、**template.json** ファイルの下の **[保存]** を選択します。

1. プロパティ値を入力または選択します。
   - **サブスクリプション**:Azure サブスクリプションを選択します。
   - **リソース グループ**: 関連するストレージ アカウントを移動するときに作成したリソース グループを選択します。
   - **リージョン**: アカウントの移動先 Azure リージョンを選択します。

1. **[確認と作成]** を選択し、次に **[作成]** を選択します。

### <a name="configure-the-new-batch-account"></a>新しい Batch アカウントを構成する

一部の機能はテンプレートにエクスポートされないため、新しい Batch アカウント内で再作成する必要があります。 これらには、次のものが含まれます。

- ジョブ
- ジョブ スケジュール
- 証明書
- アプリケーション パッケージ

これらを必要に応じて新しいアカウントで構成してください。 これらの機能をご自身のソース Batch アカウント内でどのように構成したかを、参考としてご確認いただくことができます。

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ

新しい Batch アカウントが新しいリージョンで正常に動作していることを確認し、必要な機能を復元したら、ソース Batch アカウントを削除できます。

Azure portal を使用して Batch アカウントを削除するには:

1. Azure portal で、左側のメニューを展開してサービスのメニューを展開し、 **[Batch サービス]** を選択します。

2. 削除する Batch アカウントを見つけて、一覧の右側にある **[詳細]** ボタン ( **...** ) を右クリックします。 これが作成した新しい Batch アカウントではなく、元のソース Batch アカウントであることを確認してください。

3. **[削除]** を選択して確認します。

## <a name="next-steps"></a>次のステップ

- [新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)について確認する。
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)方法を確認する。
