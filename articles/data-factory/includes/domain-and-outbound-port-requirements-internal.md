---
title: インクルード ファイル
description: インクルード ファイル
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 09f61060f9a76036f5e2d58512ddde9248402bbd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638979"
---
| ドメイン名                                          | 送信ポート | 説明                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| パブリック クラウド: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> 中国: `*.servicebus.chinacloudapi.cn`   | 443            | セルフホステッド統合ランタイムがインタラクティブな作成を行うために必要です。 |
| パブリック クラウド: `{datafactory}.{region}.datafactory.azure.net`<br> または `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 中国: `{datafactory}.{region}.datafactory.azure.cn` | 443            | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 <br>パブリック クラウドに新しく作成された Data Factory の場合は、{datafactory}.{region}.datafactory.azure.net という形式のセルフホステッド統合ランタイム キーから FQDN を見つけてください。 古いデータ ファクトリの場合、セルフホステッド統合キーに FQDN が表示されないときは、代わりに *.frontend.clouddatahub.net を使用してください。 |
| `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にしている場合は、このドメインの構成をスキップできます。 |
| Key Vault の URL | 443           | Key Vault に資格情報を格納する場合は、Azure Key Vault に必要です。 |
