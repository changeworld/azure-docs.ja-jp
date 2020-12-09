---
title: Azure AD Connect クラウド プロビジョニング属性エディター
description: この記事では、属性エディターの使用方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e272c402cac803d10d9998298ce6d3370d0e000
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348805"
---
# <a name="azure-ad-connect-cloud-provisioning-attribute-mapping"></a>Azure AD Connect クラウド プロビジョニング属性マッピング

Azure AD Connect クラウド プロビジョニングには新しい機能が導入されました。これを使用すると、オンプレミスのユーザーまたはグループ オブジェクトと Azure AD 内のオブジェクトの間に属性を簡単にマップすることができます。  この機能は、クラウド プロビジョニング構成に追加されています。

既定の属性マッピングをビジネスのニーズに合わせてカスタマイズできます。 そのため、既存の属性マッピングを変更、削除したり、新規の属性マッピングを作成したりすることができます。  同期される属性のリストについては、[同期される属性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)に関するページを参照してください。

## <a name="understanding-attribute-mapping-types"></a>属性マッピングの種類の概要
属性マッピングを使用すると、Azure AD 内で属性を設定する方法を制御できます。
次の 4 つの異なるマッピングの種類がサポートされます。

- **直接** - ターゲットの属性に、AD 内でリンクされているオブジェクトの属性値を設定します。
- **定数** – ターゲットの属性に、指定した特定の文字列を設定します。
- **式** - ターゲットの属性を、スクリプトのような式の結果に基づいて設定します。
  詳細については、[属性マッピングの式の書き方](reference-expressions.md)に関するページを参照してください。
- **なし** - ターゲットの属性を変更しません。 ただし、ターゲットの属性が空の場合は、指定した既定値が設定されます。

これら 4 つの基本的な種類とともに、カスタム属性マッピングではオプションの **既定** 値の割り当てという概念をサポートします。 既定値の割り当てでは、Azure AD にもターゲット オブジェクトにも値がない場合にも、ターゲットの属性には必ず値が設定されます。 最も一般的な構成では、これを空白のままにします。

## <a name="understanding-attribute-mapping-properties"></a>属性マッピングのプロパティの概要

前のセクションでは、属性マッピングの種類プロパティについて紹介しました。
属性マッピングは、このプロパティと共に次の属性もサポートしています。

- **ソース属性** - ソース システムのユーザー属性 (例:Active Directory)。
- **対象の属性** - ターゲット システムのユーザー属性 (例:Azure Active Directory)。
- **null の場合の既定値 (オプション)** - ソース属性が null の場合にターゲット システムに渡される値。 この値は、ユーザーを作成するときにのみプロビジョニングされます。 既存のユーザーを更新したときは、"null の場合の既定値" はプロビジョニングされません。  
- **このマッピングを適用する**
  - **常に** - このマッピングをユーザーの作成と更新の両方のアクションに適用します。
  - **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します。

> [!NOTE]
> このドキュメントでは、Azure portal を使用して属性をマップする方法について説明します。  Graph の使用方法については、「[変換](how-to-transformation.md)」を参照してください。

## <a name="using-attribute-mapping"></a>属性マッピングの使用
新しい機能を使用するには、次の手順に従ってください。

 1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
 2.  **[Azure AD Connect]** を選びます。
 3.  **[プロビジョニングの管理]** を選択します。

   ![プロビジョニングを管理する](media/how-to-configure/manage1.png)
 
 4. **[構成]** の下で、自分の構成を選択します。
 5. **[クリックしてマッピングを編集]** を選択します。  これにより [属性マッピング] 画面が開きます。

 ![属性の追加](media/how-to-attribute-mapping/mapping6.png)
 6.  **[属性の追加]** をクリックします。

 ![マッピングの種類](media/how-to-attribute-mapping/mapping1.png)
 
 7. **[マッピングの種類]** を選択します。  この例では、式を使用します。
 8.  ボックスに式を入力します。  この例では、`Replace([mail], "@contoso.com", , ,"", ,).` を使用しています。
 9.  ターゲット属性を入力します。  この例では、ExtensionAttribute15 を使用します。
 10. これを適用するタイミングを選択して、 **[適用]** をクリックします。
   
   ![[マッピングの編集]](media/how-to-attribute-mapping/mapping2a.png)
 11. [属性マッピング] 画面に戻ると、新しい属性マッピングが表示されます。  
 12. **[スキーマの保存]** をクリックします。

 ![スキーマの保存](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>属性マッピングをテストする
属性マッピングをテストするには、[オンデマンドのプロビジョニング](how-to-on-demand-provision.md)を使用できます。  から 

1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
2.  **[Azure AD Connect]** を選びます。
3.  **[プロビジョニングの管理]** を選択します。
4. **[構成]** の下で、自分の構成を選択します。
5. **[検証]** で、 **[ユーザーのプロビジョニング]** ボタンをクリックします。 
6. [オンデマンド プロビジョニング] 画面上で。  ユーザーまたはグループの **識別名** を入力し、 **[プロビジョニング]** ボタンをクリックします。  
7. 完了すると、成功の画面と、正常にプロビジョニングされたことを示す 4 つの緑色のチェック ボックスが表示されます。  
  ![プロビジョニングの成功](media/how-to-attribute-mapping/mapping4.png)
1. **[アクションの実行]** で **[詳細の表示]** をクリックします。  右側を見ると、新しい属性が同期され、式が適用されていることがわかります。

  ![実行するアクション](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>次の手順

- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
- [属性マッピングの式の書き方](reference-expressions.md)
- [同期される属性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)