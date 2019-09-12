---
title: サポートされている言語
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine にプレインストールされているサポート対象のプログラム言語および関連ツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: aedaa194a9667f1b89a3370c39c74424f2d01734
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208076"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンでサポートされている言語 

Data Science Virtual Machine (DSVM) には、人工知能 (AI) アプリケーションを構築するための事前構築済み言語および開発ツールがいくつか付属します。 重要なものをいくつか紹介します。

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 エディション)

|    |           |
| ------------- | ------------- |
| サポートされている言語バージョン | Python 2.7 および 3.6 |
| サポートされている DSVM エディション      | Windows Server 2016     |
| DSVM での構成/インストール方法  | 次の 2 つのグローバル `conda` 環境が作成されます: <br /> * `/anaconda/` にある `root` 環境は、Python 3.6 です。 <br/> * `/anaconda/envs/python2` にある `python2` 環境は、Python 2.7 です。       |
| サンプルへのリンク      | Python 用のサンプル Jupyter ノートブックが含まれています。     |
| DSVM 上の関連ツール      | PySpark、R、Julia。      |

> [!NOTE]
> 2018 年 3 月より前に作成された Windows Server 2016 のビルドには、Python 3.5 と Python 2.7 が含まれています。 Python 2.7 は conda **root** 環境であり、**py35** は Python 3.5 環境です。

### <a name="how-to-use-and-run-it"></a>使用と実行方法    

* コマンド プロンプトでの実行:

  コマンド プロンプトを開き、実行する Python のバージョンに応じて次のいずれかの方法を使用します。

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.6
    activate 
    python --version 
* Use in an IDE:

  Use Python Tools for Visual Studio (PTVS), installed in the Visual Studio Community edition. By default, the only environment that's set up automatically in PTVS is Python 3.6. 

    > [!NOTE]
    > To point PTVS at Python 2.7, you must create a  custom environment in PTVS. To set this environment path in the Visual Studio  Community Edition, go to **Tools** -> **Python Tools** -> **Python Environments** and select **+ Custom**. Then, set the location to **c:\anaconda\envs\python2** and select **Auto Detect**.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as _Python [Conda Root]_ for Python 3.6 and _Python [Conda env:python2]_ for Python 2.7.

* Install Python packages:

  The default Python environments on the DSVM are global environments that are readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or python2 environment by using the `activate` command as an administrator. Then, you can use a package manager like `conda` or `pip` to install or update packages.

## Python (Linux and Windows Server 2012 edition)

|    |           |
| ------------- | ------------- |
| Language versions supported | Python 2.7 and 3.5 |
| Supported DSVM editions      | Linux, Windows Server 2012    |
| How is it configured / installed on the DSVM?  | Two global `conda` environments are created: <br /> * `root` environment located at `/anaconda/` is Python 2.7. <br/> * `py35` environment located at `/anaconda/envs/py35`is Python 3.5.       |
| Links to samples      | Sample Jupyter notebooks for Python are included.     |
| Related tools on the DSVM      | PySpark, R, Julia      |
### How to use and run it    

**Linux**
* Run in a terminal:

  Open the terminal and do one of the following, depending on the version of Python you want to run:

    ```
    # <a name="to-run-python-27"></a>Python 2.7 を実行する場合
    source activate   python --version
    
    # <a name="to-run-python-35"></a>Python 3.5 を実行する場合
    source activate py35   python --version
    
    ```
* Use in an IDE:

  Use PyCharm, installed in the Visual Studio Community edition. 

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Python [Conda Root]** for Python 2.7 and **Python [Conda env:py35]** for the Python 3.5 environment. 

* Install Python packages:

  The default Python environments on the DSVM are global environments readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or py35 environment by using the `source activate` command as an administrator or as a user with sudo permissions. Then, you can use a package manager like `conda` or `pip` to install or update packages.

**Windows 2012**
* Run at a command prompt:

  Open a command prompt and do one of the following, depending on the version of Python you want to run:

     ```
    # <a name="to-run-python-27"></a>Python 2.7 を実行する場合
    activate   python --version
    
    # <a name="to-run-python-35"></a>Python 3.5 を実行する場合
    activate py35   python --version
    
    ```
