---
title: データ サイエンス仮想マシンの開発ツール - Azure | Microsoft Docs
description: Data Science Virtual Machine にプレインストールされているツールと統合開発環境について説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 3b8eaae63f0e316d82dd5a1238a802eefd756d9e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557801"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシン上の開発ツール

データ サイエンス仮想マシン (DSVM) は、いくつかの一般的なツールと IDE をバンドルすることにより、開発のための生産性の高い環境を提供します。 DSVM で提供されるツールには、次のものがあります。 

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| 紹介   | 汎用 IDE      |
| サポートされている DSVM バージョン      | Windows      |
| 一般的な用途      | ソフトウェア開発    |
| DSVM での構成/インストール方法      | データ サイエンス ワークロード (Python および R ツール)、Azure ワークロード (Hadoop、Data Lake)、Node.js、SQL Server ツール、[Azure Machine Learning for Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| 使用/実行方法      | デスクトップ ショートカット (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)    |
| DSVM 上の関連ツール      |     Visual Studio Code、RStudio、Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| 紹介   | 汎用 IDE      |
| サポートされている DSVM バージョン      | Windows、Linux     |
| 一般的な用途      | コード エディターと Git の統合   |
| 使用/実行方法      | Windows のデスクトップ ショートカット (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`)、Linux のデスクトップ ショートカットまたはターミナル (`code`)    |
| DSVM 上の関連ツール      |     Visual Studio 2019、RStudio、Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| 紹介   | R 言語用のクライアント IDE    |
| サポートされている DSVM バージョン      | Windows、Linux      |
| 一般的な用途      |  R 開発     |
| 使用/実行方法      | Windows のデスクトップ ショートカット (`C:\Program Files\RStudio\bin\rstudio.exe`)、Linux のデスクトップ ショートカット (`/usr/bin/rstudio`)      |
| DSVM 上の関連ツール      |   Visual Studio 2019、Visual Studio Code、Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| 紹介   | R 言語用の Web ベース IDE    |
| サポートされている DSVM バージョン      | Linux      |
| 一般的な用途      |  R 開発     |
| 使用/実行方法      | _systemctl enable rstudio-server_ を使用してサービスを有効にしてから、_systemctl start rstudio-server_ でサービスを開始します。 その後、RStudio Server (http:\//your-vm-ip:8787) にサインインできます。       |
| DSVM 上の関連ツール      |   Visual Studio 2019、Visual Studio Code、RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| 紹介   | Julia 言語用のクライアント IDE   |
| サポートされている DSVM バージョン      | Windows、Linux      |
| 一般的な用途      |  Julia 開発     |
| 使用/実行方法      | Windows のデスクトップ ショートカット (`C:\JuliaPro-0.5.1.1\Juno.bat`)、Linux のデスクトップ ショートカット (`/opt/JuliaPro-VERSION/Juno`)      |
| DSVM 上の関連ツール      |   Visual Studio 2019、Visual Studio Code、RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| 紹介   | Python 言語用のクライアント IDE    |
| サポートされている DSVM バージョン      | Linux      |
| 一般的な用途      |  Python 開発     |
| 使用/実行方法      | Linux のデスクトップ ショートカット (`/usr/bin/pycharm`)      |
| DSVM 上の関連ツール      |   Visual Studio 2019、Visual Studio Code、RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 

|    |           |
| ------------- | ------------- |
| 紹介   | 対話型データ視覚化と BI ツール    |
| サポートされている DSVM バージョン      | Windows  |
| 一般的な用途      |  データ視覚化とダッシュボード構築   |
| 使用/実行方法      | デスクトップ ショートカット (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| DSVM 上の関連ツール      |   Visual Studio 2019、Visual Studio Code、Juno      |

