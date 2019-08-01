---
title: チュートリアル:R で予測モデルをトレーニングおよび比較する
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この 3 部構成のチュートリアル シリーズのパート 2 では、Azure SQL Database Machine Learning Services (プレビュー) を使用して R で 2 つの予測モデルを作成してから、最も正確なモデルを選択します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/26/2019
ms.openlocfilehash: 2c85a378dc219e8af1b6458344ee4dba0fa73e68
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596802"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>チュートリアル:Azure SQL Database Machine Learning Services (プレビュー) を使用して R で予測モデルを作成する

この 3 部構成のチュートリアル シリーズのパート 2 では、R で 2 つの予測モデルを作成し、最も正確なモデルを選択します。 このシリーズの次のパートでは、Azure SQL Database Machine Learning Services (プレビュー) を使用して、このモデルを SQL データベースにデプロイします。

この記事では、以下の方法について説明します。

> [!div class="checklist"]
> * 2 つの機械学習モデルをトレーニングする
> * 両方のモデルで予測を行う
> * 結果を比較して最も正確なモデルを選択する

[パート 1](sql-database-tutorial-predictive-model-prepare-data.md) では、サンプル データベースをインポートし、R での予測モデルのトレーニングに使用されるデータを準備する方法を学習しました。

[パート 3](sql-database-tutorial-predictive-model-deploy.md) では、モデルをデータベースに格納した後、パート 1 と 2 で開発した R スクリプトからストアド プロシージャを作成する方法を学習します。 このストアド プロシージャは、SQL データベース内で実行され、新しいデータに基づいて予測を行います。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

* このチュートリアルのパート 2 では、[**パート 1**](sql-database-tutorial-predictive-model-prepare-data.md) とその前提条件を完了していることを前提としています。

## <a name="train-two-models"></a>2 つのモデルをトレーニングする

スキー レンタル データに最適なモデルを見つけるために、2 つの異なるモデル (線形回帰とデシジョン ツリー) を作成し、どちらの予測がより正確であるかを調べます。 このシリーズのパート 1 で作成したデータ フレーム `rentaldata` を使用します。

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>両方のモデルで予測を行う

予測関数を使用し、トレーニング済みの各モデルを使ってレンタル数を予測します。

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>結果を比較する

次に、どちらのモデルで最善の予測が得られるかを調べます。 これを行うための迅速で簡単な方法は、基本的なプロット関数を使用して、トレーニング データの実際の値と予測値の差を表示することです。

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![2 つのモデルを比較する](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

2 つのモデルのうち、デシジョン ツリー モデルの方がより正確であるように見えます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルを続行しない場合は、自分の Azure SQL Database サーバーから TutorialDB データベースを削除します。

Azure portal から次の手順を実行します。

1. Azure portal の左側のメニューから、 **[すべてのリソース]** または **[SQL データベース]** を選択します。
1. **[名前でフィルター]** フィールドに、「**TutorialDB**」と入力し、お使いのサブスクリプションを選択します。
1. 自分の TutorialDB データベースを選択します。
1. **[概要]** ページで **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアル シリーズのパート 2 では、これらの手順を完了しました。

* 2 つの機械学習モデルをトレーニングする
* 両方のモデルで予測を行う
* 結果を比較して最も正確なモデルを選択する

作成した機械学習モデルをデプロイするには、このチュートリアル シリーズのパート 3 に従ってください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure SQL Database Machine Learning Services (プレビュー) を使用して R で予測モデルをデプロイする](sql-database-tutorial-predictive-model-deploy.md)
