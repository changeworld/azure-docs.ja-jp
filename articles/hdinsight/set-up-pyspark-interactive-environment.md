---
title: "Azure HDInsight Tools - Visual Studio Code 用の PySpark 対話型環境を設定する | Microsoft Docs"
description: "Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。"
Keywords: "VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,対話型 Hive,対話型クエリ"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 839f379228322eb2da0ff61609634bf1f86e4bb3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code 用の PySpark 対話型環境を設定する

以下の手順では、**HDInsight: PySpark Interactive** の実行時に Python パッケージをインストールする方法を示します。


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>MacOS および Linux で PySpark 対話型環境を設定する
以下の手順では **pip3** コマンドを使用する必要があります (**python 3.x** の場合)。
1. **Python** と **pip** がインストールされていることを確認します。
 
    ![Python と pip のバージョン](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Jupyter をインストールします。
    ```
    sudo pip install jupyter
    ```
    +  Linux と MacOS では次のエラー メッセージが表示されることがあります。

        ![エラー 1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + libkrb5-dev (Linux の場合のみ) をインストールします。次のエラー メッセージが表示されることがあります。

        ![エラー 2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. sparkmagic をインストールします。
   ```
   sudo pip install sparkmagic
   ```

4. 次のコマンドを実行して、ipywidgets が適切にインストールされていることを確認します。
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![ラッパーのカーネルのインストール](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. ラッパーのカーネルをインストールします。 **pip show sparkmagic** を実行すると、sparkmagic のインストール先のパスが表示されます。 

    ![sparkmagic の場所](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. 表示された場所に移動して、次のコマンドを実行します。

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec install](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. インストールの状態を確認します。 

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec list](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    使用可能なカーネル: **python 2.x** に対応するのは **python2** と **pysparkkernel**、**python 3.x** に対応するのは **python3** と **pyspark3kernel** です。 

8. VScode を再起動して、**HDInsight: PySpark Interactive** を実行しているスクリプト エディターに戻ります。

## <a name="next-steps"></a>次のステップ

### <a name="demo"></a>デモ
* HDInsight for VScode: [ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure HDInsight Tool for Visual Studio Code を使用する](hdinsight-for-vscode.md)
* [Azure Toolkit for IntelliJ を使用して Spark Scala アプリケーションを作成して送信する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して SSH を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure Toolkit for IntelliJ を使用して VPN を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](./hdinsight-connect-hive-power-bi.md)。
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)。
