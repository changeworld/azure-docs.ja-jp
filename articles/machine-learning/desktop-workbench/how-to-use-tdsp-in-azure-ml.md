---
title: Team Data Science Process テンプレートでのプロジェクトの体系化 | Microsoft Docs
description: コラボレーションのためにプロジェクトを体系化する Team Data Science Process (TDSP) テンプレートを Azure Machine Learning でインスタンス化する方法
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.openlocfilehash: 05cb2a62cf0f001012f5faa022de233d7cbdce97
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42146778"
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Team Data Science Process テンプレートでのプロジェクトの体系化

このドキュメントでは、Team Data Science Process (TDSP) テンプレートを使用して Azure Machine Learning でデータ サイエンス プロジェクトを作成する手順について説明します。 これらのテンプレートは、コラボレーションと再現性のためにプロジェクトを体系化するために役立ちます。 


## <a name="what-is-the-team-data-science-process"></a>Team Data Science Process とは
TDSP は、高度な分析ソリューションを実行および配信するアジャイルで反復的なデータ サイエンス プロセスです。 エンタープライズ組織のデータ サイエンス チームのコラボレーションと効率を向上させるように設計されています。 次の 4 つの主要コンポーネントでこれらの目標をサポートします。

   * 標準の[データ サイエンス ライフサイクル](../team-data-science-process/lifecycle.md)の定義。
   * 標準化されたプロジェクト構造の[プロジェクト ドキュメントおよびレポート テンプレート](https://github.com/Azure/Azure-TDSP-ProjectTemplate)
   * 計算とストレージのインフラストラクチャやコード リポジトリなど、プロジェクトを実行するためのそれぞれのインフラストラクチャとリソース。
   * 次のようなデータ サイエンス プロジェクト タスク用の[ツールとユーティリティ](https://github.com/Azure/Azure-TDSP-Utilities)。
      - 協調的なバージョン コントロール
      - コード レビュー
      - データ探索とモデリング
      - 作業の計画

TDSP の詳細については、[Team Data Science Process の概要](../team-data-science-process/overview.md)に関する記事をご覧ください。

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>TDSP の構造とテンプレートを使用する理由
データ サイエンス プロジェクトの構造、ライフサイクル、ドキュメントの標準化は、データ サイエンス チームの効果的なコラボレーションを促進する鍵です。 TDSP テンプレートを使用して Machine Learning プロジェクトを作成すると、調和のとれたチームワークのフレームワークなどが提供されます。

以前に、こうした目標の実現に役立つ [TDSP プロジェクトの構造とテンプレート用の GitHub リポジトリ](https://github.com/Azure/Azure-TDSP-ProjectTemplate)をリリースしました しかしこれまでは、データ サイエンス ツール内で TDSP の構造とテンプレートをインスタンス化することはできませんでした。 現在は、TDSP の構造とドキュメント テンプレートをインスタンス化する Machine Learning プロジェクトを作成できるようになりました。 

## <a name="things-to-note-before-creating-a-new-project"></a>新規プロジェクトを作成する前の注意事項
新しいプロジェクトを作成する*前*に、以下の項目を確認します。
* TDSP Machine Learning [テンプレート](https://aka.ms/tdspamlgithubrepo)を確認します。
* コンテンツ ("docs" フォルダー内に既に存在するもの以外) のサイズは 25 MB 未満である必要があります。 この一覧の後の注意事項を確認してください。
* sample\_data フォルダーは、コードをテストする場合や開発を早期に開始する場合に使用できる小さなデータ ファイル (5 MB 未満) 専用です。
* Word や PowerPoint などのファイルを格納すると、"docs" フォルダーのサイズが大幅に増加する可能性があります。 このようなファイルを格納するには、コラボレーション Wiki、[SharePoint](https://products.office.com/en-us/sharepoint/collaboration)、またはその他のコラボレーション リソースを探すことをお勧めします。
* Machine Learning で大きなファイルと出力を処理する方法については、「[変更の保持と大規模なファイルの処理](http://aka.ms/aml-largefiles)」をご覧ください。

> [!NOTE]
> readme.md ファイルを除き、プロジェクトの実行中には*使用されない*すべてのドキュメント関連コンテンツ (テキスト、マークダウン、イメージ、その他のドキュメント ファイル) が "docs" (すべて小文字) という名前のフォルダーに存在している必要があります。 "docs" フォルダーは Machine Learning の実行で無視される特殊なフォルダーであるため、このフォルダー内のコンテンツは計算のターゲットに不必要にコピーされません。 このフォルダーのオブジェクトは、プロジェクト サイズの 25 MB の上限にカウントされません。 たとえば、"docs" フォルダーは、ドキュメントに必要な大きいイメージ ファイルを保存する場所です。 これらのファイルは、実行履歴を通じて Git によって追跡されます。 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Machine Learning テンプレート ギャラリーから TDSP の構造とテンプレートをインスタンス化する
TDSP の構造とドキュメント テンプレートで新しいプロジェクトを作成するには、次の手順を実行します。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する
新しいプロジェクトを作成するには、Azure Machine Learning を開きます。 左上のウィンドウの **[プロジェクト]** で、プラス記号 (**+**) を選択し、**[新しいプロジェクト]** を選択します。

![新しいプロジェクト](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>新しい TDSP 体系化プロジェクトの作成
   1. 適切なボックスまたはリストで、パラメーターと情報を指定します。

      - プロジェクト名
      - プロジェクト ディレクトリ
      - プロジェクトの説明
      - 空の Git リポジトリ パス
      - ワークスペース名

   2. **検索**ボックスに「**TDSP**」と入力します。 
   3. **[Structure a project with TDSP]\(TDSP 使用したプロジェクトの体系化\)** オプションが表示されたら、そのテンプレートを選択します。 
   4. **[作成]** ボタンをクリックして、TDSP 構造を持つ新しいプロジェクトを作成します。 プロジェクトの作成中に (適切なテキスト ボックスで) 空の Git リポジトリを指定した場合は、プロジェクトの作成後、そのリポジトリにプロジェクトの構造とコンテンツが設定されます。

![TDSP プロジェクトの作成](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>TDSP プロジェクト構造の確認
新しいプロジェクトを作成したら、その構造 (次の図の左側のパネル) を確認できま。 ビジネスの把握のために標準化されたドキュメントのすべての側面が含まれています。 これらのアイテムには、TDSP ライフサイクルの段階、データの場所、定義、およびこのドキュメント テンプレートのアーキテクチャが含まれています。 

表示される構造は、[TDSP プロジェクト構造、ドキュメント、成果物のテンプレート](https://github.com/Azure/Azure-TDSP-ProjectTemplate)で公開されている TDSP 構造から派生し、いくつかの変更があります。 たとえば、いくつかのドキュメント テンプレートは、[ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) という 1 つのマークダウンにマージされています。 

### <a name="project-folder-structure"></a>プロジェクト フォルダー構造
TDSP プロジェクト テンプレートには、次の最上位フォルダーが含まれています。
   - **code**: コードが含まれています。
   - **docs**: プロジェクトに関する必要なドキュメント (たとえば、マークダウン ファイル、関連するメディア) が含まれています。
   - **sample_data**: 開発の初期段階またはテストに使用できる**サンプル (小)** データが含まれています。 通常、これらのセットは数 MB (5 MB) 以下です。 このフォルダーは完全または大きなデータ セット用ではありません。

![サンプル データ](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>TDSP の構造とテンプレートの使用
構造とテンプレートにプロジェクト固有の情報を追加する必要があります。 これらには、プロジェクトの実行と配布に必要なコードと情報を設定することが求められます。 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) ファイルは、プロジェクトに関連する情報で変更する必要のあるテンプレートです。 これには、[TDSP ライフサイクル](../team-data-science-process/lifecycle.md)の 4 つの各ステージに関する情報を入力するのに役立つ質問のセットが付属しています。

プロジェクト構造が実行中または完了後にどのように表示されるかの例を次に示します (次の図の左側のパネル)。 このプロジェクトは、[Team Data Science Process sample project: Classify incomes from US census data in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) (Team Data Science Process サンプル プロジェクト: Azure Machine Learning での米国国勢調査データからの所得の分類) のページから取得したものです。

![プロジェクト構造の例](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>プロジェクトのドキュメント化
プロジェクトのドキュメント化方法については、[TDSP ドキュメント テンプレート](https://github.com/Azure/Azure-TDSP-ProjectTemplate)を参照してください。 現在の Machine Learning TDSP ドキュメント テンプレートでは、すべての情報を [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) ファイルに含めることをお勧めします。 このテンプレートには、プロジェクトに固有の情報を入力する必要があります。 

[ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) テンプレートも用意されています。 プライマリ プロジェクト ドキュメントには含まれないが文書化に役立つ情報を含めるために、このテンプレートを使用することもできます。 

### <a name="example-project-report"></a>プロジェクト レポートの例
[プロジェクト レポート例](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)を取得することができます。 この[米国所得分類サンプル プロジェクト](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)のプロジェクト レポートは、データ サイエンス プロジェクト用の TDSP テンプレートをインスタンス化して使用する方法を示しています。

## <a name="next-steps"></a>次の手順
Machine Learning プロジェクトで TDSP の構造とテンプレートを使用する方法についてよく理解できるように、Machine Learning のドキュメントで完成したプロジェクト例をいくつか提供しています。

- Machine Learning で TDSP プロジェクトを作成する方法を示すサンプルについては、[Team Data Science Process サンプル プロジェクト: Azure Machine Learning での米国国勢調査データからの所得の分類](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)に関するページを参照してください。
- Machine Learning で TDSP インスタンス化されたプロジェクトの自然言語処理 (NLP) にディープ ラーニングを使用するサンプルについては、[ディープ ラーニングと共に自然言語処理を使用した生体エンティティの認識](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)に関するページをを参照してください。

