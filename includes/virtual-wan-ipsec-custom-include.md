---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204468"
---
カスタム IPsec ポリシーを操作する場合は、次の要件に注意してください。

* **IKE** - IKE の場合、IKE 暗号化の任意のパラメーターと、IKE 整合性の任意のパラメーター、および DH グループの任意のパラメーターを選択できます。
* **IPsec** -  IPsec の場合、IPsec 暗号化の任意のパラメーターと、IPsec 整合性の任意のパラメーター、および PFS を選択できます。 IPsec 暗号化または IPsec 整合性のパラメーターのいずれかが GCM の場合、両方の設定のパラメーターは GCM である必要があります。

>[!NOTE]
> カスタム IPsec ポリシーでは、(既定の IPsec ポリシーとは異なり) レスポンダーとイニシエーターの概念はありません。 両側 (オンプレミスと Azure VPN ゲートウェイ) で、IKE フェーズ 1 および IKE フェーズ 2 に同じ設定が使用されます。 IKEv1 と IKEv2 の両方のプロトコルがサポートされています。 レスポンダーのみとしての Azure はサポートされていません。
>

**使用可能な設定とパラメーター**

| 設定 | パラメーター |
|--- |--- |
| IKE 暗号化 | GCMAES256、GCMAES128、AES256、AES128 |
| IKE 整合性 | SHA384、SHA256 |
| DH グループ | ECP384、ECP256、DHGroup24、DHGroup14 |
| IPsec 暗号化 | GCMAES256、GCMAES128、AES256、AES128、なし |
| IPsec 整合性 | GCMAES256、GCMAES128、SHA256 |
| PFS グループ | ECP384、ECP256、PFS24、PFS14、なし |
