---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117097"
---
Windows 10 VPN クライアントの新機能である Always On は、VPN 接続を維持する機能です。 Always On を使用すると、アクティブな VPN プロファイルは、ユーザーのサインイン、ネットワーク状態の変更、またはデバイス画面のアクティブ化などのトリガーに基づいて、自動的に接続し、接続を維持することができます。

ゲートウェイを Windows 10 Always On と共に使用することで、Azure への永続的なユーザー トンネルとデバイス トンネルを確立できます。 この記事は、Always On VPN ユーザー トンネルの構成に役立ちます。

Always On VPN 接続には、次の 2 種類のトンネルのいずれかが含まれます。

* **デバイス トンネル**:ユーザーがデバイスにサインインする前に、指定した VPN サーバーに接続します。 サインイン前の接続シナリオとデバイス管理ではデバイス トンネルが使用されます。

* **ユーザー トンネル**:ユーザーがデバイスにサインインした後にのみ接続します。 ユーザー トンネルを使用すると、ユーザーは VPN サーバー経由で組織のリソースにアクセスできます。

デバイス トンネルとユーザー トンネルは、VPN プロファイルとは関係なく動作します。 これらは同時に接続でき、必要に応じてさまざまな認証方法と他の VPN の構成設定を使用できます。
