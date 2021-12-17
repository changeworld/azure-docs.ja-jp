---
title: 開発ツール
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine で使用できるツールと統合開発環境について説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: efe57637347b5886827f5da443acd9240974d687
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070968"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine の開発ツール

Data Science Virtual Machine (DSVM) には、生産性の高い統合開発環境 (IDE) のいくつかの一般的なツールがバンドルされています。 DSVM で提供されるツールには、次のものがあります。

## <a name="visual-studio-community-edition"></a>Visual Studio Community エディション

| カテゴリ | 値 |
|--|--|
| 紹介 | 汎用 IDE |
| サポートされている DSVM バージョン | Windows Server 2019: Visual Studio 2019 |
| 標準的な使用 | ソフトウェア開発 |
| DSVM での構成とインストール方法 | データ サイエンス ワークロード (Python および R ツール)、Azure ワークロード (Hadoop、Data Lake)、Node.js、SQL Server ツール、[Azure Machine Learning for Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai) |
| 使用と実行方法 | デスクトップ ショートカット (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)。 デスクトップ アイコンまたは **[スタート]** メニューを使用して Visual Studio をグラフィカルに開きます。 プログラムを探し (Windows ロゴ キーを押しながら S キーを押します)、 **[Visual Studio]** を探します。 そこから、C#、Python、R、Node.js などの言語でプロジェクトを作成できます。 |

> [!NOTE]
> 評価期間が終了したことを示すメッセージが表示される場合があります。 ご利用の Microsoft アカウントの資格情報を入力してください。 または、新しい無料アカウントを作成して Visual Studio Community にアクセスします。

## <a name="visual-studio-code"></a>Visual Studio Code 

| カテゴリ | 値 |
|--|--|
| 紹介 | 汎用 IDE |
| サポートされている DSVM バージョン | Windows、Linux |
| 標準的な使用 | コード エディターと Git の統合 |
| 使用と実行方法 | Windows のデスクトップ ショートカット (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`)、Linux のデスクトップ ショートカットまたはターミナル (`code`) |

## <a name="rstudio-desktop"></a>RStudio Desktop

| カテゴリ | 値 |
|--|--|
| 紹介 | R 言語用のクライアント IDE |
| サポートされている DSVM バージョン | Windows、Linux |
| 標準的な使用 | R 開発 |
| 使用と実行方法 | Windows のデスクトップ ショートカット (`C:\Program Files\RStudio\bin\rstudio.exe`)、Linux のデスクトップ ショートカット (`/usr/bin/rstudio`) |

## <a name="pycharm"></a>PyCharm

| カテゴリ | 値 |
|--|--|
| 紹介 | Python 言語用のクライアント IDE |
| サポートされている DSVM バージョン | Windows 2019、Ubuntu 18.04 |
| 標準的な使用 | Python 開発 |
| 使用と実行方法 | Windows のデスクトップ ショートカット (`C:\Program Files\tk`)。 Linux のデスクトップ ショートカット (`/usr/bin/pycharm`) |
