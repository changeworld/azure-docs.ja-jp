---
title: デザイナーでユーザー補助機能を使用する
titleSuffix: Azure Machine Learning
description: デザイナーで使用できるキーボード ショートカットとスクリーン リーダーのユーザー補助機能について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: lagayhar
author: lgayhardt
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: dfa3b5630b09e2a3e3411afc7d77b76f1b651e72
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555003"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>キーボードを使用して Azure Machine Learning デザイナーを使用する

キーボード ショートカットとスクリーン リーダーのユーザー補助機能を使用して Azure Machine Learning デザイナーを使用する方法について説明します。 Azure portal のすべての場所で使用できるキーボード ショートカットの一覧については、「[Azure Portal のキーボード ショートカット](../azure-portal/azure-portal-keyboard-shortcuts.md)」を参照してください。

このワークフローは、[ナレーター](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) と [JAWS](https://www.freedomscientific.com/products/software/jaws/) でテストされていますが、他の標準的なスクリーン リーダーでも動作するはずです。

## <a name="navigate-the-pipeline-graph"></a>パイプライン グラフ内を移動する

パイプライン グラフは、入れ子になったリストとして構成されます。 外側のリストは、パイプライン グラフ内のすべてのコンポーネントを説明するコンポーネント リストです。 内側のリストは、特定のコンポーネントのすべての接続が記述された接続リストです。  

1. コンポーネントの一覧で、方向キーを使用してコンポーネントを切り替えます。
1. ターゲット コンポーネントの接続リストを開くには、Tab キーを使用します。
1. コンポーネントの接続ポートを切り替えるには、方向キーを使用します。
1. ターゲット コンポーネントに移動するには、"G" キーを使用します。

## <a name="edit-the-pipeline-graph"></a>パイプライン グラフを編集する

### <a name="add-a-component-to-the-graph"></a>グラフへのコンポーネントの追加

1. キャンバスからコンポーネント ツリーにフォーカスを切り替えるには、Ctrl + F6 キーを使用します。
1. 標準のツリービュー コントロールを使用して、コンポーネント ツリーで目的のコンポーネントを検索します。

### <a name="edit-a-component"></a>コンポーネントの編集

コンポーネントを別のコンポーネントに接続するには、次のようにします。

1. コンポーネント リスト内のターゲット コンポーネントで Ctrl + Shift + H キーを使用し、接続ヘルパーを開きます。
1. そのコンポーネントの接続ポートを編集します。

コンポーネントのプロパティを調整するには、次の手順を実行します。

1. コンポーネントを対象とする場合は、Ctrl + Shift + E キーを使用して、コンポーネントのプロパティを開きます。
1. コンポーネントのプロパティを編集します。

## <a name="navigation-shortcuts"></a>ナビゲーション ショートカット

| キー操作 | 説明 |
|-|-|
| Ctrl + F6 | キャンバスとコンポーネント ツリーの間でフォーカスを切り替える |
| Ctrl + F1   | コンポーネント ツリーのノードにフォーカスがあるときに情報カードを開く |
| Ctrl + Shift + H | ノードにフォーカスがあるときに接続ヘルパーを開く |
| Ctrl + Shift + E | ノードにフォーカスがあるときにコンポーネントのプロパティを開く |
| Ctrl + G | パイプラインの実行が失敗したときに、最初の失敗したノードにフォーカスを移動する |

## <a name="action-shortcuts"></a>アクションのショートカット

次のショートカットをアクセス キーと共に使用します。 アクセス キーの詳細については、https://en.wikipedia.org/wiki/Access_key を参照してください。

| キー操作 | アクション |
|-|-|
| アクセス キー + R | 実行 |
| アクセス キー + P | 発行 |
| アクセス キー + C | 複製 |
| アクセス キー + D | 配置 |
| アクセス キー + I | 推論パイプラインを作成、更新する |
| アクセス キー + B | バッチ推論パイプラインを作成、更新する |
| アクセス キー + K | [Create inference pipeline]\(推論パイプラインの作成\) ドロップダウンを開く |
| アクセス キー + U | [Update inference pipeline]\(推論パイプラインの更新\) ドロップダウンを開く |
| アクセス キー + M | [その他] (...) ドロップダウンを開く |

## <a name="next-steps"></a>次のステップ

- [ハイコントラストの有効化またはテーマの変更](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Microsoft のユーザー補助関連のツール](https://www.microsoft.com/accessibility)
