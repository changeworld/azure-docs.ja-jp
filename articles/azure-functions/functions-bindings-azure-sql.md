---
title: Functions の Azure SQL バインド
description: Azure Functions で Azure SQL のバインドを使用する方法について説明します。
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: b5054da620e67c38b427c12956d73727bf23d519
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715380"
---
# <a name="azure-sql-bindings-for-azure-functions-overview-preview"></a>Azure Functions における Azure SQL バインドの概要 (プレビュー)

この一連の記事では、Azure Functions で [Azure SQL](../azure-sql/index.yml) バインドを操作する方法について説明します。 Azure Functions は、Azure SQL 製品と SQL Server 製品の入力バインドと出力バインドをサポートしています。

| アクション | Type |
|---------|---------|
| データベースからデータを読み取る | [入力バインド](./functions-bindings-azure-sql-input.md) |
| データベースにデータを保存する |[出力バインド](./functions-bindings-azure-sql-output.md) |

> [!NOTE]
> このリファレンスは、[Azure Functions のバージョン 2.x 以降](functions-versions.md)を対象としています。 
>
> このバインドでは、Azure SQL または SQL Server データベースに接続する必要があります。

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions"></a>関数

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| 言語                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [プレビュー NuGet パッケージ]をインストールする | |
<!--| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 | -->


[プレビュー NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Sql
[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack


## <a name="known-issues"></a>既知の問題

- データ型 `NTEXT`、`TEXT`、`IMAGE` の列ではテーブルに対して出力バインドできません。データのアップサートは失敗します。 これらの型は将来のバージョンの SQL Server で[削除され](/sql/t-sql/data-types/ntext-text-and-image-transact-sql)、この Azure Functions バインドで使用される `OPENJSON` 関数とは互換性がありません。
- 大文字と小文字を区別する[照合順序](/sql/relational-databases/collations/collation-and-unicode-support#Collation_Defn)は現在、サポートされていません。 [GitHub item #133](https://github.com/Azure/azure-functions-sql-extension/issues/133) では、この問題の進捗状況を追跡しています。


## <a name="open-source"></a>ソースを開く

Azure Functions の Azure SQL バインドはオープンソースであり、[https://github.com/Azure/azure-functions-sql-extension](https://github.com/Azure/azure-functions-sql-extension) のリポジトリで入手できます。


## <a name="next-steps"></a>次の手順

- [データベースからデータを読み取る (入力バインド)](./functions-bindings-azure-sql-input.md)
- [データベースにデータを保存する (出力バインド)](./functions-bindings-azure-sql-output.md)