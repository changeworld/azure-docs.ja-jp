---
title: QnA Maker ナレッジ ベースをカスタム質問と回答に移行する
description: 最新の機能を活用するために、レガシ QnAMaker ナレッジ ベースをカスタム質問と回答に移行します。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.author: diagarw
author: DishaAgarwal
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2ffd43a2747d462257b4223285b12b5f81e3e4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092323"
---
# <a name="migrate-from-qna-maker-to-custom-question-answering"></a>QnA Maker からカスタム質問と回答への移行

カスタム質問と回答は 2021 年 5 月に導入され、ディープ ラーニング ランカーを使用した関連性の強化、正確な回答、エンドツーエンドのリージョン サポートなど、いくつかの新機能を備えています。 カスタム質問と回答プロジェクトは、QnA Maker のナレッジ ベースに相当します。 QnA Maker リソースのナレッジ ベースを[言語リソース](https://aka.ms/create-language-resource)内のカスタム質問と回答プロジェクトに簡単に移行できます。 また、複数の QnA Maker リソースから特定の言語リソースにナレッジ ベースを移行することもできます。

ナレッジ ベースを正常に移行するには、**移行を実行するアカウントに、 選択した QnA Maker および言語リソースへの共同作成者のアクセス権が必要です**。 ナレッジ ベースを移行すると、次の詳細が新しいカスタム質問と回答プロジェクトにコピーされます。

- アクティブ ラーニングの提案を含む QnA のペア。
- QnA Maker リソースからの同意語と既定の回答。
- ナレッジ ベース名がプロジェクトの説明フィールドにコピーされます。

ロールベースのアクセス制御 (RBAC) などのリソース レベルの設定は、新しいリソースには移行されません。 これらのリソース レベルの設定は、移行後に言語リソースに対して再構成する必要があります。 また、言語リソースの[分析を再び有効](analytics.md)にする必要があります。

## <a name="steps-to-migrate"></a>移行手順

下の手順に従って、ナレッジ ベースを移行できます。

1. 事前にカスタム質問と回答が有効になっている[言語リソース](https://aka.ms/create-language-resource)を作成します。 Azure portal で言語リソースを作成すると、カスタム質問と回答を有効にするオプションが表示されます。 そのオプションを選択して続行すると、ナレッジ ベースを保存するための Azure Search の詳細が求められます。

2. 複数の言語のナレッジ ベースを言語リソースに追加したい場合は、[Language Studio](https://lanuage.azure.com) にアクセスして最初のカスタム質問と回答プロジェクトを作成し、以下のように最初のオプションを選択します。 言語リソースの言語設定は、プロジェクトの作成時にのみ指定できます。 単一言語の既存のナレッジ ベースを言語リソースに移行する場合は、この手順を省略できます。

   > [!div class="mx-imgBorder"]
   > ![言語選択 UI 画面のスクリーンショット](../media/migrate-qnamaker/choose-language.png)。

3. [https://www.qnamaker.ai](https://www.qnamaker.ai) にアクセスし、ナレッジ ベース ページの移行メモで **[移行の開始]** を選択します。 移行を開始するためのダイアログ ボックスが開きます。

   :::image type="content" source="../media/migrate-qnamaker/start-migration.png" alt-text="qnamaker.ai のバナーに表示される [移行の開始] ボタン" lightbox="../media/migrate-qnamaker/start-migration.png":::。

4. 移行を開始するために必要な詳細情報を入力します。 テナントが自動的に選択されます。 テナントの切り替えを選択できます。

   > [!div class="mx-imgBorder"]
   > ![QnAMaker の移行で、テナント選択オプションの周りに赤い選択ボックスが表示されている](../media/migrate-qnamaker/tenant-selection.png)

5. 移行するナレッジ ベースを含む QnA Maker リソースを選択します。

   > [!div class="mx-imgBorder"]
   > ![QnAMaker の移行で、QnAMaker リソースの選択オプションの周りに赤い選択ボックスが表示されている](../media/migrate-qnamaker/select-resource.png)

6. ナレッジ ベースの移行先の言語リソースを選びます。 カスタム質問と回答が有効になっている言語リソースのみが表示されます。 言語リソースの言語設定がオプションに表示されます。 言語設定が指定されていない場合、QnA Maker リソースから言語リソースに複数の言語のナレッジ ベースを移行することはできません。

   > [!div class="mx-imgBorder"]
   > ![QnAMaker の移行で、言語リソース オプションの周りに赤い選択ボックスが表示されており、現在選択されているリソースには、言語が指定されていないという情報が含まれている](../media/migrate-qnamaker/language-setting.png)

    複数の言語のナレッジ ベースを言語リソースに移行する場合は、言語リソースの最初のカスタム質問と回答プロジェクトを作成するときに、複数の言語の設定を有効にする必要があります。 これを行うには、手順 2 の指示に従ってください。 **言語リソースの言語設定が指定されていない場合は、選択された QnA Maker リソースの言語が割り当てられます**。

7. 移行するすべてのナレッジ ベースを選んで、 **[次へ]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![QnAMaker の移行で、ナレッジ ベースの選択オプションの周りに赤い選択ボックスが表示されており、3 つのナレッジ ベース名が表示されたドロップダウンがある](../media/migrate-qnamaker/select-knowledge-bases.png)

8. 移行する予定のナレッジ ベースを確認できます。 プロジェクト名にいくつかの検証エラーが発生する可能性があります。これは、カスタム質問と回答プロジェクトの厳密な検証規則に従っているためです。 

    > [!CAUTION]
    > ターゲット言語リソースに既に存在するプロジェクトと同じ名前のナレッジ ベースを移行する場合、選択したナレッジ ベースの内容で **プロジェクトの内容が上書きされます**。

    > [!div class="mx-imgBorder"]
    > ![プロジェクト名に特殊文字は使用できないことを示すエラー メッセージのスクリーンショット](../media/migrate-qnamaker/special-characters.png)

9. 検証エラーを解決したら、 **[次へ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![特殊文字が削除された状態のスクリーンショット](../media/migrate-qnamaker/validation-errors.png)

10. 移行が完了するまでに数分かかります。 移行の進行中にキャンセルしないでください。 移行後、[Language Studio](https://lanuage.azure.com) 内の移行されたプロジェクトに移動できます。

    > [!div class="mx-imgBorder"]
    > ![正常に移行されたナレッジ ベースのスクリーンショット。Language Studio を使って公開できる情報が表示されている](../media/migrate-qnamaker/migration-success.png)

    カスタム質問と回答プロジェクトへの移行に失敗したナレッジ ベースがある場合は、エラーが表示されます。 最も一般的な移行エラーは、次の場合に発生します。
    
    - ソースとターゲットのリソースが無効である。
    - 空のナレッジ ベース (KB) を移行しようとしている。
    - ターゲット リソースにリンクされている Azure Search インスタンスの上限に達した。

    > [!div class="mx-imgBorder"]
    > ![失敗した移行のスクリーンショットとエラーの例](../media/migrate-qnamaker/migration-errors.png)

    これらのエラーを解決したら、移行を再実行できます。

11. 移行では、ナレッジ ベースのテスト インスタンスのみがコピーされます。 移行が完了したら、ナレッジ ベースを手動でデプロイして、テスト インデックスを運用環境のインデックスにコピーする必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure Monitor 診断ログ](analytics.md)で分析を再び有効にする方法を確認します。
