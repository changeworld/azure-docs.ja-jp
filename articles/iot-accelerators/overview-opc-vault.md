---
title: OPC Vault とは - Azure | Microsoft Docs
description: この記事では、OPC Vault の概要について説明します。 クラウド内で OPC UA アプリケーション用の証明書のライフサイクルを構成、登録、管理できます。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645847"
---
# <a name="what-is-opc-vault"></a>OPC Vault とは

> [!IMPORTANT]
> この記事は更新を行いますが、最新の内容については、「[Azure Industrial IoT](https://azure.github.io/Industrial-IoT/)」 (Azure 産業用 IoT) を参照してください。

OPC Vault は、OPC UA サーバーとクライアント アプリケーションに使用される証明書のライフサイクルの構成、登録、管理をクラウドで行うマイクロサービスです。 この記事では、OPC Vault の単純なユース ケースについて説明します。

## <a name="certificate-management"></a>証明書の管理

たとえば、ある製造会社において OPC UA サーバー マシンを新しく構築されたクライアント アプリケーションに接続する必要があるとします。 この製造業者がサーバー マシンに初めてアクセスすると、エラー メッセージが即座に OPC UA サーバー アプリケーション上に表示され、クライアント アプリケーションがセキュリティで保護されていないことを示します。 このメカニズムは、不正なアプリケーション アクセスを防ぐ目的で OPC UA サーバー マシンに組み込まれており、作業現場での悪質なハッキングを防止します。

## <a name="application-security-management"></a>アプリケーションのセキュリティの管理
OPC Vault には証明書レジストリ、ストレージ、およびライフサイクル管理のためのすべての機能があるため、セキュリティのプロは、OPC Vault マイクロサービスを使用して OPC UA サーバーが任意のクライアント アプリケーションと通信できるように簡単に設定できます。 これで OPC UA サーバーは安全に接続され、新しく構築されたクライアント アプリケーションと通信することができます

## <a name="the-complete-opc-vault-architecture"></a>OPC Vault の完全なアーキテクチャ
次の図は、OPC Vault の完全なアーキテクチャを示しています。

![OPC Vault のアーキテクチャ](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>次の手順

ここでは OPC Vault とその用途について説明しました。次に以下の記事を読むことをお勧めします。

[OPC Vault のアーキテクチャ](overview-opc-vault-architecture.md)