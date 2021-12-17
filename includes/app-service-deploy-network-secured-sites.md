---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: jasonfreeberg
ms.service: app-service
ms.topic: include
ms.date: 08/27/2021
ms.author: jafreebe
ms.custom: include file
ms.openlocfilehash: a3030eedcc6b00457338f4a71c27965261280a80
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225434"
---
Web アプリのネットワーク構成によっては、ローカル環境からサイトへの直接アクセスがブロックされる場合があります。 このシナリオでコードをデプロイするには、Web アプリからアクセス可能なストレージ システムに ZIP を公開し、Web アプリに ZIP を *push* するのではなく、ストレージの場所から ZIP を *pull* するようにアプリをトリガーするとよいでしょう。 詳細は、「[本アーティクルのネットワークで保護された Web アプリへのデプロイ」](https://azure.github.io/AppService/2021/03/01/deploying-to-network-secured-sites-2.html)を参照してください。 
