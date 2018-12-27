---
title: チュートリアル - Azure Cost Management からデータをエクスポートし、管理する | Microsoft Docs
description: この記事では、外部システムで使用できるように Azure Cost Management データをエクスポートし、管理する方法を紹介します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 4614a1417213ed8b4d57c3b7ab21ac7424d75949
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087935"
---
# <a name="tutorial-create-and-manage-exported-data"></a>チュートリアル: データをエクスポートし、管理する

コスト分析のチュートリアルをお読みになっていれば、コスト管理データを手動でダウンロードできます。 その一方で、コスト管理データを Azure Storage に対して毎日、毎週、または毎月、自動的にエクスポートする定期タスクを作成することができます。 エクスポートしたデータは CSV 形式になり、コスト管理で収集したすべての情報がそれに含まれます。 エクスポート後は、Azure Storage に格納されたそのデータを外部システムで利用したり、独自のデータと組み合わせたりできます。 また、エクスポートしたデータを、ダッシュボードやその他の金融システムのような外部システムで利用できます。

このチュートリアルの例では、段階的にコスト管理データをエクスポートし、データが正常にエクスポートされたことを確認します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 毎日のエクスポートを作成する
> * データが収集されたことを確認する

## <a name="prerequisites"></a>前提条件

データのエクスポートは、すべての [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) のお客様が利用できます。 ユーザーおよびグループによるデータのエクスポートについては、サブスクリプションに従い、次の Azure のアクセス許可がサポートされています。

- 所有者: サブスクリプションのスケジュールされたエクスポートを作成、変更、または削除できます。
- 共同作成者: スケジュールされたエクスポートを作成、変更、または削除できます。 他のユーザーが作成したスケジュールされたエクスポートの名前を変更できます。
- 閲覧者: アクセス許可を持っているエクスポートをスケジュールできます。

Azure Storage アカウントの場合:
- エクスポートに関するアクセス許可に関係なく、構成されているストレージ アカウントを変更するには書き込みアクセス許可が必要です。
- BLOB またはファイル ストレージに対して Azure ストレージ アカウントを構成する必要があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com/)) にサインインします。

## <a name="create-a-daily-export"></a>毎日のエクスポートを作成する

コスト管理 + 課金 &gt; コスト管理 &gt; サブスクリプションを選択するか、サブスクリプション内のリソース グループを選択する &gt; エクスポート &gt; **追加**。

エクスポートの名前を入力し、[Daily export of month-to-date costs]\(過去 1 か月間のコストを毎日エクスポート\) オプションを選択します。 **[次へ]** をクリックします。

![エクスポートの種類を示す新しいエクスポートの例](./media/tutorial-export-acm-data/basics_exports.png)

Azure Storage アカウントのサブスクリプションを指定して、お使いのストレージ アカウントを選択します。  エクスポート ファイルの保存先となるストレージ コンテナーとディレクトリ パスを指定します。  **[次へ]** をクリックします。

![ストレージ アカウントの詳細を示す新しいエクスポートの例](./media/tutorial-export-acm-data/storage_exports.png)

エクスポートの詳細を確認して **[作成]** をクリックします。

新しいエクスポートがエクスポートの一覧に表示されます。 既定では、新しいエクスポートが有効になっています。 日程を組んだエクスポートを無効にするか、削除する場合、一覧にあるエクスポートをクリックし、**[無効化]** または **[削除]** をクリックします。

初期段階では、エクスポートを実行するまで 1 から 2 時間かかることがあります。 ただし、エクスポート済みファイルにデータが表示されるまで最大 4 時間かかることがあります。

## <a name="verify-that-data-is-collected"></a>データが収集されたことを確認する

コスト管理データが収集されていることは簡単に確認できます。また、Azure Storage Explorer を利用し、エクスポートした CSV データを簡単に表示できます。

エクスポートの一覧で、ストレージ アカウント名をクリックします。 ストレージ アカウント ページで、[Explorer で開く] をクリックします。 確認ボックスが表示されたら、**[はい]** をクリックし、Azure Storage Explorer でファイルを開きます。

![情報の例と、[Explorer で開く] へのリンクを示すストレージ アカウント ページ](./media/tutorial-export-acm-data/storage-account-page.png)

ストレージ エクスプローラーで、開くコンテナーに移動し、当月のフォルダーを選択します。 CSV ファイルの一覧が表示されます。 ファイルを選択して **[開く]** をクリックします。

![ストレージ エクスプローラーに表示される情報の例](./media/tutorial-export-acm-data/storage-explorer.png)

ファイルは、CSV ファイル拡張子を開くように設定されているプログラムまたはアプリケーションで開きます。 今回の例では Excel が使用されます。

![Excel に表示されるエクスポートした CSV データの例](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>エクスポートしたデータに他のシステムからアクセスする

コスト管理データをエクスポートする目的の 1 つは、外部システムからそれにアクセスすることにあります。 ダッシュボード システムやその他の金融システムを使用することがあります。 そのようなシステムは幅広く存在し、例を示すことは実際的ではありません。  しかしながら、「[Azure Storage の概要](../storage/common/storage-introduction.md)」で自分のアプリケーションから自分のデータにアクセスすることから始めることができます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 毎日のエクスポートを作成する
> * データが収集されたことを確認する

次のチュートリアルに進み、活動休止状態のリソースや十分に活用されていないリソースを特定することで効率性を最適化し、改善してください。

> [!div class="nextstepaction"]
> [最適化に関する推奨事項を確認して対処する](tutorial-acm-opt-recommendations.md)
