---
title: LUIS のアカウント設定を管理する | Microsoft Docs
description: LUIS Web サイトを使用して、アカウント設定を管理します。
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: 73e90e5ae86db2c2c4625762b285f8c86f0e241b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398047"
---
# <a name="manage-account-and-authoring-key"></a>アカウントとオーサリング キーを管理する
LUIS アカウントの 2 つの重要な情報は、ユーザー アカウントとオーサリング キーです。 ログイン情報は [account.microsoft.com](https://account.microsoft.com) で管理されます。 オーサリング キーは、[LUIS](luis-reference-regions.md) Web サイトの **[設定]** ページから管理されます。 

## <a name="authoring-key"></a>オーサリング キー

この (**[設定]** ページ上の) リージョン固有の 1 つのオーサリング キーを使用すると、[LUIS](luis-reference-regions.md) Web サイトだけでなく、[オーサリング API](https://aka.ms/luis-authoring-api) からすべてのアプリを作成できます。 必要に応じて、オーサリング キーは、各月のエンドポイントのクエリ数を[制限](luis-boundaries.md)できます。 

![LUIS の [設定] ページ](./media/luis-how-to-account-settings/account-settings.png)

オーサリング キーは、自分が所有しているすべてのアプリの他に、コラボレーターとして一覧に表示されるすべてのアプリで使用されます。

## <a name="authoring-key-regions"></a>オーサリング キーのリージョン
オーサリング キーは、[オーサリング リージョン](luis-reference-regions.md#publishing-regions)に固有です。 このキーは、別のリージョンでは機能しません。 

## <a name="reset-authoring-key"></a>オーサリング キーをリセットする
オーサリング キーが侵害された場合は、キーをリセットします。 [LUIS](luis-reference-regions.md) Web サイトのすべてのアプリでキーがリセットされます。 オーサリング API を使用してアプリを作成する場合は、`Ocp-Apim-Subscription-Key` の値を新しいキーに変更する必要があります。 

## <a name="delete-account"></a>アカウントを削除する
アカウントが削除されるときにどのようなデータが削除されるかについては、[データ ストレージと削除](luis-concept-data-storage.md#accounts)についての記事を参照してください。 

## <a name="next-steps"></a>次の手順

[オーサリング キー](luis-concept-keys.md#authoring-key)の詳細を確認します。 

