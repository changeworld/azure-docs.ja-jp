---
title: 最新のプラットフォームへのコードのアップグレード | Azure Marketplace
description: このトピックでは、Microsoft Dynamics 365 for Operations プラットフォームのバージョンを最新のプラットフォーム リリースにアップグレードする方法を説明します。
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935283"
---
# <a name="upgrading-code-to-the-latest-platform"></a>最新プラットフォームへのコードのアップグレード

この記事では、Microsoft Dynamics 365 for Operations プラットフォームのバージョンを最新のプラットフォーム リリースにアップグレードする方法を説明します。

## <a name="overview"></a>概要

Microsoft Dynamics 365 for Operations プラットフォームは、次のコンポーネントで構成されます。

Dynamics 365 for Operations プラットフォーム バイナリ (Application Object Server (AOS)、Data Management Framework、レポート/ビジネス インテリジェンス (BI) フレームワーク、開発ツール、分析サービスなど)。 次のアプリケーション オブジェクト ツリー (AOT) パッケージ。

1. アプリケーション プラットフォーム
2. アプリケーション基準
3. Test Essentials

**重要**:最新の Dynamics 365 for Operations プラットフォームに移行するには、Dynamics 365 for Operations の実装では、そのプラットフォームに属するどの AOT パッケージにもカスタマイズ (オーバーレイヤー) を加えることはできません。 この制限は、プラットフォームに対してシームレスで継続的な更新ができるようにするために、プラットフォーム更新プログラム 3 で導入されました。 プラットフォーム更新プログラム 3 よりも以前のプラットフォーム上で実行している場合は、この記事の最後にある、以前のビルドからプラットフォーム更新プログラム 3 へのアップグレードに関するセクションを参照してください。

コードのアップグレードの詳細については、[こちら](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update)をご覧ください。