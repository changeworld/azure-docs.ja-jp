---
title: R を使用した実験の拡張
titleSuffix: ML Studio (classic) - Azure
description: R スクリプトの実行モジュールを使用して、R 言語によって Azure Machine Learning Studio (クラシック) 機能を拡張する方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: d6174459237399ea370ee9c36ba537391104a917
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168556"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (クラシック):R を使用した実験の拡張 
[R スクリプトの実行][execute-r-script]モジュール使用して、R 言語によって Azure Machine Learning Studio (クラシック) 機能を拡張できます。

このモジュールは、複数の入力データセットを受け取り、1 つのデータセットを出力として生成します。 [R スクリプトの実行][execute-r-script]モジュールの **R Script** パラメーターに、R スクリプトを入力できます。

モジュールの各入力ポートにアクセスするには、次のようなコードを使用します。

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>現在インストールされているすべてのパッケージを一覧表示する
インストールされているパッケージの一覧を変更できます。 現在インストールされているパッケージの一覧については、「[Azure Machine Learning Studio でサポートされている R パッケージ (クラシック)](https://msdn.microsoft.com/library/azure/mt741980.aspx)」を参照してください。

また、[R スクリプトの実行][execute-r-script]モジュールに次のコードを入力して、現在インストールされているパッケージの完全な一覧を取得することもできます。

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

これにより、[R スクリプトの実行][execute-r-script]モジュールの出力ポートにパッケージの一覧が送信されます。
パッケージの一覧を表示するには、[CSV に変換][convert-to-csv]などの変換モジュールを、[R スクリプトの実行][execute-r-script]モジュールの左側の出力に接続し、実験を実行します。次に、変換モジュールの出力をクリックし、 **[ダウンロード]** を選択します。 

!["CSV に変換" モジュールの出力をダウンロードする](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>パッケージをインポートする
[R スクリプトの実行][execute-r-script]モジュールで次のコマンドを使用して、まだインストールされていないパッケージをインポートできます。

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

上記の `my_favorite_package.zip` はパッケージが含まれているファイルです。




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
