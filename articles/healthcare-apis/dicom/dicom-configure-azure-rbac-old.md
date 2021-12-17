---
title: DICOM サービス用に Azure RBAC を構成する - Azure Healthcare APIs
description: この記事では、DICOM サービス用に Azure RBAC を構成する方法について説明します
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2020
ms.author: aersoy
ms.openlocfilehash: 7b414fcd5c42ccfec48b3e17d8bfe1bad27ec952
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814736"
---
# <a name="configure-azure-rbac-for-the-dicom-service"></a>DICOM サービス用に Azure RBAC を構成する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。 

この記事では、[Azure のロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/index.yml) を使用して、DICOM サービスにアクセス権を割り当てる方法について説明します。 

## <a name="assign-roles"></a>ロールを割り当てる

DICOM データ プレーンへのアクセス権をユーザー、サービス プリンシパル、またはグループに付与するには、 **[アクセス制御 (IAM)]** ブレードを選択します。 **[ロールの割り当て]** タブを選択し、 **[+ 追加]** を選択します。

[ ![DICOM アクセス制御](media/dicom-access-control.png) ](media/dicom-access-control.png#lightbox)


**[ロール]** の選択で、DICOM データ プレーンの組み込みのロールのいずれかを検索します。

[ ![RBAC ロールの割り当てを追加します。](media/rbac-add-role-assignment.png) ](media/rbac-add-role-assignment.png#lightbox)

次のいずれかを選択できます。

* DICOM データ所有者: DICOM データへのフル アクセス。
* DICOM データ閲覧者: DICOM データの読み取りおよび検索。

これらのロールがニーズに十分ではない場合は、PowerShell を使用してカスタムのロールを作成することができます。  カスタム ロールの作成の詳細については、[Azure PowerShell を使用したカスタムロールの作成](../../role-based-access-control/tutorial-custom-role-powershell.md)に関するページをご覧ください。

**[選択]** ボックスで、ロールを割り当てるユーザー、サービス プリンシパル、またはグループを検索します。

## <a name="caching-behavior"></a>キャッシュ動作

DICOM サービスは、最大 5 分間、決定をキャッシュします。 許可されたオブジェクト ID の一覧にユーザーを追加することによって DICOM サービスへのアクセス権を付与する場合、または一覧から削除する場合は、アクセス許可の変更が反映されるまで最大 5 分かかることが予想されます。

## <a name="next-steps"></a>次のステップ

この記事では、DICOM サービス データ プレーンに Azure ロールを割り当てる方法について説明しました。 
 
>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
