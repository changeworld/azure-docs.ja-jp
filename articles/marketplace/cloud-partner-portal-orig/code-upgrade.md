---
title: 最新プラットフォームへのコードのアップグレード | Microsoft Docs
description: このトピックでは、Microsoft Dynamics 365 for Operations プラットフォームのバージョンを最新のプラットフォーム リリースにアップグレードする方法を説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808016"
---
# <a name="upgrading-code-to-the-latest-platform"></a>最新プラットフォームへのコードのアップグレード

この記事では、Microsoft Dynamics 365 for Operations プラットフォームのバージョンを最新のプラットフォーム リリースにアップグレードする方法を説明します。

## <a name="overview"></a>概要

Microsoft Dynamics 365 for Operations プラットフォームは、次のコンポーネントで構成されます。

Dynamics 365 for Operations プラットフォーム バイナリ (Application Object Server (AOS)、Data Management Framework、レポート/ビジネス インテリジェンス (BI) フレームワーク、開発ツール、分析サービスなど)。 次のアプリケーション オブジェクト ツリー (AOT) パッケージ。

1. アプリケーション プラットフォーム
2. アプリケーション基準
3. Test Essentials

**"重要"**: 最新の Dynamics 365 for Operations プラットフォームに移行するには、お使いの Dynamics 365 for Operations の実装に、プラットフォームに属する AOT パッケージのカスタマイズ (オーバーレイヤー) が含まれていないことが必要です。 この制限は、プラットフォームに対してシームレスで継続的な更新ができるようにするために、プラットフォーム更新プログラム 3 で導入されました。 プラットフォーム更新プログラム 3 よりも以前のプラットフォーム上で実行している場合は、この記事の最後にある、以前のビルドからプラットフォーム更新プログラム 3 へのアップグレードに関するセクションを参照してください。

コードのアップグレードの詳細については、[こちら](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update)をご覧ください。