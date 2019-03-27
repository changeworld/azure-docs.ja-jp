---
title: Microsoft Azure Stack のトラブルシューティング | Microsoft Docs
description: Azure Stack のトラブルシューティング。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: c59f563adee3b5db7060b26906b51096cbd60212
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238681"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure Stack のトラブルシューティング

このドキュメントでは、Azure Stack の一般的なトラブルシューティング情報を提供します。 

> [!NOTE]
> Azure Stack Technical Development Kit (ASDK) は評価環境として提供されているため、Microsoft カスタマー サポート サービスによる公式なサポートは提供されません。 問題が発生する場合は、[Azure Stack MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)で追加のサポートと情報について確認してください。  

このセクションで説明されている問題のトラブルシューティングの推奨事項は、複数のソースから取得しており、特定の問題を解決できない場合があります。 コード例はそのまま提供されており、期待される結果を保証できません。 このセクションは、製品の機能強化の実装に合わせて、頻繁に編集および更新されます。

## <a name="deployment"></a>Deployment
### <a name="general-deployment-failure"></a>一般的なデプロイの失敗
インストール時に障害が発生した場合、デプロイ スクリプトの -rerun オプションを使用して、失敗した手順からデプロイを再開できます。  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>ASDK のデプロイの最後に、PowerShell セッションがまだ開いており、出力が表示されません。
この動作は、PowerShell コマンド ウィンドウが選択されている場合の既定の動作の結果に過ぎない可能性があります。 開発キットのデプロイは成功しましたが、ウィンドウを選択したときに、スクリプトが一時停止しました。 設定が完了したことを確認するには、コマンド ウィンドウのタイトル バーで、"select" という単語を探します。  ESC キーを押してその選択を解除すると、その後に完了メッセージが表示されるはずです。

### <a name="deployment-fails-due-to-lack-of-external-access"></a>外部アクセスがないことが原因でデプロイが失敗する
外部アクセスが必要な段階でデプロイが失敗する場合は、次の例のような例外が返されます。

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
このエラーが発生する場合は、[デプロイ ネットワークのトラフィックに関するドキュメント](deployment-networking.md)を見直して、最小限のネットワーク要件がすべて満たされていることを確認してください。 パートナー ツールキットの一部として、ネットワーク チェッカー ツールも提供されています。

上記の例外が表示されるデプロイ エラーは、一般に、インターネットのリソースへの接続の問題が原因です

これが問題であることを確認するため、次の手順を実行できます。

1. PowerShell を開きます
2. WAS01 または ERC VM のいずれかに対する PSSession に入ります
3. 次のコマンドレットを実行します: Test-NetConnection login.windows.net -port 443

このコマンドが失敗する場合は、TOR スイッチおよびその他のネットワーク デバイスがすべて、[ネットワーク トラフィックを許可する](azure-stack-network.md)ように構成されていることを確認します。

## <a name="virtual-machines"></a>仮想マシン
### <a name="default-image-and-gallery-item"></a>既定のイメージとギャラリー アイテム
Azure Stack に VM をデプロイする前に、まず Windows Server イメージとギャラリー アイテムを追加する必要があります。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Azure Stack ホストの再起動後、一部の VM が自動的に起動しないことがあります。
ホストの再起動後、Azure Stack サービスがすぐに使用できないことに気付く場合があります。  これは、Azure Stack [インフラストラクチャ VM](../azure-stack/asdk/asdk-architecture.md#virtual-machine-roles) およびリソース プロバイダーが一貫性をチェックするために少し時間がかかるためですが、最終的に自動的に起動します。

また、Azure Stack Development Kit ホストの再起動後に、そのテナント VM が自動的に起動しないことに気付く場合もあります。 これは既知の問題であり、それらをオンラインにするために、いくつかの手動の手順が必要です。

1.  Azure Stack Development Kit ホストで、[スタート] メニューから**フェールオーバー クラスター マネージャー**を起動します。
2.  クラスター **S-Cluster.azurestack.local** を選択します。
3.  **[役割]** を選びます。
4.  テナント VM が*保存済み*状態で表示されます。 すべてのインフラストラクチャ VM が実行されたら、テナント VM を右クリックし、**[開始]** を選択して、VM を再開します。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>一部の仮想マシンを削除しましたが、まだディスクに VHD ファイルが表示されます。 これは期待される動作ですか。
はい、これは正しい動作です。 これがそのように設計された理由は次のとおりです。

* VM を削除しても VHD は削除されません。 ディスクはリソース グループ内の個別のリソースです。
* ストレージ アカウントが削除されると、削除は Azure Resource Manager にただちに表示されますが、それを格納している可能性のあるディスクは、ガベージ コレクションが実行されるまで、記憶域に維持されます。

"孤立" VHD が表示された場合、それが削除されたストレージ アカウントのフォルダーの一部であるかどうかを知ることが重要です。 ストレージ アカウントが削除されていない場合は、まだ存在していても正常です。

リテンション期間しきい値と、オンデマンドの再利用の設定について詳しくは、[ストレージ アカウントの管理](azure-stack-manage-storage-accounts.md)に関するページを参照してください。

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>記憶域の再利用
ポータルに再利用された容量が表示されるまで、最大で 14 時間かかる場合があります。 領域の再利用は、ブロック BLOB ストア内の内部コンテナー ファイルの使用率をなど、さまざまな要因に依存します。 そのため、削除されるデータの量によって、ガベージ コレクターの実行時に再利用可能になる領域の量に対する保証はありません。

