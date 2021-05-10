---
title: マネージド ID に関する既知の問題 - Azure Active Directory
description: Azure リソースのマネージド ID に関する既知の問題。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226924"
---
# <a name="known-issues-with-managed-identities"></a>マネージド ID に関する既知の問題

この記事では、マネージドID に関するいくつかの問題とその対処方法について説明します。 マネージド ID に関してよく寄せられる質問については、[よく寄せられる質問](managed-identities-faq.md)に関する記事をご覧ください。
## <a name="vm-fails-to-start-after-being-moved"></a>移動した後に VM を開始できない 

実行状態の VM をリソース グループまたはサブスクリプションから移動すると、移動中も引き続き実行されます。 ただし、移動後に、VM を停止および再起動すると、VM を開始できなくなります。 この問題は、VM が Azure リソースのマネージド ID への参照を更新できず、以前のリソース グループでポイントし続けるために発生します。

**回避策** 
 
Azure リソースのマネージド ID の正しい値を取得できるように、VM 上で更新をトリガーします。 VM プロパティの変更を行って、Azure リソース ID のマネージド ID への参照を更新できます。 たとえば、次のコマンドを使用して、VM で新しいタグの値を設定できます。

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
このコマンドは、新しいタグ "fixVM" を値 1 で VM に設定します。 
 
このプロパティの設定によって、VM は Azure リソース URI の正しいマネージド ID で更新され、VM を開始できるようになります。 
 
VM が開始されると、次のコマンドを使用してタグを削除できます。

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Azure AD ディレクトリ間のサブスクリプションの転送

マネージド ID は、サブスクリプションが別のディレクトリに移動/転送されたときに更新されません。 その結果、既存のシステム割り当てマネージド ID やユーザー割り当てマネージド ID は破損します。 

別のディレクトリに移動されたサブスクリプションのマネージド ID の回避策:

 - システム割り当てマネージドID の場合、無効にしてから最有効化します。 
 - ユーザー割り当てマネージド ID の場合、削除、再作成の後、必要なリソース (例： 仮想マシン) へ再度アタッチします

詳細については、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

[マネージド ID をサポートするサービス](services-support-managed-identities.md)と[よく寄せられる質問](managed-identities-faq.md)の一覧を表示した記事を確認できます
