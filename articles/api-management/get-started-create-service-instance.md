---
title: "Azure API Management インスタンスの作成 | Microsoft Docs"
description: "新しい Azure API Management インスタンスを作成するには、このチュートリアルの手順に従います。"
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Azure API Management サービスの新しいインスタンスの作成

このチュートリアルでは [Azure Portal](https://portal.azure.com/) を使用して新しい API Management インスタンスを作成する手順を説明します。

## <a name="prerequisites"></a>前提条件

+ 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>新しいサービスの作成

1. [Azure Portal](https://portal.azure.com/) で **[新規]** > **[エンタープライズ統合]** > **[API management]** の順に選択します。

    または **[新規]** を選択し、検索ボックスに「`API management`」と入力して Enter キーを押します。 **Create** をクリックしてください。

2. **API Management サービス**ウィンドウで、API Management サービスに一意の **[名前]** を入力します。 この名前を後から変更することはできません。

    > [!TIP]
    > サービスの名前は、*{name}.azure-api.net* の形式で既定のドメイン名を生成するために使用されます。 カスタム ドメイン名を使用する場合は、[カスタム ドメインの構成](configure-custom-domain.md)に関するページをご覧ください。 <br/>
    > サービス名は、サービスおよび対応する Azure リソースへの参照に使用されます。

5. アクセス権のある各種の Azure サブスクリプションの中から、**サブスクリプション**を一つ選択します。
6. **[リソース グループ]**ボックスで、新規または既存のリソースを選択します。  リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../azure-resource-manager/resource-group-overview.md#resource-groups)をご覧ください。
7. **[場所]** で、API Management サービスが作成される地理的リージョンを選択します。 ドロップダウン リストのボックスには、API Management サービスを利用できるリージョンのみが表示されます。 
9. **[組織名]** を入力します。 この名前はさまざまな場所で使用されます。 たとえば、開発者ポータルのタイトルや通知メールの送信者などです。
10. **[管理者のメール アドレス]** で、**[API Management]** からのすべての通知が送信されるメール アドレスを設定します。
11. **[価格レベル]** で、サービスを評価するために **[開発者]** レベルを設定します。 このレベルは運用目的では使用できません。 API Management レベルのスケーリングの詳細については、[アップグレードとスケーリング](upgrade-and-scale.md)に関するをページをご覧ください。
12. **[作成]**を選択します。

    > [!TIP]
    > API Management サービスの作成には、通常 20 分から 30 分かかります。 **[ダッシュボードにピン留めする]** を選択すると、新しく作成したサービスの検索が簡単になります。

## <a name="next-steps"></a>次のステップ

[Azure API Management を使用した API の公開](#api-management-getstarted-publish-api.md)