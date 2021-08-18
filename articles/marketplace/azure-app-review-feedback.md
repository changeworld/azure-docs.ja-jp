---
title: Azure アプリ オファーを確認する - Microsoft コマーシャル マーケットプレース
description: Microsoft Azure Marketplace レビュー チームからの Azure アプリケーション オファーのフィードバックを処理します。 Azure DevOps のフィードバックには、パートナー センターの資格情報を使用してアクセスできます。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 56e6de0faeeba6824f17bbe179e7c9b5421163d8
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113231325"
---
# <a name="handle-review-feedback-for-azure-application-offers"></a>Azure アプリケーション オファーのレビュー フィードバックの処理

この記事では、[Azure DevOps](https://azure.microsoft.com/services/devops/) 環境で Microsoft Azure Marketplace レビュー チームからのフィードバックにアクセスする方法について説明します。 **Microsoft のレビュー** ステップで Azure アプリケーション オファーに重大な問題が見つかった場合は、このシステムにサインインして、それらの問題に関する詳細な情報 (レビュー フィードバック) を表示できます。 問題をすべて修正した後、Azure Marketplace に引き続きオファーを発行するには、オファーを再送信する必要があります。 次の図は、このフィードバック プロセスと発行プロセスの関係を示しています。

![フィードバック プロセスのレビュー](media/azure-app/review-feedback-process.png)

通常、レビューの問題はプル リクエスト (PR) と呼ばれます。 各 PR は、問題に関する詳細を含むオンラインの Azure DevOps 項目にリンクされています。 次の図は、レビュー中に問題が見つかった場合のパートナー センターのエクスペリエンスの例を示しています。 

![発行状態](media/azure-app/publishing-status.png)

送信に関する特定の詳細情報が含まれている PR は、[認定レポートの表示] リンクに記載されています。 複雑な状況では、レビューおよびサポート チームからお客様にメールが送信されることもあります。

## <a name="azure-devops-access"></a>Azure DevOps アクセス

パートナー センターの "開発者" ロールにアクセスできるすべてのユーザーは、フィードバックのレビューで参照されている PR 項目を表示するためのアクセス権を持ちます。

## <a name="reviewing-the-pull-request"></a>プルリクエストの確認

プルリクエストに記述されている問題を確認するには、次の手順を使用します。

1. [発行手順] フォームの **[Microsoft によるレビュー]** セクションで、PR リンクを選択してブラウザーを起動し、この PR の **[概要]** (ホーム) ページに移動します。 次の図は、Contoso のサンプル アプリ オファーの重大な問題のホーム ページの例を示しています。 このページには、Azure アプリで検出されたレビューの問題に関する有用な概要情報が含まれています。

    [![pull request のホーム ページ](media/azure-app/pr-home-page-thumb.png)](media/azure-app/pr-home-page.png)
    <br/> "*拡大するには画像をクリックしてください。* "

1. (省略可能) ウィンドウの右側にある **[ポリシー]** セクションで、問題のメッセージを選択して (この例では、**Policy Validation failed\(ポリシーの検証に失敗しました\)** )、問題の低レベルの詳細 (関連付けられているログ ファイルを含む) を調査します。 通常、エラーはログ ファイルの下部に表示されます。

1. ホーム ページの左側にあるメニューで、**ファイル** を選択して、このオファーの技術資産を構成するリスト ファイルを表示します。 検出された重大な問題について説明するコメントが Microsoft のレビュー担当者によって追加されているはずです。 次の例では、2 つの問題が検出されました。

    [![[ファイル] と、検出された 2 つの問題が強調表示されたスクリーンショット。](media/azure-app/pr-files-page-thumb.png)](media/azure-app/pr-files-page.png)
    <br/> "*拡大するには画像をクリックしてください。* "

1. 左側のツリーで各コメント ノードを選択して、周囲のコードのコンテキスト内のコメントに移動します。 チームのプロジェクト内のソース コードを修正し、コメントで説明されている問題を修正します。

>[!Note]
>レビュー チームの Azure DevOps 環境内でオファーの技術資産を編集することはできません。 パブリッシャーの場合、これは含まれているソース コードに対して読み取り専用の環境です。 ただし、Microsoft のレビュー チームのために、コメントに対する返信を残しておくことができます。

   次の例では、パブリッシャーが最初の問題をレビューし、修正して、返信しました。

   ![最初の修正およびコメントの返信](media/azure-app/first-comment-reply.png)

## <a name="next-steps"></a>次のステップ

- レビュー PR に記述されている重大な問題を修正したら、[Azure アプリ オファーを再発行する](azure-app-offer-setup.md)必要があります。
