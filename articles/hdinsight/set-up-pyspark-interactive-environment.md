---
title: Azure HDInsight Tools - Visual Studio Code 用の PySpark 対話型環境を設定する
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,インタラクティブ Hive,インタラクティブ クエリ
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: f7048665ea5bfbf6d4c1110a5823cf9acbfd0272
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038653"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code 用の PySpark 対話型環境を設定する

以下の手順では、**HDInsight: PySpark Interactive** を実行して、Python パッケージをインストールする方法を示します。


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>macOS および Linux で PySpark 対話型環境を設定する
**python 3.x** を使用している場合は、以下の手順で **pip3** コマンドを使用する必要があります。

1. **Python** と **pip** がインストールされていることを確認します。
 
    ![Python と pip のバージョン](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Jupyter をインストールします。
    ```
    sudo pip install jupyter
    ```
   Linux と macOS で、次のエラー メッセージが表示されることがあります。

   ![エラー 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. **libkrb5-dev** をインストールします (Linux のみ)。 次のエラー メッセージが表示されることがあります。

   ![エラー 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. **sparkmagic** をインストールします。
   ```
   sudo pip install sparkmagic
   ```

4. 次を実行して、**ipywidgets** が適切にインストールされていることを確認します。
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![ラッパーのカーネルのインストール](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. ラッパーのカーネルをインストールします。 **pip show sparkmagic** を実行します。 出力に **sparkmagic** のインストール先のパスが表示されます。 

    ![sparkmagic の場所](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. その場所に移動して、次を実行します。

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

    使用可能なカーネルの場合: 
    - **python2** と **pysparkkernel** は **python 2.x** に対応します。 
    - **python3** と **pyspark3kernel** は **python 3.x** に対応します。 

8. VSCode を再起動して、**HDInsight: PySpark Interactive** を実行しているスクリプト エディターに戻ります。

## <a name="next-steps"></a>次の手順

### <a name="demo"></a>デモ
* HDInsight for VS Code: [ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure HDInsight Tool for Visual Studio Code を使用する](hdinsight-for-vscode.md)
* [Azure Toolkit for IntelliJ を使用して Spark Scala アプリケーションを作成して送信する](spark/apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して SSH を介して Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure Toolkit for IntelliJ を使用して VPN を介して Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](spark/apache-spark-eclipse-tool-plugin.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](hadoop/apache-hadoop-connect-hive-power-bi.md)。
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](hdinsight-connect-hive-zeppelin.md)
