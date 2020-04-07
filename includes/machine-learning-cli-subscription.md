---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296878"
---
ログインすると、ご使用のアカウントに関連付けられているサブスクリプションの一覧が表示されます。 `isDefault: true` が示されているサブスクリプション情報が、Azure CLI コマンドに対して現在アクティブになっているサブスクリプションです。 このサブスクリプションは、Azure Machine Learning ワークスペースが含まれているものと同じである必要があります。 サブスクリプション ID を確認するには、[Azure portal](https://portal.azure.com) からワークスペースの概要ページにアクセスします。 SDK を使用してワークスペース オブジェクトからサブスクリプション ID を取得することもできます。 たとえば、「 `Workspace.from_config().subscription_id` 」のように入力します。
    
別のサブスクリプションを選択するには、切り替え先のサブスクリプション ID を指定して [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) コマンドを実行します。 サブスクリプションの選択の詳細については、「[複数の Azure サブスクリプションの使用](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)」を参照してください。