---
title: ロールに共同作成者ロールを追加Media Services
description: このトピックでは、共同作成者ロールを共同作成者ロールに追加する方法についてMedia Services。
ms.author: itnorman
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 294587e9f502a7f927bb905fbd354ab588955a85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092383"
---
# <a name="add-contributor-role-on-the-media-services"></a>ロールに共同作成者ロールを追加Media Services

この記事では、共同作成者ロールを共同作成者ロールに割り当てる方法についてMedia Services。

> [!NOTE]
> Azure portal UI を使用して Azure Video Analyzer for Media を作成する場合、選択したマネージド ID には、選択した Media Service アカウントに対する共同作成者アクセス許可が自動的に割り当てられます。

## <a name="prerequisites"></a>前提条件

1. Azure Media Services (AMS)
2. ユーザー割り当てマネージド ID
> [!NOTE]
> Azure Media Services とユーザー割り当てマネージド ID に対する[共同作成者][docs-role-contributor]ロールと[ユーザー アクセス管理者][docs-role-administrator]ロールの両方にアクセスできる Azure サブスクリプションが必要です。 適切なアクセス許可がない場合は、これらのアクセス許可を付与してもらうようアカウント管理者に依頼してください。 関連した Azure Media Services は、Video Analyzer for Media アカウントと同じリージョンになければなりません。


## <a name="add-contributor-role-on-the-media-services"></a>ロールに共同作成者ロールを追加Media Services
### <a name="azure-portal"></a>[Azure Portal](#tab/portal/)

### <a name="add-contributor-role-on-the-media-services-in-the-azure-portal"></a>[共同作成者] ロールを Media Servicesに追加Azure portal

1. [Azure Portal](https://portal.azure.com/) にサインインします。
    * 上部にある検索バーを使用して、「」**と入力** Media Services。
    * Media Service リソースを見つけて選択します。
1. 左側のウィンドウで、[アクセス制御 **(IAM)] をクリックします**。
    * **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。 ロールを割り当てるアクセス許可を持ってない場合は、[ロールの割り当 **ての追加** ] オプションが無効になります。
1. [ロール] ボックスの一覧で [共同作成者ロール [] を][docs-role-contributor] 選択し、[次へ] を **クリックします**。
1. [アクセスを **に割り当てる]** で、[ *マネージド ID] ラジオ ボタンを* 選択します。
    * [+ **メンバーの選択]** ボタンをクリックし、[ **マネージド ID の** 選択] ウィンドウがポップアップ表示されます。
1. **次の** 項目を選択します：
    * [サブスクリプション **]** で、マネージド ID が保存されているサブスクリプション。
    * [マネージド **ID] で、[** ユーザー割り当 *てマネージド ID] を選択します*。
    * [選択 **] セクション** で、Media Services リソースに対する共同作成者のアクセス許可を付与するマネージド ID を検索します。    
1. セキュリティ プリンシパルが見つかったら、クリックして選択します。
1. ロールを割り当てるには、[確認と割り当 **て] をクリックします**

<!-- links -->
[docs-role-contributor]: ../../role-based-access-control/built-in-roles.md#contributor
[docs-role-administrator]: ../../role-based-access-control/built-in-roles.md#user-access-administrator
