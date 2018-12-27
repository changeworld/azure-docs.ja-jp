---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506351"
---
## <a name="overview-of-ssh-and-keys"></a>SSH とキーの概要

SSH は、セキュリティで保護されていない接続においてセキュリティで保護されたサインインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 SSH 自体は暗号化された接続を提供しますが、SSH 接続でパスワードを使用すると、VM はブルートフォース攻撃やパスワードの推測に対して脆弱になります。 SSH を使用して VM に接続するためのより安全で推奨される方法は、公開キーと秘密キーのペア (*SSH キー*とも呼ばれます) を使用する方法です。 

* *公開キー*は、Linux VM か、公開キー暗号化で使用する他のサービスに配置します。

* ローカル システム上の "*秘密キー*" は、ユーザーが Linux VM に接続するときに、ユーザーの ID を確認するために SSH クライアントによって使用されます。 このキーは安全に保管してください。 このキーは共有しないようにしてください。

組織のセキュリティ ポリシーに応じて、単一の公開キーと秘密キーのペアを再利用して複数の Azure VM とサービスにアクセスできます。 アクセスする VM またはサービスごとに異なるキーのペアを用意する必要はありません。 

公開キーはだれとでも共有できますが、秘密キーを所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) にする必要があります。