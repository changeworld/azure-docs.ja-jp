---
title: ストレージ同期サービスをデプロイする
description: Azure File Sync クラウド リソース (ストレージ同期サービス) のデプロイ。 移行ドキュメント間で共有される一般的なテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143582"
---
この手順では、Azure サブスクリプションの資格情報が必要です。

Azure File Sync を構成するためのコア リソースは、"*ストレージ同期サービス*" と呼ばれます。 同じファイル セットを今すぐ、または今後同期するすべてのサーバーに対して、1 つのみをデプロイすることをお勧めします。 データを交換する必要のない個別のサーバー セットがある場合にのみ (例: 同じ Azure ファイル共有の同期)、複数のストレージ同期サービスを作成します。 それ以外の場合は、1 つのストレージ同期サービスをお勧めします。

ストレージ同期サービスには、自分の場所に近い Azure リージョンを選択します。 他のすべてのクラウド リソースは、同じリージョンにデプロイする必要があります。
管理が簡単になるように、サブスクリプションに新しいリソース グループを作成し、同期リソースとストレージ リソースを格納します。

詳細については、「Azure File Sync のデプロイ」の記事にある[ストレージ同期サービスのデプロイに関するセクション](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)を参照してください。記事のこの部分のみに従います。 後の手順に、この記事の他のセクションへのリンクがあります。