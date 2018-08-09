---
title: ナレッジ ベースの共同利用 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker ナレッジ ベースを共同利用する方法
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376165"
---
# <a name="collaborate-on-your-knowledge-base"></a>ナレッジ ベースの共同利用

QnA Maker では、複数のユーザーがナレッジ ベースを共同利用できます。 この機能は、Azure の[ロール ベースのアクセス制御](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure)で提供されています。 

次の手順を実行して、QnA Maker のサービスを他者と共有します。

1. Azure Portal にログインして、使用する QnA Maker リソースに移動します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. **[アクセス制御 (IAM)]** タブを開きます。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. **[追加]** を選択します。

    ![QnA Maker IAM の [追加]](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. **[所有者]** または **[共同作成者]** のロールを選択します。

    ![QnA Maker IAM のロールの追加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. 共有する電子メールを入力して、[保存] を押します。

    ![QnA Maker IAM の電子メールの追加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

これで、QnA Maker サービスを共有する相手が [QnA Maker ポータル](https://qnamaker.ai)にログインすると、そのサービスのすべてのナレッジ ベースを表示できるようになりました。

1 つの QnA Maker サービスにある特定のナレッジ ベースの共有はできないことを忘れないでください。 より詳細なアクセス制御が必要な場合は、さまざまな QnA Maker サービスにわたってナレッジ ベースを配布することを検討してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースのテスト](./test-knowledge-base.md)
