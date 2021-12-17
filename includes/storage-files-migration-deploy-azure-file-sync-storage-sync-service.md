---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 180b615869579752f9a14aa2dcdd34f1676b577b
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2021
ms.locfileid: "109645263"
---
この手順を完了するには、Azure サブスクリプションの資格情報が必要です。

Azure File Sync を構成するためのコア リソースは、"*ストレージ同期サービス*" と呼ばれます。 同じファイル セットを今すぐ、または今後同期するすべてのサーバーに対して、1 つのみをデプロイすることをお勧めします。 データを交換する必要のない個別のサーバー セットがある場合のみ、複数のストレージ同期サービスを作成します。 たとえば、同じ Azure ファイル共有を同期しないようにする必要があるサーバーがある場合です。 それ以外の場合は、1 つのストレージ同期サービスを使用することをお勧めします。

ストレージ同期サービスには、自分の場所に近い Azure リージョンを選択します。 他のすべてのクラウド リソースは、同じリージョンにデプロイする必要があります。 管理が簡単になるように、サブスクリプションに新しいリソース グループを作成し、同期リソースとストレージ リソースを格納します。

詳細については、「Azure File Sync のデプロイ」の記事にある[ストレージ同期サービスのデプロイに関するセクション](../articles/storage/file-sync/file-sync-deployment-guide.md#deploy-the-storage-sync-service)を参照してください。記事のこのセクションのみに従います。 後の手順に、この記事の他のセクションへのリンクがあります。