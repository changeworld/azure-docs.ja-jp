---
title: Fluid Framework リリースとのバージョンの互換性
description: >
  Azure Fluid Relay サービスと互換性のある Fluid Framework リリースのバージョンを確認する方法。
services: azure-fluid
author: tylerbutler
ms.author: tylerbu
ms.date: 09/28/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 62493eeb270a171fe874a877dfd51ac404eaab27
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661629"
---
# <a name="version-compatibility-with-fluid-framework-releases"></a>Fluid Framework リリースとのバージョンの互換性

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

アプリケーションを Azure Fluid Relay サービスに接続するには、 **@fluidframework/azure-client** ライブラリを使用します。 また、**fluid-framework** ライブラリを使用して、Fluid Framework によって提供されるコア データ構造を使用することもできます。

Azure Fluid Relay を使用しているため、最初に利用可能な最新バージョンの @fluidframework/azure-client をインストールし、そのバージョンを使用して、使用する fluid-framework ライブラリのバージョンを決定する必要があります。 このライブラリは、依存する fluid-framework パッケージのバージョンに対する "*ピア依存関係*" を表します。

[install-peerdeps](https://www.npmjs.com/package/install-peerdeps) ツールを使用すると、次のコマンドを使用して @fluidframework/azure-client と、互換性のあるバージョンの fluid-framework の両方をインストールできます。

```bash
npx install-peerdeps @fluidframework/azure-client
```

> [!TIP]
> パブリック プレビューの段階では、 **@fluidframework/azure-client** と **fluid-framework** のバージョンは一致します。 つまり、 **@fluidframework/azure-client** の現在のリリースが 0.48 の場合、**fluid-framework** 0.48 と互換性があります。 逆もまた真です。

## <a name="compatibility-table"></a>互換性の表

| npm パッケージ                         | 最小バージョン | API                                                              |
| ----------------------------------  | :-------------- | :--------------------------------------------------------------- |
| @fluidframework/azure-client        | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-client/)        |
| fluid-framework                     | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/fluid-framework/)     |
| @fluidframework/azure-service-utils | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-service-utils/) |
| @fluidframework/test-client-utils   | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/test-client-utils/)   |

[0.48.4]: https://fluidframework.com/docs/updates/v0.48/

## <a name="next-steps"></a>次の手順

- [Azure Fluid Relay サービスをプロビジョニングする](../how-tos/connect-fluid-azure-service.md)
- [Azure Fluid Relay サービスに接続する](../how-tos/connect-fluid-azure-service.md)
- [ローカル テストに AzureClient を使用する](../how-tos/local-mode-with-azure-client.md)
