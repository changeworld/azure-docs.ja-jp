---
title: ストレージ同期サービスをデプロイする
description: Azure File Sync クラウド リソースのデプロイ。 ストレージ同期サービス。 移行ドキュメント間で共有する共通のテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209290"
---
この手順では、Azure サブスクリプションの資格情報が必要です。 使用する Azure サブスクリプションは、StorSimple に使用するものとは異なる場合があります。

Azure File Sync を構成するためのコア リソースは、"ストレージ同期サービス" と呼ばれます。
社内の同じファイル セットを今すぐ、または今後同期するすべてのサーバーについて、1 つのみをデプロイすることをお勧めします。 複数の StorSimple アプライアンスがある場合は、それぞれに対してストレージ同期サービス リソースを作成することを検討できます。 ただし、データを交換する必要のない個別のサーバー セットがある場合は、複数のストレージ同期サービスを作成するだけで済みます。 それ以外の場合は、1 つのストレージ同期サービスがベスト プラクティスです。

ストレージ同期サービスには、オフィスの場所に近いの Azure リージョンを選択します。 他のすべてのクラウド リソースは、同じリージョンにデプロイする必要があります。
ベスト プラクティスとして、管理が簡単になるように、サブスクリプションに新しいリソース グループを作成し、同期リソースとストレージ リソースを格納することをお勧めします。

次の記事では、ストレージ同期サービスをデプロイする方法について説明します。 ドキュメントのこの部分にのみ従ってください。後の手順に、このドキュメントの他のサブセクションへのリンクがあります。

[ストレージ同期サービスをデプロイする方法を確認します。](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
