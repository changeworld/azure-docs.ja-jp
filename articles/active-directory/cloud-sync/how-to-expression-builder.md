---
title: Azure AD Connect クラウド同期で式ビルダーを使用する
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
ms.openlocfilehash: 6ef8a039b61a0dd787c65ec66a3acb48a09bc1b2
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506585"
---
# <a name="expression-builder-with-cloud-sync"></a>クラウド同期での式ビルダー
式ビルダーは、クラウド同期の下にある Azure の新しい関数で、複雑な式の作成に役立ちます。 これを使用して、複雑な式をクラウド同期環境に適用する前にテストできます。

## <a name="use-the-expression-builder"></a>式ビルダーを使用する
式ビルダーにアクセスするには:

 1. Azure Portal で、 **[Azure Active Directory]** を選びます。
 1. **[Azure AD Connect]** を選びます。
 1. **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。
 1. **[構成]** の下で、自分の構成を選択します。
 1. **[属性の管理]** で、 **[クリックしてマッピングを編集]** を選択します。
 1. **[属性マッピングの編集]** ウィンドウで、 **[属性マッピングの追加]** を選択します。
 1. **[マッピングの種類]** で **[式]** を選択します。
 1. **式ビルダーを試す (プレビュー)］** を選択します。
 
    ![式ビルダーの使用方法を示すスクリーンショット。](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>式を作成する
このセクションでは、ドロップダウン リストを使用して、サポートされている関数から選択します。 次に、選択した関数に応じて、追加でボックスに入力します。 **[式の適用]** を選択した後、 **[式の入力]** ボックスに構文が表示されます。

たとえば、ドロップダウン リストから **[置換]** を選択すると、追加のボックスが表示されます。 関数の構文が水色のボックスに表示されます。 表示されるボックスは、選択した関数の構文に対応しています。 Replace は、指定されたパラメーターに応じて異なる動作をします。

この例では、**oldValue** と **replacementValue** を指定すると、置換元の **oldValue** のすべての出現箇所が **replacementValue** に置き換えられます。

詳細については、「[置換](reference-expressions.md#replace)」を参照してください。

まず、replace 関数の置換元の属性を選択する必要があります。 この例では、**mail** 属性が選択されています。

次に、**oldValue** のボックスを探して「 **@fabrikam.com** 」と入力します。 最後に、**replacementValue** のボックスに値 **@contoso.com** を入力します。

要するにこの式は、@fabrikam.com の値を持つユーザー オブジェクトの mail 属性を @contoso.com の値に置き換えます。 **[式の追加]** を選択すると、 **[式の入力]** ボックスに構文が表示されます。

>[!NOTE]
>**Replace** を選択した場合に発生する構文に基づいて、**oldValue** および **replacementValue** に対応するボックスに値を配置してください。

サポートされる式の詳細については、「[Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)」を参照してください。

### <a name="information-on-expression-builder-input-boxes"></a>式ビルダーの入力ボックスに関する情報
選択した関数に応じて、式ビルダーによって提供されるボックスは複数の値を受け入れます。 たとえば、JOIN 関数は、指定された属性に関連付けられている文字列または値を受け入れます。 たとえば、 **[givenName]** の属性値に含まれる値を使用し、これを **@contoso.com** の文字列値と結合してメール アドレスを作成できます。

  ![入力ボックスの値を示すスクリーンショット。](media/how-to-expression-builder/expression-8.png)

使用可能な値と式の記述方法の詳細については、「[Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)」を参照してください。

## <a name="test-an-expression"></a>式をテストする
このセクションでは、式をテストできます。 ドロップダウン リストから **mail** 属性を選択します。 値に **@fabrikam.com** を入力し、 **[式のテスト]** を選択します。

値 **@contoso.com** が **[式の出力の表示]** ボックスに表示されます。

 ![式のテストを示すスクリーンショット。](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>式をデプロイする
式に問題がなければ、 **[式の適用]** を選択します。

![式の追加を示すスクリーンショット。](media/how-to-expression-builder/expression-5.png)

これにより、式がエージェント構成に追加されます。

![エージェント構成を示すスクリーンショット。](media/how-to-expression-builder/expression-6.png)

## <a name="set-a-null-value-on-an-expression"></a>式に NULL 値を設定する
属性の値を NULL に設定するには、`""` の値を持つ式を使用します。 この式により、NULL 値がターゲット属性に渡されます。

![NULL 値を示すスクリーンショット。](media/how-to-expression-builder/expression-7.png)


## <a name="next-steps"></a>次のステップ 

- [Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)
- [クラウド同期の構成](how-to-configure.md)
