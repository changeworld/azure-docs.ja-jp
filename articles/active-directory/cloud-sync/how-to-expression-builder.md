---
title: Azure AD Connect クラウド同期で式ビルダーを使用する方法
description: この記事では、クラウド同期での式ビルダーの使用方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e169c2cbabf9e9e37bb93b1e3ee26080105d501e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776945"
---
# <a name="expression-builder-with-cloud-sync"></a>クラウド同期での式ビルダー
式ビルダーは、クラウド同期の下にある Azure の新しいブレードです。 これは、複雑な式の作成に役立ち、これらの式をクラウド同期環境に適用する前にテストすることができます。

## <a name="use-the-expression-builder"></a>式ビルダーを使用する
式ビルダーにアクセスするには、次の手順を実行します。

 1. Azure portal で、 **[Azure Active Directory]** を選択します
 2. **[Azure AD Connect]** を選びます。
 3. **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。
 4. **[構成]** の下で、自分の構成を選択します。
 5. **[属性の管理]** で、 **[クリックしてマッピングを編集]** を選択します。
 6. **[属性マッピングの編集]** ブレードで、 **[属性マッピングの追加]** をクリックします。
 7. **[マッピングの種類]** で **[式]** を選択します。
 8. **式ビルダーを試す (プレビュー)］** を選択します。
 ![式ビルダーの使用](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>式を作成する
このセクションでは、ドロップダウンを使用して、サポートされている関数の一覧から選択することができます。  次に、選択した関数に応じて、入力する追加のフィールドが表示されます。  **[式の適用]** を選択すると、 **[式の入力]** ボックスに構文が表示されます。

たとえば、ドロップダウンから **[Replace]** を選択すると、追加のボックスが表示されます。  関数の構文が水色のボックスに表示されます。  表示されるボックスは、選択した関数の構文に対応しています。  Replace は、指定されたパラメーターに応じて異なる動作をします。  この例では、次を使用します。

- oldValue と replacementValue が指定されたとき:
    - source に含まれるすべての OldValue を replacementValue に置換します。

詳細については、「[Replace](reference-expressions.md#replace)」を参照してください。

まず、replace 関数の置換元の属性を選択する必要があります。 この例では、**mail** 属性を選択しました。 

次に、oldValue の値を入力します。  この oldValue は **@fabrikam.com** になります。  最後に、replacementValue のボックスに値 **@contoso.com** を入力します。

要するにこの式は、@fabrikam.com の値を持つユーザー オブジェクトの mail 属性を @contoso.com の値に置き換えます。  **[式の追加]** ボタンをクリックすると、 **[式の入力]** に構文が表示されます。


>[!NOTE]
>[Replace] を選択した場合に発生する構文に基づいて、oldValue および replacementValue に対応するボックスに値を配置してください。

サポートされる式の詳細については、「[Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)」を参照してください。

### <a name="information-on-expression-builder-input-boxes"></a>式ビルダーの入力ボックスに関する情報
選択した関数に応じて、式ビルダーによって提供されるボックスは複数の値を受け入れます。  たとえば、JOIN 関数は、指定された属性に関連付けられている文字列または値を受け入れます。  たとえば、[givenName] の属性値に含まれる値を使用し、これを "@contoso.com" の文字列値と結合してメール アドレスを作成できます。

  ![入力ボックスの値](media/how-to-expression-builder/expression-8.png)

使用可能な値と式の記述方法の詳細については、「[Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)」を参照してください。

## <a name="test-an-expression"></a>式をテストする
このセクションでは、式をテストできます。  ドロップダウンから、**mail** 属性を選択します。  値に **@fabrikam.com** を入力し、 **[式のテスト]** をクリックします。  

**[式の出力の表示]** ボックスに **@contoso.com** の値が表示されます。

 ![式のテスト](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>式をデプロイする
式に問題がなければ、 **[式の適用]** ボタンをクリックします。
![式の追加](media/how-to-expression-builder/expression-5.png)

これにより、式がエージェント構成に追加されます。
![エージェントの構成](media/how-to-expression-builder/expression-6.png)

## <a name="setting-a-null-value-on-an-expression"></a>式に NULL 値を設定する
属性値を NULL に設定するには、  `""` の値とともに式を使用します。  これにより、NULL 値がターゲット属性に渡されます。

![Null 値](media/how-to-expression-builder/expression-7.png)



## <a name="next-steps"></a>次のステップ 

- [Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)
- [クラウド同期の構成](how-to-configure.md)
