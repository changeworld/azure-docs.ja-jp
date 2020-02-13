---
title: Azure VMware Solutions (AVS) プライベート クラウドの管理
description: AVS プライベート クラウドのリソースとアクティビティを管理するために使用できる機能について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014829"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>AVS プライベート クラウドのリソースとアクティビティを管理する

AVS プライベート クラウドは、AVS ポータルから管理します。 AVS ポータルから、AVS プライベート クラウドにおける状態、使用可能なリソース、アクティビティ、さらにその他の設定を確認します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="access-the-avs-portal"></a>AVS ポータルへのアクセス

[AVS ポータル](access-cloudsimple-portal.md)にアクセスします。

## <a name="view-the-list-of-avs-private-clouds"></a>AVS プライベート クラウドの一覧を表示する

**[リソース]** ページの **[AVS プライベート クラウド]** タブには、ご利用のサブスクリプション内のすべての AVS プライベート クラウドが一覧表示されます。 情報には、AVS プライベート クラウドにおける名前、vSphere クラスターの数、場所、現在の状態、さらにリソース情報が含まれます。

![[AVS プライベート クラウド] ページ](media/manage-private-cloud.png)

追加情報とアクションについては、AVS プライベート クラウドを選択してください。

## <a name="avs-private-cloud-summary"></a>AVS プライベート クラウドの概要

選択した AVS プライベート クラウドの包括的な概要を表示します。 概要ページには、AVS プライベート クラウドにデプロイされている DNS サーバーが含まれます。 オンプレミスの DNS サーバーから AVS プライベート クラウドの DNS サーバーへの DNS 転送を設定することができます。 DNS 転送の詳細については、[AVS プライベート クラウド vCenter の名前解決用 DNS をオンプレミスから構成する方法](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)に関するページを参照してください。

![AVS プライベート クラウドの概要](media/private-cloud-summary.png)

### <a name="available-actions"></a>使用可能なアクション

* [vSphere クライアントの起動](https://docs.azure.cloudsimple.com/vsphere-access/)。 この AVS プライベート クラウドの vCenter にアクセスします。
* [ノードの購入](create-nodes.md)。 この AVS プライベート クラウドにノードを追加します。
* [拡大](expand-private-cloud.md)。 この AVS プライベート クラウドにノードを追加します。
* **更新する**。 このページの情報を更新します。
* **削除**。 AVS プライベート クラウドはいつでも削除できます。 **削除する前に、すべてのシステムとデータがバックアップ済みであることを確認してください。** AVS プライベート クラウドを削除すると、すべての VM、vCenter 構成、データが削除されます。 選択した AVS プライベート クラウドの概要セクションで **[削除]** をクリックします。 削除後、すべての AVS プライベート クラウド データは、安全で、コンプライアンスに厳格に準拠した消去プロセスで消去されます。
* [vSphere 権限の変更](escalate-private-cloud-privileges.md)。 この AVS プライベート クラウドに対するご利用の特権をエスカレーションします。

## <a name="avs-private-cloud-vlanssubnets"></a>AVS プライベート クラウドの VLAN またはサブネット

選択した AVS プライベート クラウドに対して定義されている VLAN またはサブネットの一覧を表示します。 一覧には、AVS プライベート クラウドの作成時に作成された管理 VLAN またはサブネットが含まれています。

![AVS プライベート クラウド - VLAN またはサブネット](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>使用可能なアクション

* [VLAN/サブネットの追加](https://docs.azure.cloudsimple.com/create-vlan-subnet/)。 VLAN またはサブセットをこの AVS プライベート クラウドに追加します。

以下のアクションのために VLAN/サブネットを選択します
* [ファイアウォール テーブルのアタッチ](https://docs.azure.cloudsimple.com/firewall/)。 ファイアウォール テーブルをこの AVS プライベート クラウドにアタッチします。
* **[編集]**
* **削除** (ユーザー定義の VLAN/サブネットのみ)

## <a name="avs-private-cloud-activity"></a>AVS プライベート クラウドのアクティビティ

選択した AVS プライベート クラウドの次の情報を表示します。 アクティビティ情報は、すべてのアクティビティから、選択した AVS プライベート クラウドで絞り込んだ一覧です。 このページには最近のアクティビティが最大 25 件表示されます。

* 最近のアラート
* 最近のイベント
* 最近のタスク
* 最近の監査

![AVS プライベート クラウド - アクティビティ](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>クラウド ラック

クラウド ラックはご利用の AVS プライベート クラウドの構成要素です。 各ラックは容量の単位を提供します。 AVS プライベート クラウドを作成または拡張するときに、AVS では、選択した内容に基づいてクラウド ラックが自動的に構成されます。 それぞれが割り当てられている AVS プライベート クラウドを含む、クラウド ラックの完全な一覧を表示します。

![AVS プライベート クラウド - クラウド ラック](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理ネットワーク

現在 AVS プライベート クラウドで構成されている VMware 管理リソースおよび仮想マシンの一覧。 情報には、ソフトウェアのバージョン、完全修飾ドメイン名 (FQDN)、リソースの IP アドレスが含まれます。

![AVS プライベート クラウド - vSphere 管理ネットワーク](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>次のステップ

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [AVS プライベート クラウド](cloudsimple-private-cloud.md)の詳細を確認する