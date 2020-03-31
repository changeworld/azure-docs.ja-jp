---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597834"
---
Azure ファイル共有は、ストレージの共有プールを表す最上位オブジェクトである "*ストレージ アカウント*" にデプロイされます。 このストレージのプールを使用すると、複数のファイル共有だけでなく、BLOB コンテナー、キュー、テーブルなどの他のストレージ リソースもデプロイできます。 ストレージ アカウントにデプロイされているすべてのストレージ リソースにより、そのストレージ アカウントに適用される制限が共有されます。 ストレージ アカウントの現在の制限を確認するには、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../articles/storage/files/storage-files-scale-targets.md)」をご覧ください。

Azure Files のデプロイに使用するストレージ アカウントには、主に次の 2 種類があります。 
- **汎用バージョン 2 (GPv2) ストレージ アカウント**: GPv2 ストレージ アカウントを使うと、Standard のハード ディスク ベース (HDD ベース) のハードウェアに、Azure ファイル共有をデプロイできます。 GPv2 ストレージ アカウントでは、Azure ファイル共有を格納するだけでなく、BLOB コンテナー、キュー、テーブルなどの他のストレージ リソースを格納することもできます。 
- **FileStorage ストレージ アカウント**:FileStorage ストレージ アカウントを使うと、Premium のソリッドステート ディスク ベース (SSD ベース) のハードウェアに、Azure ファイル共有をデプロイできます。 FileStorage アカウントは、Azure ファイル共有を格納するためにのみ使用できます。FileStorage アカウントでその他のストレージ リソース (BLOB コンテナー、キュー、テーブルなど) をデプロイすることはできません。

Azure portal、PowerShell、CLI では、他にもいくつかの種類のストレージ アカウントを使用できます。 BlockBlobStorage と BlobStorage の 2 種類のストレージ アカウントには、Azure ファイル共有を格納できません。 他の 2 つのストレージ アカウントの種類は、汎用バージョン 1 (GPv1) ストレージ アカウントと従来のストレージ アカウントであり、どちらも Azure ファイル共有を格納できます。 GPv1 と従来のストレージ アカウントには Azure ファイル共有を格納できますが、Azure Files のほとんどの新機能は、GPv2 ストレージ アカウントと FileStorage ストレージ アカウントでのみ使用できます。 そのため、新しいデプロイには GPv2 と FileStorage ストレージ アカウントのみを使用し、GPv1 と従来のストレージ アカウントが環境に既に存在する場合はアップグレードすることをお勧めします。  