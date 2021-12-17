---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: eff2a7eda24834389db7afc0a689767f20da045f
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423376"
---
## <a name="what-happens-to-my-app-during-deployment"></a>使用しているアプリはデプロイ中にどうなりますか?

正式にサポートされているすべてのデプロイ メソッドは、アプリの */home/site/wwwroot* フォルダー内のファイルに変更を加えます。 アプリの実行には、それらのファイルが使用されます。 したがって、ファイルがロックされていることにより、デプロイに失敗する可能性があります。 すべてのファイルが同時に更新されるわけではないため、アプリはデプロイ中に予期しない動作をすることもあります。 この動作は、顧客向けのアプリでは好ましくありません。 これらの問題を回避するにはいくつかの方法があります。

- ZIP パッケージ を展開せず、[ZIP パッケージから直接アプリを実行](../articles/app-service/deploy-run-package.md)します。
- デプロイ中に、アプリを停止するか、またはアプリのオフライン モードを有効にします。 詳細については、「[Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)」 (デプロイ中にロックされているファイルに対処する) を参照してください。
- [自動スワップ](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)を有効にした状態で、[ステージング スロット](../articles/app-service/deploy-staging-slots.md)にデプロイします。 
