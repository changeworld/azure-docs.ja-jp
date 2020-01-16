---
title: Jupyter ノートブックでパッケージをインストールする - Azure Notebooks プレビュー
description: Azure で実行している Jupyter ノートブック内から Python、R、F# パッケージをインストールする方法について説明します。
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646230"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Azure Notebooks プレビュー内からパッケージをインストールする

[プロジェクト レベルでノートブックの環境](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)を構成できますが、個々のノートブック内から直接、パッケージをインストールすると便利な場合があります。

ノートブックからインストールされたパッケージは、現在のサーバー セッションにのみ適用されます。 サーバーをシャットダウンすると、パッケージのインストールは保存されません。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

Python のパッケージは pip または conda でインストールできます。その際、コード セル内でコマンドを使用します。

```bash
!pip install <package_name>

!conda install <package_name> -y
```

要件が既に満たされていることがコマンドの出力から確認できた場合、Azure Notebooks には既定でパッケージが含まれている可能性があります。 パッケージは、[プロジェクト環境設定手順](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)によってインストールされることもあります。

## <a name="r"></a>R

R のパッケージは、コード セルの `install.packages` 関数を利用し、CRAN または GitHub からインストールできます。

```r
install.packages("package_name")
```

DevTools ライブラリを使用し、GitHub からリリース前のバージョンやその他の開発パッケージをインストールすることもできます。

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

F# のパッケージは、コード セル内からパケット依存関係マネージャーを呼び出すことで、[nuget.org](https://www.nuget.org) からインストールできます。 最初にパケット マネージャーを読み込みます。

```fsharp
#load "Paket.fsx"
```

次にパッケージをインストールします。

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

次に、パケット ジェネレーターを読み込みます。
```fsharp
#load "Paket.Generated.Refs.fsx"
```

ライブラリを開きます。
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>次のステップ

- [方法: プロジェクトの構成と管理](configure-manage-azure-notebooks-projects.md)
- [方法: スライド ショーの表示](present-jupyter-notebooks-slideshow.md)
