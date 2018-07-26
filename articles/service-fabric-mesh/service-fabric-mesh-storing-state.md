---
title: Azure Service Fabric Mesh の状態ストレージ オプション | Microsoft Docs
description: Azure Service Fabric Mesh 上で実行されている Service Fabric Mesh アプリケーションに状態を確実に格納する方法について説明します。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075939"
---
# <a name="state-management-with-service-fabric"></a>Service Fabric を使用した状態の管理
Service Fabric では、状態ストレージのさまざまなオプションをサポートします。 状態管理のパターンと Service Fabric の概念的概要については、[Service Fabric の概念の中の、状態](/azure/service-fabric/service-fabric-concepts-state)に関する記事を参照してください。 サービスが実行されているのが Service Fabric Mesh の中か外にかかわらず、これらと同じ概念が適用されます。 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Azure Service Fabric Mesh の状態ストレージ オプション
Service Fabric Mesh を使用することにより、新しいアプリケーションを簡単にデプロイし、Azure 上でホストされている既存のデータ ストアに接続できます。 任意のリモート データベースを使用するだけでなく、サービスで使用するローカルまたはリモートのストレージに応じて、データを格納するためのいくつかのオプションがあります。 

* ローカルに保存されたレプリケート済みデータ
  * Reliable Collection (プレビューでは使用できません)
    * サービスで使用するキーおよびキー値ペアのようなデータ構造を実装するライブラリ
    * これは、簡単にパーティション ルーティングと Service Fabric Mesh のインテリジェント ルーティングを提供しながら、データと交信する最も簡単で高速な方法です。
  * Service Fabric のボリューム ドライバー (プレビューでは使用できません)
    * コンテナーにローカル ボリュームをマウントする Docker ボリューム ドライバー
    * これにより、ファイル ストレージをサポートするあらゆる API を通して、データをローカルに保存でき、最大限の柔軟性が提供されます。

* リモート ストレージ
  * Azure Files ボリューム ドライバー
    * コンテナーに Azure Files 共有をマウントする Docker ボリューム ドライバー
    * パフォーマンスは下がりますが、ファイル ストレージをサポートするあらゆる API を通して、高い柔軟性と信頼性のデータ オプションを低価格で提供します。
    * [ハウツー ガイド: Azure Files ボリュームでアプリを展開する](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>次の手順

アプリケーション モデルについては、[Service Fabric のリソース](service-fabric-mesh-service-fabric-resources.md)に関する記事を参照してください。
