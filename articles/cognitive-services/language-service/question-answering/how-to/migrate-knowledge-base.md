---
title: プロジェクトとナレッジ ベースを移行する - カスタム質問と回答
description: カスタム質問と回答プロジェクトを移行するには、あるリソースからプロジェクトをエクスポートしてから、別のリソースにインポートする必要があります。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 46c092fa096c6f68299f6e0ac8254bfd25c7ce75
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092282"
---
# <a name="migrate-projects-and-question-answer-sources"></a>プロジェクトと質問応答のソースを移行する

次のようなケースでは、プロジェクトのコピーを作成するとよいかもしれません。

* バックアップと復元のプロセスを導入する
* CI/CD パイプラインと統合する
* データを別のリージョンに移動する

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください。
* Azure portal でカスタム質問と回答機能が有効になっている[言語リソース](https://aka.ms/create-language-resource)。 リソースを作成したときに選んだ Azure Active Directory ID、サブスクリプション、言語リソース名を覚えておいてください。

## <a name="export-a-project"></a>プロジェクトをエクスポートする

プロジェクトをエクスポートすると、1 つのプロジェクト内に含まれるすべての質問応答のソースを移行またはバックアップできます。

1. [Language Studio](https://language.azure.com/) にサインインします。
1. プロジェクトの移行元の言語リソースを選択します。
1. **[プロジェクト]** ページには、2 つのエクスポート形式 (Excel または TSV) のオプションがあります。 これにより、ファイルの内容が決定されます。 ファイル自体は、すべてのナレッジ ベースを含む .zip ファイルとしてエクスポートされます。

## <a name="import-a-project"></a>プロジェクトをインポートする  

1. 言語リソースを選択します。これは、以前にエクスポートしたプロジェクトの移行先になります。
1. **[プロジェクト]** ページで **[インポート]** を選択し、エクスポートを選択したときに使用した形式を選択します。 次に、エクスポートしたプロジェクトを含むローカルの .zip ファイルを参照します。 新しくインポートするプロジェクトの名前を入力し、 **[完了]** を選択します。

## <a name="export-question-and-answers"></a>質問と回答をエクスポートする

1. 個々の質問応答ソースを移行する言語リソースを選択します。
1. エクスポートする質問と回答のソースを含むプロジェクトを選択します。
1. [ナレッジ ベースの編集] ページで、ツール バーの **[リッチ テキストを有効にする]** の右側にある省略記号 (`...`) アイコンを選択します。 エクスポートのオプションは、Excel または TSV のいずれかです。

## <a name="import-question-and-answers"></a>質問と回答をインポートする

1. 言語リソースを選択します。これは、以前にエクスポートした質問と回答のソースの移行先になります。
1. 質問と回答のソースをインポートするプロジェクトを選択します。
1. [ナレッジ ベースの編集] ページで、ツール バーの **[リッチ テキストを有効にする]** の右側にある省略記号 (`...`) アイコンを選択します。 インポートのオプションは、Excel または TSV ファイルのいずれかです。
1. **[ファイルの選択]** オプションを使用してファイルのローカルの場所を参照し、 **[完了]** を選択します。

<!-- TODO: Replace Link-->
### <a name="test"></a>テスト

**[ナレッジ ベースの編集]** ページのツールバーから **[テスト]** オプションを選択してテスト パネルを起動し、質問と回答のソースを **テスト** します。 [ナレッジ ベースのテスト](../../../qnamaker/How-To/test-knowledge-base.md)方法を確認してください。

### <a name="deploy"></a>配置

<!-- TODO: Replace Link-->
ナレッジ ベースを **配置** し、チャットボットを作成します。 [ナレッジ ベースの配置](../../../qnamaker/Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)方法を確認してください。

## <a name="chat-logs"></a>チャット ログ

プロジェクトやナレッジ ベースを使用してチャット ログを移行する方法はありません。 診断ログが有効になっている場合、チャット ログは関連付けられている Azure Monitor リソースに格納されます。

## <a name="next-steps"></a>次のステップ

<!-- TODO: Replace Link-->
> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../../../qnamaker/How-To/edit-knowledge-base.md)
