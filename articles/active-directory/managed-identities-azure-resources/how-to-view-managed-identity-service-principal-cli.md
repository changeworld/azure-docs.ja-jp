---
title: マネージド ID のサービス プリンシパルを表示する - Azure CLI - Azure AD
description: Azure CLI を使用してマネージド ID のサービス プリンシパルを表示するための段階的手順。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c0f167a36ceff6a9b62e2a3ccf24ca6e13e6294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611913"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Azure CLI を使用してマネージド ID のサービス プリンシパルを表示する

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure CLI を使用してマネージド ID のサービス プリンシパルを表示する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料アカウントを新規登録](https://azure.microsoft.com/free/)してください。
- [仮想マシン](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)または[アプリケーション](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)でシステム割り当ての ID を有効にします。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する。これは、コード ブロックの右上隅にある **[Try It]\(試してみる\)** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) をインストールしてスクリプトをローカルで実行した後、[az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインする。 リソースを作成する Azure サブスクリプションに関連付けられているアカウントを使用します。   

## <a name="view-the-service-principal"></a>サービス プリンシパルを表示する

次のコマンドは、マネージド ID を有効にして VM またはアプリケーションのサービス プリンシパルを表示する方法を示しています。 `<Azure resource name>` を独自の値に置き換えます。

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>次のステップ

Azure CLI を使用して Azure AD サービス プリンシパルを管理する方法の詳細については、[az ad sp](/cli/azure/ad/sp) を参照してください。
