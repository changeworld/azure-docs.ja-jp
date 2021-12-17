---
title: Azure Fluid Relay アーキテクチャの概要
description: Azure Fluid Relay アーキテクチャの概要
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/overview/
ms.openlocfilehash: 6b58c90692a7d4eba404e4d5da84b175d229f343
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661582"
---
# <a name="overview-of-azure-fluid-relay-architecture"></a>Azure Fluid Relay アーキテクチャの概要

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

Fluid を使用してアプリケーションを構築する際に理解する必要がある主な概念は 3 つあります。

- サービス
- コンテナー
- 共有オブジェクト

## <a name="service"></a>サービス

Fluid クライアントには、接続されているすべてのクライアントが操作の送受信に使用する一元化されたサービスが必要です。 アプリケーションで Fluid を使用する場合は、接続先の基になるサービスに対応した正しいパッケージを使用する必要があります。

Azure Fluid Relay サービスの場合、このパッケージは **@fluidframework/azure-client** です。 このパッケージは、Azure Fluid Relay を介して Azure でホストされている Fluid コンテナーを作成して読み込むのに役立ちます。

## <a name="container"></a>コンテナー

**コンテナー** は、Fluid でのカプセル化の主要な単位です。 共有オブジェクトのコレクションと、コンテナーとその内部のオブジェクトのライフサイクルを管理するためのサポート API で構成されます。

新しいコンテナーの作成はクライアント駆動型のアクションであり、コンテナーの有効期間はサポート サーバーの格納データにバインドされます。 既存のコンテナーを取得する場合は、コンテナーの以前の状態を考慮することが重要です。

コンテナーの詳細については、fluidframework.com の[コンテナー](https://fluidframework.com/docs/build/containers/)に関するページを参照してください。

## <a name="shared-objects"></a>共有オブジェクト

**共有オブジェクト** は、特定の API を公開することでコラボレーション データを強化するオブジェクトの種類です。 多くの共有オブジェクトは、コンテナーのコンテキスト内に存在し、静的または動的に作成できます。 **分散データ構造 (DDSes)** と **DataObjects** は、どちらも共有オブジェクトの種類です。

詳細については、fluidframework.com の[データ モデリング](https://fluidframework.com/docs/build/data-modeling/)に関するページを参照してください。

## <a name="package-structure"></a>パッケージ構造

Fluid での構築時に使用する主な **パッケージ** は 2 つあります。 **fluid-framework** パッケージと、**azure-client** のようなサービス固有のクライアント パッケージ。

詳細については、fluidframework.com の[パッケージ](https://fluidframework.com/docs/build/packages/)に関するページを参照してください。

### <a name="the-fluid-framework-package"></a>fluid-framework パッケージ

**fluid-framework** パッケージは、アプリケーションの構築と使用を容易にするコア Fluid API のコレクションです。 このパッケージには、すべての共通型定義と、すべてのプリミティブ共有オブジェクトが含まれています。

### <a name="the-fluidframeworkazure-client-package"></a>@fluidframework/azure-client パッケージ

**@fluidframework/azure-client** パッケージによって、Azure Fluid Relay サービス インスタンスに接続して Fluid コンテナーを作成および読み込むための API が提供されます。 この API の使用方法について詳しくは、「[方法: Azure Fluid Relay サービスに接続する](../how-tos/connect-fluid-azure-service.md)」を参照してください。
