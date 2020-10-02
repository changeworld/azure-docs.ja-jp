---
title: LUIS ポータルにサインインする
description: 新しいユーザーが LUIS ポータルにサインインする場合、現在のユーザー アカウントに基づいてサインイン エクスペリエンスが若干異なるものになります。
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: 58212fc35df7f230682d4f80b5eadf6cd8ca0902
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007955"
---
# <a name="sign-in-to-luis-portal"></a>LUIS ポータルにサインインする

新しいユーザーが LUIS ポータルにサインインする場合、現在のユーザー アカウントに基づいてサインイン エクスペリエンスが若干異なるものになります。
  * Azure サブスクリプションに関連付けられている
  * Azure サブスクリプションに関連付けられていない

## <a name="determine-account-type"></a>アカウントの種類の決定

LUIS ポータルに初めてサインインするときは、次のビジュアル インジケーターを使用してアカウントの種類を決定します。

### <a name="account-without-azure-subscription"></a>Azure サブスクリプションのないアカウント

Azure サブスクリプションに関連付けられていないアカウントでは、右上のナビゲーション バーに Azure アイコンが表示されます。 関連付けられているアカウントの種類に移行すると、そのアイコンは表示されなくなります。

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Azure アイコンが含まれている LUIS ナビゲーション バーの部分的なスクリーンショット。":::

### <a name="account-with-azure-subscription"></a>Azure サブスクリプションのある Azure アカウント

Azure サブスクリプションに関連付けられているアカウントでは、使用するサブスクリプションとリソースを選択できます。

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Azure アイコンが含まれている LUIS ナビゲーション バーの部分的なスクリーンショット。":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Azure サブスクリプションに関連付けられているアカウントを使用したサインイン

1. [LUIS ポータル](https://www.luis.ai)にサインインし、使用条件に同意します。

1. 次の 2 つの方法でサインアップできます。

    * Azure リソースを引き続き使用する。これは推奨されるパスであり、近いうちに使用可能な唯一のパスになる予定です。 このパスでは、サブスクリプション内の既存のリソースを選択するか新しいリソースを作成してサインアップすると同時に、LUIS アカウントを Azure オーサリング リソースにリンクすることができます。 これは、後で[移行プロセス](luis-migration-authoring.md#what-is-migration)を実行する必要もなく移行されたサインアップと同じです。 すべてのユーザーは、2020 年 11 月 2 日までに移行する必要があります。

    * スターターまたは試用版キーを引き続き使用する。 このパスでは、リソースを作成しなくても、提供されているスターターまたは試用版のリソースで LUIS にサインインできます。 このパスを選択した場合は、最終的に[アカウントを移行](luis-migration-authoring.md#migration-steps)し、アプリケーションをオーサリング リソースにリンクする必要があります。 そのため、Azure リソースが引き続き使用されるパスを選択することが推奨されます。

    [オーサリングおよびスターター キーの詳細をご確認ください](luis-how-to-azure-subscription.md#luis-resources)。 どちらのリソースでも、100 万件のオーサリング トランザクション と 1000 件の予測エンドポイント トランザクション が無料で提供されます。

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Azure アイコンが含まれている LUIS ナビゲーション バーの部分的なスクリーンショット。":::

1. 既存のオーサリング リソースを使用して移行します

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Azure アイコンが含まれている LUIS ナビゲーション バーの部分的なスクリーンショット。":::

    サブスクリプションに LUIS のオーサリング リソースが既にあり、サインイン中に LUIS アカウントに関連付ける場合は、 **[既存のオーサリング リソースを使用]** オプションを選択して、次の情報を指定します。

    * **[Tenant]\(テナント\)** - Azure サブスクリプションが関連付けられているテナント。 既存のウィンドウからテナントを切り替えることはできません。 テナントを切り替えるには、上部バーにイニシャルが含まれている、右端のアバターを選択します。
    * **[Subscription name]\(サブスクリプション名)** - リソースに関連付けられるサブスクリプション。 テナントに属しているサブスクリプションが複数ある場合は、ドロップダウン リストから目的のサブスクリプションを選択します。
    * **[リソース名]** - アカウントが関連付けられるオーサリング リソース。

    > [!Note]
    > 目的のオーサリング リソースがドロップダウンリストでグレー表示されている場合は、別のリージョンのポータルにサインインしていることを意味します。 [リージョン別のポータルの概念について理解してください](luis-reference-regions.md#luis-authoring-regions)。

1. 新しいオーサリング リソースを作成します

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Azure アイコンが含まれている LUIS ナビゲーション バーの部分的なスクリーンショット。" というメッセージが表示されるまで数秒かかることがあります。 **[続行]** を選択して完了します。

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Azure アイコンが含まれている LUIS ナビゲーション バーの部分的なスクリーンショット。":::

    > [!Note]
    > サブスクリプションを持っていて、ポータルでサインアップしているものと同じオーサリング リソースがリージョン内に少なくとも 1 つある場合は、進むパスを選択しなくても、移行されてリソースに関連付けられた LUIS に自動的にサインインされる可能性があります。


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Azure サブスクリプションに関連付けられていないユーザー アカウントを使用したサインイン

1. [LUIS ポータル](https://www.luis.ai)にサインインし、利用規約に同意していることを確認します。

1. **[続行]** を選択して完了します。 試用版/スターター キーを使用して自動的にサインインされます。 これは、最終的に[アカウントを移行](luis-migration-authoring.md#migration-steps)し、アプリケーションをオーサリング リソースにリンクする必要があることを意味します。 移行プロセスを実行するには、[Azure の無料試用版](https://azure.microsoft.com/free/)にサインインする必要があります。

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Azure アイコンが含まれている LUIS ナビゲーション バーの部分的なスクリーンショット。":::

## <a name="troubleshooting"></a>トラブルシューティング

* サインインしているポータルとは異なるリージョンにある Azure portal からオーサリング リソースを作成した場合、そのオーサリング リソースはグレー表示されます。
* 新しいリソースを作成するときは、リソース名に英数字と "-" のみが含まれていること、および先頭または末尾に "-" を使用できないことを確認してください。 アクティブなインスタンスがある場合、ファイルは正しく実行されません。
* [Azure リソース作成するための、サブスクリプションに対する適切なアクセス許可](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)があることを確認します。 適切なアクセス許可がない場合は、ご使用のサブスクリプションの管理者に連絡して、十分なアクセス許可を付与してもらってください。

## <a name="next-steps"></a>次の手順

* [新しいアプリの開始](luis-how-to-start-new-app.md)方法を学習する
