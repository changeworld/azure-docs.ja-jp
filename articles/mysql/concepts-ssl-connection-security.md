---
title: SSL 接続 - Azure Database for MySQL
description: SSL 接続を適切に使用できるように、Azure Database for MySQL と関連アプリケーションを構成するための情報
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6c1f52c69104e19ff0a7a7cae255dd7029e1a4b8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941788"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Azure Database for MySQL の SSL 接続

Azure Database for MySQL では、Secure Sockets Layer (SSL) を使用して、データベース サーバーをクライアント アプリケーションに接続できます。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

## <a name="default-settings"></a>既定の設定

既定では、MySQL サーバーへの接続時に SSL 接続を要求するようにデータベース サービスを構成する必要があります。  可能な場合は、SSL オプションを無効にしないことをお勧めします。

Azure Portal や CLI を使用して新しい Azure Database for MySQL サーバーをプロビジョニングする場合、SSL 接続の適用が既定で有効になります。 

さまざまなプログラミング言語の接続文字列が Azure Portal に表示されます。 これらの接続文字列には、データベースへの接続に必要な SSL パラメーターが含まれます。 Azure Portal で、お使いのサーバーを選択します。 **[設定]** の見出しにある **[接続文字列]** を選択します。 SSL パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

> [!NOTE]
> 現在、Azure Database for MySQL でサポートされている TLS のバージョンは、TLS 1.0、TLS 1.1、TLS 1.2 です。

アプリケーションの開発時に SSL 接続を有効または無効にする方法については、[SSL の構成方法](howto-configure-ssl.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

[Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)
