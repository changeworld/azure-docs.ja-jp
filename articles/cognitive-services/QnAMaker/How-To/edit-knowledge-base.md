---
title: ナレッジ ベースの編集 - QnA Maker
description: QnA Maker では、使いやすい編集環境で、ナレッジ ベースのコンテンツを管理することができます。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756723"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>ナレッジベースで QnA ペアを編集する

QnA Maker では、使いやすい編集環境で、ナレッジ ベースのコンテンツを管理することができます。

QnA ペアは、ファイルや URL などのデータソースから追加されるか、または編集ソースとして追加されます。 編集ソースは、QnA ペアが QnA ポータルに手動で追加されたことを示します。 すべての QnA ペアを編集できます。

## <a name="add-an-editorial-qna-pair"></a>編集 QnA ペアを追加する
1. [QnA ポータル](https://www.qnamaker.ai/)にサインインし、QnA ペアを追加するナレッジ ベースを選択します。
1. ナレッジ ベースの **[編集]** ページで、 **[QnA ペアの追加]** を選択し、新しい QnA ペアを追加します。

1. 新しい QnA ペアの行で、必須の **[質問]** フィールドと **[回答]** フィールドを追加します。 他のフィールドはすべて省略可能です。 いつでもすべてのフィールドを変更できます。

1. 必要に応じて**代わりの言い回し**を追加します。 代わりの言い回しは、元の質問とは大きく異なるものの、同じ回答を提供する必要がある任意の形式の質問です。

    ナレッジ ベースが発行され、[アクティブ ラーニング](use-active-learning.md)が有効になっている場合、ユーザーの同意を得るために QnA Maker によって代わりの言い回しの選択肢が収集されます。 これらの選択肢は、予測精度を高めるために選択されます。

1. 必要に応じて**メタデータ**を追加します。 メタデータを表示するには、コンテキスト メニューで **[表示のオプション]** を選択します。 メタデータでは、チャット ボットなどのクライアント アプリケーションからの回答に対するフィルターが提供されます。

1. 必要に応じて、**フォローアップ プロンプト**を追加します。 フォローアップ プロンプトでは、ユーザーに提示するクライアント アプリケーションへの追加の会話パスを提供します。

1. 新しい QnA ペアを含む予測を表示するには、 **[保存してトレーニング]** を選択します。

## <a name="edit-a-qna-pair"></a>QnA ペアを編集する

元のデータ ソースに関係なく、すべての QnA ペアのすべてのフィールドを編集できます。 コンテキスト ツール バーにある **[表示のオプション]** の現在の設定のため、一部のフィールドが表示されない場合があります。

## <a name="delete-a-qna-pair"></a>QnA ペアの削除

QnA を削除するには、QnA 行の右端にある**削除**アイコンをクリックします。 これは、永続的な操作です。 この操作を元に戻すことはできません。 セットを削除する前に、 **[公開]** ページから KB をエクスポートすることを検討してください。

![QnA ペアの削除](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>QnA ペア ID を検索する

QnA ペア ID を確認する必要がある場合は、次の 2 つの場所で検索できます。

* 目的の QnA ペア行の削除アイコンをマウスでポイントします。 表示されるテキストに QnA ペア ID が含まれています。
* ナレッジ ベースをエクスポートします。 ナレッジ ベースの各 QnA ペアには QnA ペア ID が含まれています。

## <a name="add-alternate-questions"></a>代わりの質問を追加する

既存の QnA ペアに代わりの質問を追加すること (別の表現で言い換えること) で、ユーザーからの問い合わせに一致する可能性が向上します。

![代わりの質問を追加する](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA ペアのリンク

QnA ペアのリンクは、[フォローアップ プロンプト](multiturn-conversation.md)を使用して提供されます。 これは QnA ペア間の論理的な接続であり、ナレッジ ベースのレベルで管理されます。 フォローアップ プロンプトは、QnA Maker ポータルで編集できます。

回答のメタデータで QnA ペアをリンクすることはできません。

## <a name="add-metadata"></a>メタデータの追加

メタデータのセットを追加するには、最初に **[表示のオプション]** を選択してから、 **[Show metadata]\(メタデータの表示\)** を選択します。 これにより、メタデータの列が表示されます。 次に、 **[+]** 記号を選択して、メタデータのセットを追加します。 このセットは、1 つのキーと 1 つの値で構成されます。

## <a name="save-changes-to-the-qna-pairs"></a>変更を QnA ペアに保存する

変更内行が失われないよう、編集を行った後は、 **[保存してトレーニング]** を定期的に選択してください。

![メタデータの追加](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースの共同作業](./collaborate-knowledge-base.md)

* [QnA Maker によって使用される Azure リソースを管理する](set-up-qnamaker-service-azure.md)