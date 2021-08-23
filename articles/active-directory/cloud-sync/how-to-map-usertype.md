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
ms.openlocfilehash: 14bbeb7339b3af41b24b25aed2332ac8367e99ae
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776922"
---
# <a name="map-usertype-with-cloud-sync"></a>クラウド同期を使用して UserType をマッピングする方法

クラウド同期では、ユーザー オブジェクトの UserType 属性の同期がサポートされています。 

既定では、UserType 属性の同期が有効になっていません。これは、対応する UserType 属性がオンプレミスの Active Directory に存在しないためです。 このマッピングは、同期用に手動で追加する必要があります。 この操作の前に、Azure AD によって適用されている次の動作に注意する必要があります。

- Azure AD で指定できる UserType 属性の値は、Member と Guest の 2 つのみです。
- クラウド同期で UserType 属性がマッピングされていない場合、ディレクトリの同期によって作成された Azure AD ユーザーの UserType 属性は Member に設定されます。

UserType 属性のマッピングを追加する前に、まず UserType 属性をオンプレミスの Active Directory からどのように派生させるかを決める必要があります。 その一般的な方法を次に示します。

 - 未使用のオンプレミス AD 属性 (extensionAttribute1 など) が、ソース属性として使用されるように指定します。 指定したオンプレミス AD 属性は文字列型、単一値である必要があります。また、Member または Guest 値が含まれます。
 - このアプローチを選択した場合は、UserType 属性の同期を有効にする前に、Azure AD に同期されているオンプレミス Active Directory の既存のユーザー オブジェクトすべてについて、指定した属性に正しい値が設定されていることを確認する必要があります。

## <a name="to-add-the-usertype-mapping"></a>UserType マッピングを追加するには
UserType マッピングを追加するには、次の手順に従います。

 1. Azure portal で、 **[Azure Active Directory]** を選択します
 2. **[Azure AD Connect]** を選びます。
 3. **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。
 4. **[構成]** の下で、自分の構成を選択します。
 5. **[属性の管理]** で、 **[クリックしてマッピングを編集]** を選択します。
  ![属性マッピングを編集する](media/how-to-map-usertype/usertype-1.png) 

 6. **[属性マッピングの追加]** をクリックします
    ![属性マッピングを追加する](media/how-to-map-usertype/usertype-2.png) 
7. マッピングの種類を選択します。 マッピングは、次の 3 つの方法のいずれかで行うことができます。
 - 直接マッピング (つまり、AD 属性から)
 - 式 (IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member"))
 - 定数 (つまり、すべてのユーザー オブジェクトをゲストとして作成する)。
  ![Usertype を追加する](media/how-to-map-usertype/usertype-3.png) 
8. [ターゲット属性] ドロップダウンで、[UserType] を選択します。
9. ページの下部にある **[適用]** ボタンをクリックします。 これにより、Azure AD UserType 属性のマッピングが作成されます。

## <a name="next-steps"></a>次のステップ 

- [Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)
- [クラウド同期の構成](how-to-configure.md)
