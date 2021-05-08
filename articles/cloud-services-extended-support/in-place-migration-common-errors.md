---
title: Azure Cloud Services (延長サポート) に移行するときの一般的なエラーと既知の問題
description: Cloud Services (クラシック) から Cloud Services (延長サポート) に移行するときの一般的なエラーの概要
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286758"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) に移行するときの一般的なエラーと既知の問題

この記事では、Cloud Services (クラシック) から Cloud Services (延長サポート) に移行するときに発生する可能性のある既知の問題と一般的なエラーについて説明します。 

## <a name="known-issues"></a>既知の問題
次の問題が確認されており、対処が行われています。

| 既知の問題 | 対応策 | 
|---|---|
| コミットが成功した後、ロール インスタンスによって UD が 1 つずつ再起動される。 | 再起動操作は、毎月のゲスト OS のロールアウトと同じ方法で行われます。 ロール インスタンスが 1 つだけのクラウド サービスの移行をコミットしないでください。そうしないと、再起動の影響を受けます。| 
| ブラウザーの更新後に、Azure portal で移行の状態を読み取ることができない。 | 元の移行状態に戻すには、検証および準備操作を再実行します。 | 
| キー コンテナーで証明書がシークレット リソースとして表示される。 | 移行後に、証明書を証明書リソースとして再アップロードし、Cloud Services (延長サポート) での更新操作を簡略化します。 | 
| 移行の一環として、デプロイ ラベルがタグとして保存されない。 | この情報を維持するには、移行後にタグを手動で作成します。
| リソース グループ名がすべて大文字になる。 | 影響はありません。 使用できる解決策はまだありません。 |
| Cloud Services (延長サポート) ロックでのロックの名前が正しくない。 | 影響はありません。 使用できる解決策はまだありません。 | 
| Cloud Services (延長サポート) ポータル ブレードで、IP アドレス名が正しくない。 | 影響はありません。 使用できる解決策はまだありません。 | 
| 移行されたクラウド サービスでの更新操作の後、仮想 IP アドレスに対して無効な DNS 名が表示される。 | 影響はありません。 使用できる解決策はまだありません。 | 
| 準備が正常に完了した後、Cloud Services (延長サポート) の新しいデプロイをスワップ可能としてリンクできない。 | 準備されたクラウド サービスに、新しいクラウド サービスをスワップ可能としてリンクしないでください。 | 
| エラー メッセージを更新する必要がある。 | 影響はありません。 | 

## <a name="common-migration-errors"></a>移行の一般的なエラー
移行に関する一般的なエラーと軽減手順。 

