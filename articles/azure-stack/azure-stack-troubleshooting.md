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
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b63fdd630647cc970a2d935619b4d3f16b8c0375
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure Stack のトラブルシューティング

*適用対象: Azure Stack Development Kit*

このドキュメントでは、Azure Stack の一般的なトラブルシューティング情報を提供します。 

Azure Stack Technical Development Kit は評価環境として提供されているため、Microsoft カスタマー サポート サービスによる公式なサポートは提供されません。 記載されていない問題が発生している場合は、[Azure Stack MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)で追加のサポートと情報について確認してください。  

このセクションで説明されている問題のトラブルシューティングの推奨事項は、複数のソースから取得しており、特定の問題を解決できない場合があります。 コード例はそのまま提供されており、期待される結果を保証できません。 このセクションは、製品の機能強化の実装に合わせて、頻繁に編集および更新されます。

## <a name="deployment"></a>デプロイ
### <a name="deployment-failure"></a>デプロイの失敗
インストール時に障害が発生した場合、デプロイ スクリプトの -rerun オプションを使用して、失敗した手順からデプロイを再開できます。  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>デプロイの最後に、PowerShell セッションがまだ開いており、出力が表示されません
この動作は、PowerShell コマンド ウィンドウが選択されている場合の既定の動作の結果に過ぎない可能性があります。 開発キットのデプロイは実際に成功しましたが、ウィンドウを選択したときに、スクリプトが一時停止しました。 設定が完了したことを確認するには、コマンド ウィンドウのタイトル バーで、"select" という単語を探します。  ESC キーを押してその選択を解除すると、その後に完了メッセージが表示されるはずです。

## <a name="virtual-machines"></a>仮想マシン
### <a name="default-image-and-gallery-item"></a>既定のイメージとギャラリー アイテム
Azure Stack に VM をデプロイする前に、まず Windows Server イメージとギャラリー アイテムを追加する必要があります。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Azure Stack ホストの再起動後、一部の VM が自動的に起動しないことがあります。
ホストの再起動後、Azure Stack サービスがすぐに使用できないことに気付く場合があります。  これは、Azure Stack [インフラストラクチャ VM](..\azure-stack\asdk\asdk-architecture.md#virtual-machine-roles) および RP が一貫性をチェックするために少し時間がかかるためですが、最終的に自動的に起動します。

また、Azure Stack Development Kit ホストの再起動後に、そのテナント VM が自動的に起動しないことに気付く場合もあります。 これは既知の問題であり、それらをオンラインにするために、いくつかの手動の手順が必要です。

1.  Azure Stack Development Kit ホストで、[スタート] メニューから**フェールオーバー クラスター マネージャー**を起動します。
2.  クラスター **S-Cluster.azurestack.local** を選択します。
3.  **[役割]** を選びます。
4.  テナント VM が*保存済み*状態で表示されます。 すべてのインフラストラクチャ VM が実行されたら、テナント VM を右クリックし、**[開始]** を選択して、VM を再開します。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>一部の仮想マシンを削除しましたが、まだディスクに VHD ファイルが表示されます。 これは期待される動作ですか。
はい。これは期待される動作です。 これがそのように設計された理由は次のとおりです。

* VM を削除しても VHD は削除されません。 ディスクはリソース グループ内の個別のリソースです。
* ストレージ アカウントが削除されると、削除は Azure Resource Manager にただちに表示されますが、それを格納している可能性のあるディスクは、ガベージ コレクションが実行されるまで、記憶域に維持されます。

"孤立" VHD が表示された場合、それが削除されたストレージ アカウントのフォルダーの一部であるかどうかを知ることが重要です。 ストレージ アカウントが削除されていない場合は、まだ存在していても正常です。

リテンション期間しきい値と、オンデマンドの再利用の設定について詳しくは、[ストレージ アカウントの管理](azure-stack-manage-storage-accounts.md)に関するページを参照してください。

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>記憶域の再利用
ポータルに再利用された容量が表示されるまで、最大で 14 時間かかる場合があります。 領域の再利用は、ブロック BLOB ストア内の内部コンテナー ファイルの使用率をなど、さまざまな要因に依存します。 そのため、削除されるデータの量によって、ガベージ コレクターの実行時に再利用可能になる領域の量に対する保証はありません。

## <a name="windows-azure-pack-connector"></a>Windows Azure Pack コネクタ
* Azure Stack Development Kit をデプロイした後に azurestackadmin アカウントのパスワードを変更する場合に、マルチクラウド モードを設定できなくなりました。 そのため、対象となる Windows Azure Pack 環境に接続できません。
* マルチクラウド モードの設定後:
    * ユーザはポータル設定のリセット後にのみ、ダッシュボードを表示できます (ユーザー ポータルで、ポータル設定アイコン (右上隅にある歯車アイコン) をクリックします。 **[既定の設定に戻す]** の下の **[適用]** をクリックします)。
    * ダッシュボードのタイトルが表示されない場合があります。 この問題が発生した場合は、それらを手動で追加する必要があります。
    * 最初にタイルをダッシュボードに追加したときに、一部のタイルが正しく表示されないことがあります。 この問題を解決するには、ブラウザーを更新します。



