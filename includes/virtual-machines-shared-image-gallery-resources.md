---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015998"
---
| リソース | 説明|
|----------|------------|
| **イメージのソース** | これは、イメージ ギャラリーに **イメージ バージョン** を作成するために使用できるリソースです。 イメージのソースには、[一般化または特殊化された](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images)既存の Azure VM、マネージド イメージ、スナップショット、または別のイメージ ギャラリー内のイメージ バージョンを使用できます。 |
| **イメージ ギャラリー** | Azure Marketplace などの **イメージ ギャラリー** は、イメージを管理して共有するためのリポジトリです。ただし、アクセス権の所有者を制御します。 |
| **イメージ定義** | イメージ定義はギャラリー内に作成され、内部で使用するためにイメージと要件に関する情報を伝達します。 この情報には、イメージが Windows または Linux のどちらか、リリース ノート、および最小と最大のメモリ要件が含まれます。 これは、イメージの種類の定義です。 |
| **イメージ バージョン** | **イメージ バージョン** は、ギャラリーを利用している場合に、VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 マネージド イメージのように、**イメージ バージョン** を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。 |