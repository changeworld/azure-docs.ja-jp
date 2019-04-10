---
title: Azure IoT OPC UA 証明書管理とは | Microsoft Docs
description: OPC Vault の概要
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759773"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>Azure IoT Open Platform Communications (OPC) UA 証明書管理とは

Azure IoT OPC UA 証明書管理 (OPC Vault とも呼ばれます) は、OPC UA サーバーとクライアント アプリケーションに使用される証明書のライフサイクルの構成、登録、管理をクラウドで行うマイクロサービスです。 この記事では、OPC Vault の単純なユース ケースについて説明します。

## <a name="certificate-management"></a>証明書の管理

たとえば、ある製造会社において OPC UA サーバー マシンを新しく構築されたクライアント アプリケーションに接続する必要があるとします。 この製造業者がサーバー マシンに初めてアクセスすると、エラー メッセージが即座に OPC UA サーバー アプリケーション上に表示され、クライアント アプリケーションがセキュリティで保護されていないことを示します。 このメカニズムは、不正なアプリケーション アクセスを防ぐ目的で OPC UA サーバー マシンに組み込まれており、作業現場での悪質なハッキングを防止します。

## <a name="application-security-management"></a>アプリケーションのセキュリティの管理
OPC Vault には証明書レジストリ、ストレージ、およびライフサイクル管理のためのすべての機能があるため、セキュリティのプロは、OPC Vault マイクロサービスを使用して OPC UA サーバーが任意のクライアント アプリケーションと通信できるように簡単に設定できます。 これで OPC UA サーバーは安全に接続され、新しく構築されたクライアント アプリケーションと通信することができます

## <a name="the-complete-opc-vault-architecture"></a>OPC Vault の完全なアーキテクチャ
次の図は、OPC Vault の完全なアーキテクチャを示しています。

![OPC Vault のアーキテクチャ](media/overview-opc-vault-architecture/opc-vault.png)