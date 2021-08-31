---
title: Azure portal を使用して、リソースにマネージド ID アクセスを割り当てる - Azure AD
description: Azure portal を使用して、1 つのリソースにマネージド ID を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/24/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeff666af9d680baa037415cedb19077de9c061f
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966358"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Azure portal を使用してリソースにマネージド ID アクセスを割り当てる

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースにマネージド ID アクセスを付与できます。 この記事では、Azure portal を使用して、Azure 仮想マシンまたは仮想マシン スケール セットのマネージド ID アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC を使用して他のリソースにマネージド ID アクセスを割り当てる

[Azure VM](qs-configure-portal-windows-vm.md)、[Azure 仮想マシン スケール セット](qs-configure-portal-windows-vmss.md)などの Azure リソースでマネージド ID を有効にした後、次の手順を実行します。

1. マネージド ID を構成した Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. アクセス制御を変更する目的のリソースに移動します。 この例では、ストレージ アカウントに Azure 仮想マシンへのアクセスを許可するため、ストレージ アカウントに移動します。

1. **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. ロールとマネージド ID を選択します。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)
     
## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure 仮想マシン上でマネージド ID を有効にするには、「[Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」を参照してください。
- Azure 仮想マシン スケール セット上でマネージド ID を有効にするには、「[Azure portal を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vmss.md)」を参照してください。


