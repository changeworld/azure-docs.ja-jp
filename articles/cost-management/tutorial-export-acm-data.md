---
title: チュートリアル - Azure Cost Management からデータをエクスポートし、管理する | Microsoft Docs
description: この記事では、外部システムで使用できるように Azure Cost Management データをエクスポートし、管理する方法を紹介します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/13/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b1c7323dc108f5ee7a8bf6075abfe2a3e2fb9810
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953142"
---
# <a name="tutorial-create-and-manage-exported-data"></a>チュートリアル: データをエクスポートし、管理する

コスト分析のチュートリアルをお読みになっていれば、コスト管理データを手動でダウンロードできます。 しかしながら、毎日繰り返すタスクを作成し、コスト管理データを Azure Storage に毎日自動的にエクスポートできます。 エクスポートしたデータは CSV 形式になり、コスト管理で収集したすべての情報がそれに含まれます。 エクスポート後は、Azure Storage に格納されたそのデータを外部システムで利用したり、独自のデータと組み合わせたりできます。 また、エクスポートしたデータを、ダッシュボードやその他の金融システムのような外部システムで利用できます。

このチュートリアルの例では、段階的にコスト管理データをエクスポートし、データが正常にエクスポートされたことを確認します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 毎日のエクスポートを作成する
> * データが収集されたことを確認する

## <a name="prerequisites"></a>前提条件

- Azure EA アカウント
- BLOB またはファイル ストレージに対して構成された Azure ストレージ アカウント

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com/)) にサインインします。

## <a name="create-a-daily-export"></a>毎日のエクスポートを作成する

コスト管理 + 課金 &gt; サブスクリプションを選択するか、サブスクリプション内のリソース グループを選択する &gt; エクスポート &gt; **追加**。

エクスポートの名前を入力し、サブスクリプション、Azure ストレージ アカウント、コンテナー、ファイル ストレージ ディレクトリ、BLOB コンテナーを指定し、**[作成]** をクリックします。

![新しいエクスポート](./media/tutorial-export-acm-data/new-export01.png)

新しいエクスポートがエクスポートの一覧に表示されます。 既定では、新しいエクスポートは有効になり、毎日実行されます。 日程を組んだエクスポートを無効にするか、削除する場合、一覧にあるエクスポートをクリックし、**[無効化]** または **[削除]** をクリックします。

初期段階では、エクスポートを実行するまで 1 から 2 時間かかることがあります。 ただし、エクスポート済みファイルにデータが表示されるまで最大 4 時間かかることがあります。

## <a name="verify-that-data-is-collected"></a>データが収集されたことを確認する

コスト管理データが収集されていることは簡単に確認できます。また、Azure Storage Explorer を利用し、エクスポートした CSV データを簡単に表示できます。

エクスポートの一覧で、ストレージ アカウント名をクリックします。 ストレージ アカウント ページで、[Explorer で開く] をクリックします。 確認ボックスが表示されたら、**[はい]** をクリックし、Azure Storage Explorer でファイルを開きます。

![ストレージ アカウント ページ](./media/tutorial-export-acm-data/storage-account-page.png)

ストレージ エクスプローラーで、開くコンテナーに移動し、当月のフォルダーを選択します。 CSV ファイルの一覧が表示されます。 ファイルを選択して **[開く]** をクリックします。

![ストレージ エクスプローラー](./media/tutorial-export-acm-data/storage-explorer.png)

ファイルは、CSV ファイル拡張子を開くように設定されているプログラムまたはアプリケーションで開きます。 今回の例では Excel が使用されます。

![データのエクスポート例](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>エクスポートしたデータに他のシステムからアクセスする

コスト管理データをエクスポートする目的の 1 つは、外部システムからそれにアクセスすることにあります。 ダッシュボード システムやその他の金融システムを使用することがあります。 そのようなシステムは幅広く存在し、例を示すことは実際的ではありません。  しかしながら、「[Azure Storage の概要](../storage/common/storage-introduction.md)」で自分のアプリケーションから自分のデータにアクセスすることから始めることができます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 毎日のエクスポートを作成する
> * データが収集されたことを確認する

次のチュートリアルに進み、活動休止状態のリソースや十分に活用されていないリソースを特定することで効率性を最適化し、改善してください。

> [!div class="nextstepaction"]
> [最適化に関する推奨事項を確認して対処する](tutorial-acm-opt-recommendations.md)
