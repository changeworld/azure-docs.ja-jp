---
title: Azure Logic Apps から SFTP アカウントに接続する | Microsoft Docs
description: Azure Logic Apps を使用して、SFTP サーバーのファイルの監視、作成、管理および受信を行うタスクとワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 8f430477883543aa8f87eb3fb0fb49ab31e2d723
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042040"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して SFTP ファイルの監視、作成、および管理を行う

Azure Logic Apps と SFTP コネクターを使用して、[SFTP](https://www.ssh.com/ssh/sftp/) サーバーでお使いのアカウントを通してファイルの管理、作成、送信および受信を行う自動化されたタスクとワークフローを作成できます。他にも、次のようなアクションを実行できます。

* ファイルの追加または変更を監視します。
* ファイルの取得、作成、コピー、更新、一覧、および削除を行います。
* ファイルの内容とメタデータを取得します。
* アーカイブをフォルダーに抽出します。

SFTP サーバーから応答を取得し、その出力を他のアクションが使用できるようにするトリガーを使用できます。 ロジック アプリでアクションを使用して、SFTP サーバー上でファイルのタスクを実行できます。 他のアクションに SFTP アクションからの出力を使用させることもできます。 たとえば、SFTP サーバーからファイルを定期的に取得する場合、Office 365 Outlook コネクタまたは Outlook.com コネクタを使用して、それらのファイルとその内容に関する電子メールを送信できます。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* SFTP ホスト サーバー アドレスとアカウント資格情報。

   接続を作成してお使いの SFTP アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SFTP アカウントにアクセスするロジック アプリ。 SFTP トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 SFTP アクションを使用するには、**繰り返し**トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-sftp"></a>SFTP への接続

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリの場合: 検索ボックスに、フィルターとして「SFTP」と入力します。 トリガーの一覧で、目的のトリガーを選択します。 

   または

   既存のロジック アプリの場合: アクションを追加する最後のステップの下で、**[新しいステップ]** を選択します。 
   検索ボックスに、フィルターとして「sftp」と入力します。 
   アクションの一覧で、目的のアクションを選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 (**+**) を選択し、**[アクションの追加]**  を選択します。

1. 接続で必要な詳細を指定し、**[作成]** を選択します。

1. 選択したトリガーまたはアクションで必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="examples"></a>例

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP トリガー: ファイルが追加または変更されたとき

このトリガーは、SFTP サーバー上でファイルの追加または変更が行われたことを検出したときに、ロジック アプリ ワークフローを開始します。 そのため、たとえばファイルの内容をチェックし、内容が特定の条件を満たしているかどうか基づいてその内容を取得するかどうかを決定する条件を追加できます。 最後に、ファイルの内容を取得するアクションを追加し、その内容を SFTP サーバー上のフォルダーに格納できます。 

**企業での使用例**: このトリガーを使用して、SFTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。 その後、**ファイルの内容を取得する**などの SFTP アクションを使用して注文の内容を取得し、後続の処理や注文データベースへの格納を行うことができます。

### <a name="sftp-action-get-content"></a>SFTP アクション: 内容を取得する

このアクションは、SFTP サーバー上のファイルの内容を取得します。 そのため、たとえば前の例のトリガーと、ファイルの内容が満たす必要がある条件を追加できます。 条件が true であれば、内容を取得するアクションを実行できます。 

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/sftpconnector/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。