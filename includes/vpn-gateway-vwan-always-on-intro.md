---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1345df8991acba09feb65eb11e9eadc21b9c7af4
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579700"
---
Windows 10 VPN クライアントの新機能である Always On は、VPN 接続を維持する機能です。 Always On を使用すると、アクティブな VPN プロファイルは、ユーザーのサインイン、ネットワーク状態の変更、またはデバイス画面のアクティブ化などのトリガーに基づいて、自動的に接続し、接続を維持することができます。

ゲートウェイを Windows 10 Always On と共に使用することで、Azure への永続的なユーザー トンネルとデバイス トンネルを確立できます。

Always On VPN 接続には、次の 2 種類のトンネルのいずれかが含まれます。

* **デバイス トンネル**:ユーザーがデバイスにサインインする前に、指定した VPN サーバーに接続します。 サインイン前の接続シナリオとデバイス管理ではデバイス トンネルが使用されます。

* **ユーザー トンネル**:ユーザーがデバイスにサインインした後にのみ接続します。 ユーザー トンネルを使用すると、ユーザーは VPN サーバー経由で組織のリソースにアクセスできます。

デバイス トンネルとユーザー トンネルは、VPN プロファイルとは関係なく動作します。 これらは同時に接続でき、必要に応じてさまざまな認証方法と他の VPN の構成設定を使用できます。
