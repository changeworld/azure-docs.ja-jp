---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236471"
---
## <a name="what-happens-to-my-app-during-deployment"></a>使用しているアプリはデプロイ中にどうなりますか?

正式にサポートされているすべてのデプロイ方法には、アプリの `/site/home/wwwroot` フォルダー内のファイルに変更を加えるという 1 つの共通点があります。 これらは、運用環境で実行されているのと同じファイルです。 そのため、ロックされたファイルが原因でデプロイが失敗する可能性があります。あるいは、すべてのファイルが同時に更新されるわけではないために、デプロイ中に運用環境のアプリが予期しない動作をする場合があります。 これらの問題を回避するにはいくつかの方法があります。

- デプロイ中に、アプリを停止するか、またはアプリのオフライン モードを有効にします。 詳細については、「[Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)」 (デプロイ中にロックされているファイルに対処する) を参照してください。
- [自動スワップ](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap)を有効にした状態で[ステージング スロット](../articles/app-service/web-sites-staged-publishing.md)にデプロイします。 
- [Run-from-Zip](https://github.com/Azure/app-service-announcements/issues/84) を代わりに使用します。