| エラー メッセージ | 詳細 | 
|---|---|
| API バージョン '2020-10-01-preview' の名前空間 '`Microsoft.Compute`' でリソースの種類が見つかりませんでした。 | パブリック プレビューにアクセスするには、CloudServices 機能フラグの[サブスクリプションを登録](in-place-migration-overview.md#setup-access-for-migration)してください。 | 
| サーバーで内部エラーが発生しました。 要求をやり直してください。 | 操作を再試行するか、[Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) を使用するか、サポートに問い合わせてください。 | 
| The server encountered an unexpected error while trying to allocate network resources for the cloud service. (クラウド サービス用のネットワーク リソースを割り当てようとしているときに、サーバーで予期しないエラーが発生しました。) 要求をやり直してください。 | 操作を再試行するか、[Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) を使用するか、サポートに問い合わせてください。 | 
| クラウド サービス <クラウド サービス名> でのデプロイ <デプロイ名> は、移行する仮想ネットワーク内に存在する必要があります。 | デプロイが仮想ネットワーク内にありません。 詳細については、[こちら](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network)のドキュメントを参照してください。 | 
| クラウド サービス <クラウド サービス名> でのデプロイ <デプロイ名> の移行は、リージョン <リージョン名> 内にあるため、サポートされていません。 許可されたリージョン: <使用可能なリージョンの一覧>。 | 移行はリージョンでまだサポートされていません。 | 
| ロール <ロール名> に関連付けられているサブネットがないため、クラウド サービス <クラウド サービス名> のデプロイ <デプロイ名> を移行できません。 すべてのロールをサブネットに関連付けてから、クラウド サービスの移行をもう一度お試しください。 | 移行の前にサブネットに配置することで、Cloud Services (クラシック) のデプロイを更新します。 |  
| デプロイに必要な 1 つ以上の機能が Azure Resource Manager でサブスクリプションに登録されていないため、クラウド サービス <クラウド サービス名> のデプロイ <デプロイ名> を移行できません。 このデプロイを移行するには、すべての必要な機能を登録してください。 不足している機能: <不足している機能の一覧>。 | 機能フラグの登録を、サポートに依頼してください。 | 
| このデプロイのクラウド サービスには占有されているスロットが 2 つあるため、デプロイを移行できません。 クラウド サービスの移行は、そのクラウド サービス内の唯一のデプロイであるデプロイでのみサポートされています。 このデプロイの移行を続行するには、クラウド サービス内のもう一方のデプロイを削除してください。 | 詳細については、[サポートされていないシナリオ](in-place-migration-overview.md#unsupported-configurations--migration-scenarios)の一覧を参照してください。 | 
| HostedService <クラウド サービス名> のデプロイ <デプロイ名> は中間の状態 <状態> にあります。 移行することはできません。 | デプロイは、作成中、削除中、または更新中です。 操作が完了するまで待ってから、再試行します。 | 
| ホストされるサービス <クラウド サービス名> 内のデプロイ <デプロイ名> には予約済み IP がありますが、予約済み IP 名がありません。 この問題を解決するには、予約済み IP 名を更新するか、Microsoft Azure サービス デスクにお問い合わせください。 | クラウド サービスのデプロイを更新します。 | 
| ホストされるサービス <クラウド サービス名> 内のデプロイ <デプロイ名> には予約済み IP <予約済み IP 名> がありますが、予約済み IP 上にエンドポイントがありません。 この問題を解決するには、予約済み IP に少なくとも 1 つのエンドポイントを追加してください。 | 予約済み IP にエンドポイントを追加します。 | 
| HostedService {1} のデプロイ {0} の移行は、コミットの処理中のため、その処理が正常に完了するまで変更できません。  | 操作を待機または再試行します。 | 
| HostedService {1} のデプロイ {0} の移行は、中止の処理中のため、その処理が正常に完了するまで変更できません。 | 操作を待機または再試行します。 |
| HostedService {1} のデプロイ {0} に含まれる 1 つ以上の VM は更新操作の実行中です。 前の操作が正常に完了するまで移行することはできません。 しばらくしてからやり直してください。 | 操作の完了を待ちます。 | 
| HostedService {1} 内のデプロイ {0} では、移行をサポートしていない次の機能が使用されているため、移行はサポートされていません: 非 VNet のデプロイ。| デプロイが仮想ネットワーク内にありません。 詳細については、[こちら](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network)のドキュメントを参照してください。 | 
| 仮想ネットワーク名を null または空にすることはできません。 | REST 要求本文で仮想ネットワーク名を指定します | 
| サブネット名を null または空にすることはできません。 | REST 要求本文でサブネット名を指定します。 | 
| DestinationVirtualNetwork は、次のいずれかの値に設定する必要があります: Default、New、Existing。 | REST 要求本文で DestinationVirtualNetwork プロパティを適切に指定します。 | 
| Default VNet destination option not implemented. (既定の VNet 宛先オプションが実装されていません。) | REST 要求本文の DestinationVirtualNetwork プロパティでは、"Default" 値はサポートされていません。 | 
| The deployment {0} cannot be migrated because the CSPKG is not available. (CSPKG を使用できないため、デプロイ {0} を移行できません。) | デプロイをアップグレードしてから、もう一度試してください。 | 
| ID '{0}' のサブネットがホストされるサービス '{2}' の展開 '{1}' 以外の場所にあります。 サブネットの場所は '{3}' であり、ホストされるサービスの場所は '{4}' です。  展開と同じ場所のサブネットを指定してください。 | 移行の前に、サブネットとクラウド サービスの両方が同じ場所になるように、クラウド サービスを更新します。 | 
| HostedService {1} のデプロイ {0} の移行は、中止の処理中のため、その処理が正常に完了するまで変更できません。 | 中止が完了するのを待つか、または中止を再試行します。 [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) を使用するか、それ以外についてはサポートに問い合わせてください。 | 
| HostedService {1} 内の展開 {0} は、移行の準備ができていません。 | コミット操作を実行する前に、クラウド サービスで準備を実行します。 | 
| UnknownExceptionInEndExecute: Contract.Assert failed: rgName is null or empty: Exception received in EndExecute that is not an RdfeException. (UnknownExceptionInEndExecute: Contract.Assert が失敗しました: rgName が null または空です: RdfeException ではない EndExecute で例外を受け取りました。) |   [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) を使用するか、サポートに問い合わせてください。 | 
| UnknownExceptionInEndExecute: A task was canceled: Exception received in EndExecute that is not an RdfeException. (UnknownExceptionInEndExecute: タスクが取り消されました: rgName が null または空です: RdfeException ではない EndExecute で例外を受け取りました。) | [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) を使用するか、サポートに問い合わせてください。 | 
| XrpVirtualNetworkMigrationError: Virtual network migration failure. (XrpVirtualNetworkMigrationError: 仮想ネットワークの移行失敗。) | [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) を使用するか、サポートに問い合わせてください。 | 
| HostedService {1} 内の展開 {0} は仮想ネットワーク {2} に属しています。 仮想ネットワーク {2} をこの HostedService {1} に移行してください。 | 「[仮想ネットワークの移行](in-place-migration-technical-details.md#virtual-network-migration)」を参照してください。 | 
| Azure Resource Manager にあるリソース名のための現在のクォータは、移行を完了するには不十分です。 現在のクォータは {0}、必要な追加分は {1} です。 クォータを増やすサポート リクエストを提出し、クォータが増えてから移行をもう一度お試しください。    | 適切なチャネルに従ってクォータの引き上げを要求します。 <br>[ネットワーク リソースのクォータの引き上げ](../azure-portal/supportability/networking-quota-requests.md) <br>[コンピューティング リソースのクォータの引き上げ](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>次の手順
移行の要件の詳細については、「[Azure Cloud Services への移行に関する技術的な詳細 (延長サポート)](in-place-migration-technical-details.md)」を参照してください
