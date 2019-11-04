---
title: Azure SignalR でリアルタイム モバイル アプリケーションを簡単に構築する
description: Azure SignalR を使用してリアルタイム アプリケーションを構築できるサービスについて説明します。
author: elamalani
ms.assetid: 34a8a070-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 566f1f8b192a108b1ce7e3153eac9a4daf192c28
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795203"
---
# <a name="build-real-time-applications-with-azure-signalr"></a>Azure SignalR でリアルタイム アプリケーションを構築する

[Azure SignalR](https://azure.microsoft.com/services/signalr-service/) を使用すると、アプリケーションにリアルタイム機能を簡単に追加できます。 このリアルタイム機能により、サービスは接続されているモバイル アプリケーションにコンテンツの更新をプッシュできます。 その結果、アプリは、サーバーをポーリングしたり、更新プログラムについて新しい HTTP 要求を送信したりしなくても更新されます。

## <a name="azure-signalr-features"></a>Azure SignalR の機能
- **幅広いクライアント サポート** - Web ブラウザー、モバイル ブラウザー、デスクトップ アプリケーション、サーバー プロセス、IoT デバイス、ゲーム コンソールなど、幅広い範囲のクライアントに対応しています。
- SignalR Service により、数百万のモバイル アプリケーション接続に対応するために**複数のインスタンスが連携して動作**できます。 シャーディング、高可用性、ディザスター リカバリーの目的で、複数のグローバル リージョンもサポートしています。
- **さまざまなメッセージ パターンのための豊富な API を提供**し、特定の接続やすべての接続にメッセージを送信したり、特定のユーザーに属するか、任意のグループに割り当てられた接続のサブセットにメッセージを送信したりできます。

## <a name="references"></a>参照
   - [Azure Portal](https://portal.azure.com)
   - [ドキュメント](/azure/azure-signalr/signalr-overview)
   - [チュートリアル](/azure/azure-signalr/signalr-tutorial-authenticate-azure-functions)