* Use in an IDE:

  Use Python Tools for Visual Studio (PTVS) installed in the Visual Studio Community edition. The only environment that's set up automatically in PTVS is Python 2.7.
    > [!NOTE]
    > To point PTVS at Python 3.5, you need to create a custom environment in PTVS. To set this environment path in the Visual Studio Community edition, go to **Tools** -> **Python Tools** -> **Python Environments** and select **+ Custom**. Then, set the location to `c:\anaconda\envs\py35` and select _Auto Detect_.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Python [Conda Root]** for Python 2.7 and **Python [Conda env:py35]** for Python 3.5. 

* Install Python packages:

  The default Python environments on the DSVM are global environments that are readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or py35 environment by using the `activate` command as an administrator. Then, you can use a package manager like `conda` or `pip` to install or update packages.

## R

|    |           |
| ------------- | ------------- |
| Language versions supported | Microsoft R Open 3.x (100% compatible with CRAN-R)<br /> Microsoft R Server 9.x Developer edition (a scalable enterprise-ready R platform)|
| Supported DSVM editions      | Linux, Windows     |
| How is it configured / installed on the DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links to samples      | Sample Jupyter notebooks for R are included.     |
| Related tools on the DSVM      | SparkR, Python, Julia      |
### How to use and run it    

**Windows**:

* Run at a command prompt:

  Open a command prompt and type `R`.

* Use in an IDE:

  Use RTools for Visual Studio (RTVS) installed in the Visual Studio Community edition or RStudio. These are available on the Start menu or as a desktop icon. 

* Use in Jupyter

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **R** to use the Jupyter R kernel (IRKernel).

* Install R packages:

  R is installed on the DSVM in a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run R by using one of the preceding methods. Then, you can run the R package manager `install.packages()` to install or update packages.

**Linux**:

* Run in terminal:

  Open a terminal and run `R`.  

* Use in an IDE:

  Use RStudio, installed on the Linux DSVM.  

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **R** to use the Jupyter R kernel (IRKernel). 

* Install R packages:

  R is installed on the DSVM in a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run R by using one of the preceding methods. Then, you can run the R package manager `install.packages()` to install or update packages.


## Julia

|    |           |
| ------------- | ------------- |
| Language versions supported | 0.6 |
| Supported DSVM editions      | Linux, Windows     |
| How is it configured / installed on the DSVM?  | Windows: Installed at `C:\JuliaPro-VERSION`<br /> Linux: Installed at `/opt/JuliaPro-VERSION`    |
| Links to samples      | Sample Jupyter notebooks for Julia are included.     |
| Related tools on the DSVM      | Python, R      |
### How to use and run it    

**Windows**:

* Run at a command prompt

  Open a command prompt and run `julia`.
* Use in an IDE:

  Use `Juno` with the Julia IDE installed on the DSVM and available as a desktop shortcut.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Julia VERSION**.

* Install Julia packages:

  The default Julia location is a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run Julia by using one of the preceding methods. Then, you can run Julia package manager commands like `Pkg.add()` to install or update packages.


**Linux**:
* Run in a terminal:

  Open a terminal and run `julia`.
* Use in an IDE:

  Use `Juno`, with the Julia IDE installed on the DSVM and available as an **Application** menu shortcut.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Julia VERSION**.

* Install Julia packages:

  The default Julia location is a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run Julia by using one of the preceding methods. Then, you can run Julia package manager commands like `Pkg.add()` to install or update packages.

## Other languages

**C#**: Available on Windows and accessible through the Visual Studio Community edition or at the `Developer Command Prompt for Visual Studio`, where you can run the `csc` command.

**Java**: OpenJDK is available on both the Linux and Windows editions of the DSVM and is set on the path. To use Java, type the `javac` or `java` command at a command prompt in Windows or on the bash shell in Linux.

**Node.js**: Node.js is available on both the Linux and Windows editions of the DSVM and is set on the path. To access Node.js, type the `node` or `npm` command at a command prompt in Windows or on the bash shell in Linux. On Windows, the Visual Studio extension for the Node.js tools is installed to provide a graphical IDE to develop your Node.js application.

**F#**: Available on Windows and accessible through the Visual Studio Community edition or at a `Developer Command Prompt for Visual Studio`, where you can run the `fsc` command.
