---
title: Azure Resource Mover で VM 移動プロセス中に作成されたリソースを管理する
description: Azure Resource Mover で VM 移動プロセス中に作成されたリソースを管理する方法について説明します
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 6d6f56fb47bfdaadc6704e2a13ebcf0e893b8b1c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602355"
---
# <a name="manage-resources-created-for-the-vm-move"></a>VM 移動用に作成されたリソースを管理する

この記事では、VM 移動プロセスを支援するために [Azure Resource Mover](overview.md) によって明示的に作成されたリソースを管理する方法について説明します。 

リージョン間で VM を移動した後は、Resource Mover によって作成されたリソースが多数存在するようになり、手動でのクリーンアップが必要となります。

## <a name="delete-resources-created-for-vm-move"></a>VM 移動用に作成されたリソースを削除する

移動コレクションと、VM 移動用に作成された Site Recovery リソースを手動で削除します。

1. リソース グループ ```ResourceMoverRG-<sourceregion>-<target-region>``` でリソースを確認します。
2. VM、および移動コレクション内の他のすべてのソース リソースが移動または削除されていることを確認します。 これにより、それらを使用している保留中のリソースがないことが保証されます。
2. これらのリソースを削除します。

    - 移動コレクションの名前は ```movecollection-<sourceregion>-<target-region>``` です。
    - キャッシュ ストレージ アカウントの名前は ```resmovecache<guid>``` です
    - コンテナーの名前は ```ResourceMove-<sourceregion>-<target-region>-GUID``` です。

## <a name="next-steps"></a>次のステップ

Resource Mover を使用して、別のリージョンへの [VM の移動](tutorial-move-region-virtual-machines.md)を試行する。