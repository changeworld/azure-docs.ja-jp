---
title: 既定の回答の取得 - カスタム質問応答
description: 規定の回答は、質問に対する回答が見つからなかった場合に返されます。 カスタム質問応答では、標準の既定の回答から既定の回答を変更することもできます。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
author: mrbullwinkle
ms:author: mbullwin
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 44f2ba3d3a1ae3e05cace797349a2680584a9aff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476568"
---
# <a name="change-default-answer-for-question-answering"></a>質問応答の既定の回答を変更する

プロジェクトの既定の回答は、回答が見つからない場合に返されることを意図しています。 [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna) などのクライアント アプリケーションを使用している場合は、スコアしきい値を満たす回答がないことを示す別個の既定の回答が用意されている場合もあります。

## <a name="default-answer"></a>[Default answer]\(既定の回答\)


|[Default answer]\(既定の回答\)|回答の説明|
|--|--|
|回答が決定されないときの KB 回答|`No good match found in KB.` - 質問応答 API で質問に対応する回答が見つからないとき、既定のテキスト応答が表示されます。 質問応答では、プロジェクトの **[設定]** でこのテキストを設定できます。 |

### <a name="client-application-integration"></a>クライアント アプリケーション統合

**Azure Bot サービス** のボットなど、クライアント アプリケーションの場合、共通となる次のシナリオから選択できます。

* プロジェクトとナレッジベースの設定を使用する
* 回答が返されるが、スコアしきい値を満たさないとき、クライアント アプリケーションで別のテキストを使用して区別する。 このテキストは、コード内に格納されている静的テキストにするか、クライアント アプリケーションの設定リストに格納することができます。

## <a name="change-default-answer-in-language-studio"></a>Language Studio で既定の回答を変更する

質問応答から回答が返されない場合、ナレッジ ベースの既定の回答が返されます。

1. [Language Studio](https://language.azure.com) にサインインして、リストからプロジェクトを選びます。
1. 左側のナビゲーション バーから **[設定]** を選択します。
1. **[回答が見つからない場合の既定の回答]** の値を変更する > **[保存]** を選択します。

> [!div class="mx-imgBorder"]
> ![既定の回答を赤いボックスで囲んだプロジェクト設定のスクリーンショット](../media/change-default-answer/settings.png)

## <a name="next-steps"></a>次のステップ

* [ナレッジ ベースの作成](manage-knowledge-base.md)
