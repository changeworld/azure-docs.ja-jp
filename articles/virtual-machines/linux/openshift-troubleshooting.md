---
title: Azure での OpenShift デプロイのトラブルシューティング | Microsoft Docs
description: Azure での OpenShift デプロイの問題を解決します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
ms.locfileid: "24139452"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Azure での OpenShift デプロイのトラブルシューティング

OpenShift クラスターが正常に展開しない場合は、これらのトラブルシューティングを試すと、問題を絞り込むことができます。 デプロイ ステータスを表示し、次の終了コード一覧と比較してください。

- 終了コード 3: Red Hat サブスクリプション ユーザー名/パスワードまたは組織 ID/ライセンス認証キーが正しくありません
- 終了コード 4: Red Hat のプール ID が正しくないか、使用権利がありません
- 終了コード 5: Docker シン プールのボリュームをプロビジョニングできません
- 終了コード 6: OpenShift クラスターのインストールに失敗しました
- 終了コード 7: OpenShift クラスターのインストールは成功しましたが、Azure Cloud Solution Provider の構成に失敗しました - マスター ノードのマスター設定の問題
- 終了コード 8: OpenShift クラスターのインストールは成功しましたが、Azure Cloud Solution Provider の構成に失敗しました - マスター ノードのノード設定の問題
- 終了コード 9: OpenShift クラスターのインストールは成功しましたが、Azure Cloud Solution Provider の構成に失敗しました - インすらストラクチャまたはアプリケーション ノードのノード設定の問題
- 終了コード 10: OpenShift クラスターのインストールは成功しましたが、Azure Cloud Solution Provider の構成に失敗しました - マスター ノードの修正、またはマスターをスケジュール不可に設定できません
- 終了コード 11: メトリックの展開に失敗しました
- 終了コード 12: ロギングの展開に失敗しました

終了コード 7 から 10、OpenShift クラスターをインストールしましたが、Azure Cloud Solution Provider の構成に失敗しました。 SSH をマスター ノード (OpenShift Origin) または要塞ノード (OpenShift Container Platform) に送信し、そこから各ノードに SSH を送信して、問題を解決できます。

終了コード 7 から 9 で失敗する場合、主な原因として、サービス プリンシパルがサブスクリプションまたはリソース グループに対する適切な権限を持っていなかったことが考えられます。 これが原因である場合は、適切な権限を割り当て、失敗したスクリプトと後続のすべてのスクリプトを手動で再実行します。

スクリプトを再度実行する前に、必ず失敗したサービス (たとえば、systemctl atomic-openshift-node.service) を再起動してください。

さらにトラブルシューティングするには、ポート 2200 (Origin) で、マスター ノードに SSH を送信するか、またはポート 22 (Container Platform) の要塞ノードに送信します。 ルート (sudo su -) ユーザーでログインし、/var/lib/waagent/custom-script/download ディレクトリに移動します。

ここには "0" と "1" というフォルダーがあります。 これらの各フォルダーに、"stderr"、"stdout" という 2 つのファイルがあります。 これらのファイルから、障害が発生した箇所を特定します。
