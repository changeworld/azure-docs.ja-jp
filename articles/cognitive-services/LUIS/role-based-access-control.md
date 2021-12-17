---
title: LUIS ロールベースのアクセス制御
titleSuffix: Azure Cognitive Services
description: この記事では、LUIS リソースにアクセス制御を追加する方法について説明します。
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 5d7469e8eb31acb1a9fdeece78affc1d12e33727
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718452"
---
# <a name="luis-role-based-access-control"></a>LUIS ロールベースのアクセス制御

LUIS では、Azure リソースへの個々のアクセスを管理するための承認システムである、Azure のロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Azure RBAC を使用すると、LUIS 作成リソースに対してさまざまなアクセス許可レベルをさまざまなチーム メンバーに割り当てることができます。 詳細については、「[Azure RBAC のドキュメント](../../role-based-access-control/index.yml)」を参照してください。

## <a name="add-role-assignment-to-language-understanding-authoring-resource"></a>Language Understanding 作成リソースにロールの割り当てを追加する

Language Understanding 作成リソースに Azure RBAC を割り当てることができます。 Azure リソースへのアクセス権を付与するには、ロールの割り当てを追加します。
1. [Azure portal](https://ms.portal.azure.com/) で **[すべてのサービス]** を選択します。 
2. **[Cognitive Services]** を選択し、特定の Language Understanding 作成リソースに移動します。
   > [!NOTE]
   > また、リソース グループ、サブスクリプション、または管理グループ全体に対して Azure RBAC を設定することもできます。 これを行うには、目的のスコープ レベルを選択し、目的の項目に移動します。 たとえば、 **[リソース グループ]** を選択し、特定のリソース グループに移動します。

1. 左側のナビゲーション ウィンドウで **[アクセス制御 (IAM)]** を選択します。
1. **[追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。
1. 次の画面の **[ロール]** タブで、追加するロールを選択します。
1. **[メンバー]** タブで、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。
1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。

数分以内に、選択したスコープで選択したロールがターゲットに割り当てられます。 これらの手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="security"></a>セキュリティ 

LUIS では Azure Active Directory (AAD) 認証をサポートします。 詳細については、「[Azure Active Directory を使用して認証する](../authentication.md#authenticate-with-azure-active-directory)」を参照してください。

## <a name="luis-role-types"></a>LUIS ロールの種類

次の表を使用して、LUIS アプリケーションのアクセス ニーズを判断してください。

これらのカスタム ロールは、作成 (Language Understanding 作成) にのみ適用され、予測リソース (Language Understanding) には適用されません。

### <a name="cognitive-services-luis-reader"></a>Cognitive Services LUIS 閲覧者

LUIS アプリケーションの検証と確認のみを行う必要があるユーザー。通常は、プロジェクトを配置する前にアプリケーションが正常に動作していることを確認するためのテスト担当者です。 アプリケーションの資産 (発話、意図、エンティティ) をレビューして、必要な変更をアプリの開発者に通知することが必要になる場合がありますが、それを行うための直接アクセスはありません。


:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **API アクセス**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        * 発話の読み取り
        * 意図 
        * エンティティ
        * アプリケーションのテスト
    :::column-end:::
    :::column span="":::
      以下のすべての GET API: 
        * [LUIS Programmatic v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [LUIS Programmatic v2.0 APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

      以下のすべての API: 
        * [LUIS Endpoint APIs v2.0](https://chinaeast2.dev.cognitive.azure.cn/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
        * [LUIS Endpoint APIs v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8)
        * [LUIS Endpoint APIs v3.0-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a9459a1fe8fa44c28dd8)

      すべてのバッチ テスト Web API
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-writer"></a>Cognitive Services LUIS ライター

大規模なチームのコラボレーターとして、LUIS アプリケーションの構築と変更を担当するユーザー。 コラボレーターは、何らかの方法で LUIS アプリケーションを変更し、それらの変更をトレーニングし、これらの変更をポータルで検証またはテストできます。 ただし、運用環境に変更が誤って反映される可能性があるため、このユーザーには、このアプリケーションをランタイムにデプロイするためのアクセス権はありません。 また、アプリケーションを削除したり、予測リソースとエンドポイントの設定を変更したり (予測リソースの割り当てや割り当て解除を行ったり、エンドポイントをパブリックにしたりする) こともできません。 これにより、このロールでは、現在運用環境で使用されているアプリケーションの変更が制限されます。 また、このリソースの下に新しいアプリケーションを作成することもできますが、上述の制限事項があります。

:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **API アクセス**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Cognitive Services LUIS 閲覧者のすべての機能。 

      追加する機能: 
        * 発話
        * 意図
        * エンティティ
    :::column-end:::
    :::column span="":::
      * LUIS 閲覧者の下にあるすべての API

      以下のすべての POST、PUT、DELETE API:

        * [LUIS Programmatic v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [LUIS Programmatic v2.0 APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2d)

        以下を除く
        * [アプリケーションを削除する](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c39)
        * [アプリを別の LUIS 作成 Azure リソースに移動する](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/apps-move-app-to-another-luis-authoring-azure-resource)
        * [アプリケーションを発行する](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c3b)
        * [ アプリケーション設定の更新](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58aeface39e2bb03dcd5909e)
        * [LUIS Azure アカウントをアプリケーションに割り当てる](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32228e8473de116325515)
        * [割り当てられた LUIS Azure アカウントをアプリケーションから削除する](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32554f8591db3a86232e1)
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-owner"></a>Cognitive Services LUIS 所有者

これらのユーザーは、運用環境での LUIS アプリケーションのゲートキーパーです。 基になる機能のいずれかにフル アクセスできる必要があるため、アプリケーション内のすべてを表示し、作成とランタイム環境の両方の変更を編集するための直接アクセス権を持つことができます。

:::row:::
    :::column span="":::
        **機能**
    :::column-end:::
    :::column span="":::
        **API アクセス**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Cognitive Services LUIS ライターのすべての機能
      * モデルをデプロイする
      * アプリケーションの削除
    :::column-end:::
    :::column span="":::
      * LUIS で使用可能なすべての API
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>次のステップ

* [Azure リソースの管理](./luis-how-to-azure-subscription.md?branch=pr-en-us-171715&tabs=portal#authoring-resource)