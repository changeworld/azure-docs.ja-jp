---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/13/2021
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ae085270a28bfd27542585d5df6c1dd918943d9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421640"
---
| リソース | 説明|
|----------|------------|
| **イメージのソース** | これは、ギャラリーに **イメージ バージョン** を作成するために使用できるリソースです。 イメージのソースには、[一般化または特殊化された](../shared-image-galleries.md#generalized-and-specialized-images)既存の Azure VM、マネージド イメージ、スナップショット、または別のギャラリー内のイメージ バージョンを使用できます。 |
| **[ギャラリー]** | Azure Marketplace と同様に、**ギャラリー** は、イメージと [VM アプリケーション](../vm-applications.md)を管理して共有するためのリポジトリです。ただし、だれがアクセスできるかは、ユーザーが制御します。 |
| **イメージ定義** | イメージ定義はギャラリー内に作成され、内部で使用するためにイメージと要件に関する情報を伝達します。 この情報には、イメージが Windows または Linux のどちらか、リリース ノート、および最小と最大のメモリ要件が含まれます。 これは、イメージの種類の定義です。 |
| **イメージ バージョン** | **イメージ バージョン** は、ギャラリーを利用している場合に、VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 マネージド イメージのように、**イメージ バージョン** を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。 |