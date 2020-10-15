---
title: Azure Key Vault アクセス ポリシーを割り当てる (ポータル)
description: Azure portal を使用して、サービス プリンシパルまたはアプリケーション ID に Key Vault アクセス ポリシーを割り当てる方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: d16d14ff51cb9a7e71193d93c6755a8e4cb93ed0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "89481411"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Azure portal を使用して Key Vault アクセス ポリシーを割り当てる

Key Vault アクセス ポリシーは、特定のサービス プリンシパル (アプリケーションまたはユーザー グループ) が、Key Vault の[シークレット](../secrets/index.yml)、[キー](../keys/index.yml)、および[証明書](../certificates/index.yml)に対して、さまざまな操作を実行できるかどうかを決定します。 アクセス ポリシーは、Azure portal (この記事)、[Azure CLI](assign-access-policy-cli.md)、または [Azure PowerShell](assign-access-policy-powershell.md) を使用して割り当てることができます。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure Active Directory での Azure portal を通じたグループの作成の詳細については、[基本グループの作成とメンバーの追加](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)に関する記事を参照してください。

## <a name="assign-an-access-policy"></a>アクセス ポリシーを割り当てる

1.  [Azure portal](https://portal.azure.com) で、Key Vault リソースに移動します。 

1.  **[設定]** で **[アクセス ポリシー]** を選択し、 **[アクセス ポリシーの追加]** を選択します。

    ![[アクセス ポリシー] を選択し、[ロール割り当ての追加] を選択しています](../media/authentication/assign-policy-portal-01.png)

1.  **[証明書のアクセス許可]** 、 **[キーのアクセス許可]** 、および **[シークレットのアクセス許可]** でアクセス許可を選択します。 一般的なアクセス許可の組み合わせが含まれるテンプレートを選択することもできます。

    ![アクセス ポリシーのアクセス許可を指定しています](../media/authentication/assign-policy-portal-02.png)

1. **[プリンシパルの選択]** で **[選択なし]** リンクを選択すると、 **[プリンシパル]** 選択ウィンドウが開きます。 検索フィールドにアプリまたはサービス プリンシパルの名前を入力し、適切な結果を選択したら、 **[選択]** を選択します。

    ![アクセス ポリシーのサービス プリンシパルを選択しています](../media/authentication/assign-policy-portal-03.png)

    アプリにマネージド ID を使用している場合は、アプリ自体の名前を検索して選択します。 (マネージド ID とサービス プリンシパルの詳細については、[Key Vault 認証におけるアプリ ID とサービス プリンシパル](authentication.md#app-identity-and-security-principals)に関する記事を参照してください。)
 
1.  **[アクセス ポリシーの追加]** ウィンドウに戻り、 **[追加]** を選択して、アクセス ポリシーを保存します。

    ![割り当てられたサービス プリンシパルを使用してアクセス ポリシーを追加する](../media/authentication/assign-policy-portal-04.png)

1. **[アクセス ポリシー]** ページに戻り、アクセス ポリシーが **[現在のアクセス ポリシー]** に表示されていることを確認したら、 **[保存]** を選択します。 アクセス ポリシーは、保存するまで適用されません。

    ![アクセス ポリシーの変更の保存](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>次のステップ

- [Azure Key Vault セキュリティ: ID 管理とアクセス管理](overview-security.md#identity-and-access-management)
- [キー コンテナーをセキュリティで保護する](secure-your-key-vault.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
- [Azure Key Vault のベスト プラクティス](best-practices.md)
