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
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075589"
---
この手順では、Azure サブスクリプションの資格情報が必要です。

Azure File Sync を構成するためのコア リソースは、"*ストレージ同期サービス*" と呼ばれます。 同じファイル セットを今すぐ、または今後同期するすべてのサーバーに対して、1 つのみをデプロイすることをお勧めします。 データを交換する必要のない個別のサーバー セットがある場合のみ、複数のストレージ同期サービスを作成します。 たとえば、同じ Azure ファイル共有を同期しないようにする必要があるサーバーがある場合です。 それ以外の場合は、1 つのストレージ同期サービスをお勧めします。

ストレージ同期サービスには、自分の場所に近い Azure リージョンを選択します。 他のすべてのクラウド リソースは、同じリージョンにデプロイする必要があります。 管理が簡単になるように、サブスクリプションに新しいリソース グループを作成し、同期リソースとストレージ リソースを格納します。

詳細については、「Azure File Sync のデプロイ」の記事にある[ストレージ同期サービスのデプロイに関するセクション](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)を参照してください。記事のこの部分のみに従います。 後の手順に、この記事の他のセクションへのリンクがあります。