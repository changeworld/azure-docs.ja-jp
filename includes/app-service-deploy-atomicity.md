---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836803"
---
## <a name="what-happens-to-my-app-during-deployment"></a>使用しているアプリはデプロイ中にどうなりますか?

正式にサポートされているすべてのデプロイ方法は、アプリの `/home/site/wwwroot` フォルダー内のファイルに変更を加えます。 これらのファイルは、運用環境で実行されているのと同じファイルです。 したがって、ファイルがロックされているため、デプロイに失敗する可能性があります。 すべてのファイルが同時に更新されるわけではないため、運用環境のアプリはデプロイ中に予期しない動作をすることもあります。 これらの問題を回避するにはいくつかの方法があります。

- デプロイ中に、アプリを停止するか、またはアプリのオフライン モードを有効にします。 詳細については、「[Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)」 (デプロイ中にロックされているファイルに対処する) を参照してください。
- [自動スワップ](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)を有効にした状態で[ステージング スロット](../articles/app-service/deploy-staging-slots.md)にデプロイします。 
- 継続的デプロイではなく、[パッケージから実行](https://github.com/Azure/app-service-announcements/issues/84)を使用します。
