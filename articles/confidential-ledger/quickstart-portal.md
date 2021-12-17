---
title: クイックスタート – Microsoft Azure Confidential Ledger と Azure portal
description: Azure portal で Microsoft Azure Confidential Ledger を使用する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/18/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 265dfc023d0adf384eb483efc16a40380e395fb3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458154"
---
# <a name="quickstart-create-a-confidential-ledger-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して Confidential Ledger を作成する

Azure Confidential Ledger は、データを安全に保持する必要がある機密のデータ ログとレコードに対して高い整合性ストアを提供するクラウド サービスです。 Azure Confidential Ledger の詳細と、Confidential Ledger に格納できるものの例については、[Microsoft Azure Confidential Ledger の概要](overview.md)に関する記事を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

このクイックスタートでは、[Azure portal](https://portal.azure.com) を使用して Confidential Ledger を作成します。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-confidential-ledger"></a>Confidential Ledger を作成する

1. Azure portal メニューまたは [ホーム] ページで、 **[リソースの作成]** を選択します。

1. [検索] ボックスに "Confidential Ledger" と入力します。

1. 結果の一覧から **[Confidential Ledger]** を選択します。

1. [Confidential Ledger] セクションで、 **[作成]** を選択します。

1. [Create confidential ledger]\(Confidential Ledger の作成\) セクションで、次の情報を入力します。
    - **名前**: Confidential Ledger に一意の名前を指定します。
    - **サブスクリプション**:サブスクリプションを選択します。
    - **リソース グループ**: **[新規作成]** * を選択し、リソース グループの名前を入力します。
    - **場所**: プルダウン メニューで場所を選択します。
    - 他のオプションは既定値のままにしておきます。
   
1. **[セキュリティ]** タブをクリックします。

1. 次に、"管理者" ロールを持つ Azure AD ベースまたは証明書ベースのユーザーを Confidential Ledger に追加する必要があります。 このクイックスタートでは、Azure AD ベースのユーザーを追加します。 **[+ Add AAD-Based User]\(+ AAD ベースのユーザーの追加\)** を選択します。

1. Azure AD ベースまたは証明書ベースのユーザーを追加する必要があります。 右側のウィンドウでメール アドレスを検索します。 該当の行を選択し、ウィンドウの下部にある **[選択]** を選択します。

1. **[Ledger Role]\(Ledger ロール\)** のドロップダウン フィールドで、 **[管理者]** を選択します。

1. **[確認および作成]** を選択します。 検証に合格したら、 **[作成]** を選択します。

デプロイが完了したら、 **[リソースに移動]** を選択します。

:::image type="content" source="./media/confidential-ledger-portal-quickstart.png" alt-text="ACL ポータルの作成画面":::

次の 2 つのプロパティをメモしておきます。
- **Confidential Ledger 名**: この例では、"test-create-ledger-demo" です。 この名前は他の手順で使用します。
- **Ledger エンドポイント**: この例では、このエンドポイントは `https://test-create-ledger-demo.confidential-ledger.azure.net/` です。 

これらのプロパティ名は、データ プレーンから Confidential Ledger を処理するために必要になります。
 
## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Confidential Ledger に関するその他の記事は、このクイックスタートに基づいています。 後続の記事に引き続き取り組む場合は、これらのリソースをそのまま残しておくことをお勧めします。 

不要になったら、リソース グループを削除します。これにより、Confidential Ledger と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1.  ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。

1.  **[リソース グループの削除]** を選択します。

1.  **[リソース グループ名を入力してください]** ボックスにリソース グループの名前を入力して **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure portal を使用して Confidential Ledger を作成しました。 Azure Confidential Ledger およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Microsoft Azure Confidential Ledger の概要](overview.md)
