---
title: レンダリング機能の使用 - Azure Batch
description: Azure Batch レンダリングの機能を使用する方法。 直接またはクライアント アプリケーション プラグインから呼び出して、Batch Explorer アプリケーションの使用を試みます。
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: f3b2e641ab187514a7900b2ab7cc75068df00252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672008"
---
# <a name="using-azure-batch-rendering"></a>Azure Batch レンダリングを使用する

Azure Batch のレンダリングを使用する方法はいくつかあります。

* API:
  * Batch の API のいずれかを使用してコードを記述します。  開発者は、クラウドかオンプレミス ベースかにかかわらず、既存のアプリケーションまたはワークフローに Azure Batch 機能を統合できます。
* コマンド ライン ツール:
  * [Azure コマンド ライン](https://docs.microsoft.com/cli/azure/)または [PowerShell](https://docs.microsoft.com/powershell/azure/overview) を使用すると、Batch の使用のスクリプトを作成できます。
  * 特に、[Batch CLI テンプレートのサポート](https://docs.microsoft.com/azure/batch/batch-cli-templates)により、プールの作成とジョブの送信がかなり容易になっています。
* Batch Explorer UI:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) は Batch のアカウントも管理および監視できるクロス プラットフォームのクライアント ツールです。
  * 各レンダリング アプリケーションには、簡単にプールを作成してジョブを送信できる数多くのプール テンプレートやジョブ テンプレートが用意されています。  一連のテンプレートはアプリケーション UI に表示され、テンプレート ファイルは GitHub からアクセスできます。
  * カスタム テンプレートを一から作成することも、GitHub から提供されているテンプレートをコピーして編集することもできます。
* クライアント アプリケーションのプラグイン:
  * Batch レンダリングをクライアント設計およびモデリング アプリケーション内から直接使えるようにするプラグインを使用できます。  このプラグインでは、主に現在の 3D モデルに関するコンテキスト情報が含まれる Batch Explorer アプリケーションが呼び出され、アセットの管理をサポートする機能が含まれています。

開発者でも Azure のエキスパートでもないエンド ユーザーにとって Azure Batch レンダリングを試す最適な方法および最も簡単な方法は、直接またはクライアント アプリケーションのプラグインから呼び出される Batch Explorer アプリケーションを使用する方法です。

## <a name="using-batch-explorer"></a>Batch Explorer を使用する

Batch Explorer を使用してレンダリングを実行するステップ バイ ステップ チュートリアルについては、[Blender のチュートリアル](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)をご覧ください。

### <a name="download-and-install"></a>ダウンロードおよびインストールする

Windows、OSX および Linux 用の Batch Explorer の[ダウンロードが用意されています](https://azure.github.io/BatchExplorer/)。

### <a name="using-templates-to-create-pools-and-run-jobs"></a>テンプレートを使用してプールを作成してジョブを実行する

プール、ジョブ、およびタスクを作成するためのすべてのプロパティを指定することなく、Batch で直接各種レンダリング アプリケーションのプールを簡単に作成してジョブを実行できる、テンプレートの全セットを Batch Explorer で使用できます。  Batch Explorer で利用できるテンプレートは [GitHub のリポジトリ](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)に格納および表示されます。

![Batch Explorer ギャラリー](./media/batch-rendering-using/batch-explorer-gallery.png)

VM イメージをレンダリングする Marketplace 上に存在するすべてのアプリケーションの要求に応じたテンプレートが用意されています。  各アプリケーションには CPU プールや GPU プール、Windows や Linux のプールの要求に応じたプールのテンプレートを含む、複数のテンプレートが存在します。ジョブ テンプレートにはフル フレームまたはタイル Blender レンダリングおよび V-Ray 分散レンダリングが含まれています。 提供されるテンプレートは時間の経過と共に、プールの自動スケーリングなど、他の Batch 機能の要求に応じるように展開されます。

また、一から作成または提供されたテンプレートを編集することで、カスタムテンプレートを生成することもできます。 カスタム テンプレートは、Batch Explorer の [ギャラリー] セクションの [ローカル テンプレート] の項目を選択することで使用できます。

### <a name="file-system-and-data-movement"></a>ファイル システムとデータ移動

Batch Explorer の [データ] セクションでは、ローカル ファイル システムと Azure Storage アカウント間でファイルをコピーできます。

## <a name="client-application-plug-ins"></a>クライアント アプリケーションのプラグイン

一部のクライアント アプリケーションにはプラグインが用意されています。  プラグインを使用すると、アプリケーションから直接プールやジョブを作成または Batch Explorer を呼び出すことができます。

* [Blender 2.79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Blender 2.8 以降](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>次のステップ

Batch レンダリングの例については、次の 2 つのチュートリアルをお試しください。

* [Azure CLI を使用したレンダリング](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Batch Explorer を使用したレンダリング](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)