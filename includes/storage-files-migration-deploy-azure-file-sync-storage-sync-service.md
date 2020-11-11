---
title: ストレージ同期サービスをデプロイする
description: Azure File Sync クラウド リソース (ストレージ同期サービス) をデプロイします。 移行ドキュメント間で共有される一般的なテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043184"
---
この手順では、Azure サブスクリプションの資格情報が必要です。

Azure File Sync を構成するためのコア リソースは、" *ストレージ同期サービス* " と呼ばれます。 同じファイル セットを今すぐ、または今後同期するすべてのサーバーに対して、1 つのみをデプロイすることをお勧めします。 データを交換する必要のない個別のサーバー セットがある場合のみ、複数のストレージ同期サービスを作成します。 たとえば、同じ Azure ファイル共有を同期しないようにする必要があるサーバーがある場合です。 それ以外の場合は、1 つのストレージ同期サービスをお勧めします。

ストレージ同期サービスには、自分の場所に近い Azure リージョンを選択します。 他のすべてのクラウド リソースは、同じリージョンにデプロイする必要があります。 管理が簡単になるように、サブスクリプションに新しいリソース グループを作成し、同期リソースとストレージ リソースを格納します。

詳細については、「Azure File Sync のデプロイ」の記事にある[ストレージ同期サービスのデプロイに関するセクション](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)を参照してください。記事のこの部分のみに従います。 後の手順に、この記事の他のセクションへのリンクがあります。