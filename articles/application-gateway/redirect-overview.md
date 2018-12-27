---
title: Azure Application Gateway のリダイレクトの概要
description: Azure Application Gateway のリダイレクト機能について説明します
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204379"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway のリダイレクトの概要

HTTP を HTTPS に自動的にリダイレクトして、アプリケーションとユーザーの間のすべての通信が暗号化されたパスで行われるようにすることは、多くの Web アプリケーションでよくあるシナリオです。 これまでは、HTTP で受信した要求を HTTPS にリダイレクトすることが唯一の目的である専用のバックエンド プールの作成といった手法が使われていました。

Application Gateway は、ゲートウェイ上でトラフィックをリダイレクトする機能をサポートするようになりました。 これにより、アプリケーションの構成が簡単になり、リソースの使用が最適化され、グローバルなリダイレクトやパスに基づくリダイレクトなどの新しいリダイレクト シナリオがサポートされるようになります。 Application Gateway のリダイレクトのサポートは、HTTP から HTTPS へのリダイレクトだけではありません。 汎用的なリダイレクト メカニズムを持ち、Application Gateway のあるリスナーで受信したトラフィックを別のリスナーにリダイレクトできます。 外部のサイトのリダイレクトもサポートされます。

Application Gateway のリダイレクトのサポートでは、次の機能が提供されます。

-  **グローバルなリダイレクト**

   ゲートウェイ上のあるリスナーから別のリスナーへのリダイレクト。 これにより、サイトでの HTTP から HTTPS へのリダイレクトが可能になります。
- **パスに基づくリダイレクト**

   このタイプのリダイレクトを使うと、特定のサイト領域でのみ HTTP から HTTPS へのリダイレクトが可能になります (たとえば、/cart/* で示されたショッピング カート領域など)。
- **外部サイトへのリダイレクト**

![リダイレクト](./media/redirect-overview/redirect.png)

この変更により、お客様は、リダイレクト先のターゲット リスナーまたは外部サイトを指定する新しいリダイレクト構成オブジェクトを作成する必要があります。 構成要素は、リダイレクトされる URL に URI パスとクエリ文字列を追加できるオプションもサポートしています。 リダイレクトが一時的か (HTTP 状態コード 302) または永続的なリダイレクトか (HTTP 状態コード 301) を選ぶこともできます。 このリダイレクト構成は、作成されると、新しいルールによってソース リスナーに関連付けられます。 基本的なルールを使うと、リダイレクト構成はソース リスナーに関連付けられて、グローバル リダイレクトになります。 パスベース ルールを使用する場合、リダイレクトの構成は URL パス マップで定義されます。 したがって、サイトの特定のパス領域にのみ適用されます。

### <a name="next-steps"></a>次の手順

[アプリケーション ゲートウェイに URL リダイレクトを構成する](tutorial-url-redirect-powershell.md)
