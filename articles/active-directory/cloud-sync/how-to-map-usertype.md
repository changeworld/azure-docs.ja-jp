---
title: Azure AD Connect クラウド同期で UserType のマッピングを使用する方法
description: この記事では、クラウド同期で UserType 属性をマッピングする方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b06381472549fd7d9b3f3b5dcd222fcd96f4f15
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506207"
---
# <a name="map-usertype-with-cloud-sync"></a>クラウド同期を使用して UserType をマッピングする方法

クラウド同期では、ユーザー オブジェクトの **UserType** 属性の同期がサポートされています。

既定では、**UserType** 属性の同期が有効になっていません。これは、対応する **UserType** 属性がオンプレミスの Active Directory に存在しないためです。 このマッピングは、同期用に手動で追加する必要があります。 この手順を実行する前に、Azure Active Directory (Azure AD) によって適用されている次の動作に注意する必要があります。

- Azure AD で指定できる **UserType** 属性の値は Member と Guest の 2 つのみです。
- クラウド同期で **UserType** 属性がマッピングされていない場合、ディレクトリの同期によって作成された Azure AD ユーザーの **UserType** 属性は Member に設定されます。

**UserType** 属性のマッピングを追加する前に、まず UserType 属性をオンプレミスの Active Directory からどのように派生させるかを決める必要があります。 その一般的な方法を次に示します。

 - 未使用のオンプレミス Active Directory 属性 (extensionAttribute1 など) が、ソース属性として使用されるように指定します。 指定したオンプレミス Active Directory 属性は、文字列型の単一値である必要があります。また、Member または Guest 値が含まれます。
 - このアプローチを選択した場合、**UserType** 属性の同期を有効にする前に、Azure AD に同期されているオンプレミス Active Directory の既存のユーザー オブジェクトすべてについて、指定した属性に正しい値が設定されていることを確認する必要があります。

## <a name="add-the-usertype-mapping"></a>UserType マッピングを追加する
**UserType** マッピングを追加するには:

 1. Azure Portal で、 **[Azure Active Directory]** を選びます。
 1. **[Azure AD Connect]** を選びます。
 1. **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。
 1. **[構成]** の下で、自分の構成を選択します。
 1. **[属性の管理]** で、 **[クリックしてマッピングを編集]** を選択します。
 
    ![属性マッピングの編集を示すスクリーンショット。](media/how-to-map-usertype/usertype-1.png) 

 1. **[属性マッピングの追加]** を選択します。
 
    ![新しい属性マッピングの追加を示すスクリーンショット。](media/how-to-map-usertype/usertype-2.png) 
1. マッピングの種類を選択します。 マッピングは、次の 3 つの方法のいずれかで行うことができます。
   - 直接マッピング (たとえば、Active Directory 属性から)
   - 式 (IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member" など))
   - 定数 (たとえば、すべてのユーザー オブジェクトをゲストとして作成する)
 
     ![UserType 属性の追加を示すスクリーンショット。](media/how-to-map-usertype/usertype-3.png)

1. **[ターゲット属性]** ドロップダウン ボックスで、**UserType** を選択します。
1. ページ下部の **[適用]** を選択し、Azure AD の **UserType** 属性のマッピングを作成します。

## <a name="next-steps"></a>次のステップ 

- [Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)
- [クラウド同期の構成](how-to-configure.md)
