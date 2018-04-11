---
title: データ サイエンス仮想マシンの開発ツール - Azure | Microsoft Docs
description: データ サイエンス仮想マシンの開発ツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 6f141fc03b64d0ca922d003f6352b7751ab9967d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシン上の開発ツール

データ サイエンス仮想マシン (DSVM) は、いくつかの一般的なツールと IDE をバンドルすることにより、開発のための生産性の高い環境を提供します。 DSVM で提供されるツールには、次のものがあります。 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| 紹介   | 汎用 IDE      |
| サポートされている DSVM バージョン      | Windows      |
| 一般的な用途      | ソフトウェア開発    |
| DSVM での構成/インストール方法      | データ サイエンス ワークロード (Python および R ツール)、Azure ワークロード (Hadoop、Data Lake)、Node.js、SQL Server ツール、[Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai)    |
| 使用/実行方法      | デスクトップ ショートカット (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| DSVM 上の関連ツール      |     Visual Studio Code、RStudio、Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| 紹介   | 汎用 IDE      |
| サポートされている DSVM バージョン      | Windows、Linux     |
| 一般的な用途      | コード エディターと Git の統合   |
| 使用/実行方法      | Windows のデスクトップ ショートカット (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`)、Linux のデスクトップ ショートカットまたはターミナル (`code`)    |
| DSVM 上の関連ツール      |     Visual Studio 2017、RStudio、Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 
|    |           |
| ------------- | ------------- |
| 紹介   | R 言語用のクライアント IDE    |
| サポートされている DSVM バージョン      | Windows、Linux      |
| 一般的な用途      |  R 開発     |
| 使用/実行方法      | Windows のデスクトップ ショートカット (`C:\Program Files\RStudio\bin\rstudio.exe`)、Linux のデスクトップ ショートカット (`/usr/bin/rstudio`)      |
| DSVM 上の関連ツール      |   Visual Studio 2017、Visual Studio Code、Juno      |

## <a name="rstudio--server"></a>RStudio Server 
|    |           |
| ------------- | ------------- |
| 紹介   | R 言語用の Web ベース IDE    |
| サポートされている DSVM バージョン      | Linux      |
| 一般的な用途      |  R 開発     |
| 使用/実行方法      | _systemctl enable rstudio-server_ を使用してサービスを有効にしてから、_systemctl start rstudio-server_ でサービスを開始します。 その後、RStudio Server (http://your-vm-ip:8787) にログインできます。       |
| DSVM 上の関連ツール      |   Visual Studio 2017、Visual Studio Code、RStudio Desktop      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| 紹介   | Julia 言語用のクライアント IDE   |
| サポートされている DSVM バージョン      | Windows、Linux      |
| 一般的な用途      |  Julia 開発     |
| 使用/実行方法      | Windows のデスクトップ ショートカット (`C:\JuliaPro-0.5.1.1\Juno.bat`)、Linux のデスクトップ ショートカット (`/opt/JuliaPro-VERSION/Juno`)      |
| DSVM 上の関連ツール      |   Visual Studio 2017、Visual Studio Code、RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| 紹介   | Python 言語用のクライアント IDE    |
| サポートされている DSVM バージョン      | Linux      |
| 一般的な用途      |  R 開発     |
| 使用/実行方法      | Linux のデスクトップ ショートカット (`/usr/bin/pycharm`)      |
| DSVM 上の関連ツール      |   Visual Studio 2017、Visual Studio Code、RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| 紹介   | 対話型データ視覚化と BI ツール    |
| サポートされている DSVM バージョン      | Windows  |
| 一般的な用途      |  データ視覚化とダッシュボード構築   |
| 使用/実行方法      | デスクトップ ショートカット (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| DSVM 上の関連ツール      |   Visual Studio 2017、Visual Studio Code、Juno      |

