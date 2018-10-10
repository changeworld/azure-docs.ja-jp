---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0363b719e2587226282257e19e58e3c4e55c2219
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454381"
---
## <a name="overview-of-ssh-and-keys"></a>SSH とキーの概要

SSH は、セキュリティで保護されていない接続においてセキュリティで保護されたサインインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 SSH 自体は暗号化された接続を提供しますが、SSH 接続でパスワードを使用すると、VM はブルートフォース攻撃やパスワードの推測に対して脆弱になります。 SSH を使用して VM に接続するためのより安全で推奨される方法は、公開キーと秘密キーのペア (*SSH キー*とも呼ばれます) を使用する方法です。 

* *公開キー*は、Linux VM か、公開キー暗号化で使用する他のサービスに配置します。

* *秘密キー*は、SSH 接続するときに自分の身元を証明するために渡すキーです。 このキーは安全に保管してください。 このキーは共有しないようにしてください。

組織のセキュリティ ポリシーに応じて、単一の公開キーと秘密キーのペアを再利用して複数の Azure VM とサービスにアクセスできます。 アクセスする VM またはサービスごとに異なるキーのペアを用意する必要はありません。 

公開キーはだれとでも共有できますが、秘密キーを所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) にする必要があります。