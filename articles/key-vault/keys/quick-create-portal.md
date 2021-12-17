---
title: Azure クイック スタート - Azure portal を使用して Key Vault との間でキーの設定と取得を行う | Microsoft Docs
description: Azure portal を使用して Azure Key Vault との間でキーの設定と取得を行う方法を紹介するクイック スタート
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: f7db6c26dbc80e104bd9fbd85468b54fc4877dec
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217822"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure Key Vault との間でキーの設定と取得を行う

Azure Key Vault は、シークレットのセキュリティで保護されたストアを提供するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Azure Key Vault は、Azure Portal を使用して作成および管理できます。 このクイック スタートでは、キー コンテナーを作成し、それを使用してキーを格納します。 Key Vault の詳細については、[概要](../general/overview.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure Key Vault にアクセスするには、Azure サブスクリプションが必要です。 まだサブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

シークレットへのアクセスはすべて、Azure Key Vault 経由で行われます。 このクイックスタートでは、[Azure portal](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md)、または [Azure PowerShell](../general/quick-create-powershell.md) を使用してキー コンテナーを作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="add-a-key-to-key-vault"></a>Key Vault にキーを追加する

キーをコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 この例では、アプリケーションが使用できるキーを追加します。 このキーは **ExampleKey** と呼ばれます。

1. Key Vault のプロパティ ページで、 **[キー]** を選択します。
2. **[Generate/Import]\(生成/インポート\)** をクリックします。
3. **[キーの作成]** 画面で、次の値を選択します。
    - **オプション**: 生成。
    - **Name**:ExampleKey。
    - 他の値は既定値のままにしておきます。 **Create** をクリックしてください。

キーが正常に作成されたことを示すメッセージが表示されたら、一覧でそのシークレットをクリックできます。 すると、いくつかのプロパティを確認できます。 

:::image type="content" source="../media/keys/quick-create-portal/current-version-hidden.png" alt-text="キーのプロパティ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Key Vault に関する他のクイック スタートとチュートリアルは、このクイック スタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
不要になったら、リソース グループを削除します。これにより、Key Vault と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成してキーを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Key Vault のセキュリティの概要](../general/security-features.md)を確認する