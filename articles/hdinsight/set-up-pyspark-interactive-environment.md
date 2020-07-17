---
title: Azure HDInsight Tools を利用した PySpark 対話型環境
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,インタラクティブ Hive,インタラクティブ クエリ
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 47f774f7b848e34aa7dc219ee4b08fb083cbd813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188210"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code 用の PySpark 対話型環境を設定する

以下の手順では、VS Code に PySpark 対話型環境を設定する方法を示します。

**python/pip** コマンドを使用して、ホーム パスに仮想環境を構築します。 別のバージョンを使用する場合は、**python/pip** コマンドの既定のバージョンを手動で変更する必要があります。 詳細については、[update-alternatives](https://linux.die.net/man/8/update-alternatives) を参照してください。

1. [Python](https://www.python.org/downloads/) と [pip](https://pip.pypa.io/en/stable/installing/) をインストールします。

   * [https://www.python.org/downloads/](https://www.python.org/downloads/) から Python をインストールします。
   * pip を [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) からインストールします (Python インストールからインストールされてない場合)。
   * 次のコマンドを使用して、Python と pip が正常にインストールされていることを確認します。 (省略可能)

        ![Python pip バージョンの確認コマンド](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Python は、macOS の既定のバージョンを使用する代わりに、手動でインストールすることをお勧めします。

2. 以下のコマンドを実行して、**virtualenv** をインストールします。

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>その他のパッケージ

エラー メッセージが表示される場合は、以下のコマンドを実行して、必要なパッケージをインストールします。

   ![Python 用 libkrb5 パッケージをインストールする](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

VSCode を再起動して、**HDInsight: PySpark Interactive** を実行しているスクリプト エディターに戻ります。

## <a name="next-steps"></a>次のステップ

### <a name="demo"></a>デモ

* HDInsight for VS Code: [ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [Azure HDInsight Tool for Visual Studio Code を使用する](hdinsight-for-vscode.md)
* [Azure Toolkit for IntelliJ を使用して Apache Spark Scala アプリケーションを作成して送信する](spark/apache-spark-intellij-tool-plugin.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](spark/apache-spark-jupyter-notebook-install-locally.md)
