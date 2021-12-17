---
title: vCenter のアクセスと ID の説明
description: vCenter には cloudadmin という組み込みのローカル ユーザーがあり、CloudAdmin ロールに割り当てられています。
ms.topic: include
ms.date: 08/31/2021
ms.openlocfilehash: 1fdc81c11b833b1239d37f613732eec42b463e1a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252536"
---
<!-- used in concepts-run-commands.md and tutorial-configure-identity-source.md -->

Azure VMware Solution では、vCenter に *cloudadmin* という組み込みのローカル ユーザーがあり、CloudAdmin ロールに割り当てられています。 プライベート クラウドの CloudAdmin ロールを使用して Active Directory Domain Services (AD) のユーザーとグループを構成できます。 通常、プライベート クラウドのワークロードは、CloudAdmin ロールによって作成と管理が行われます。 しかし Azure VMware Solution では、CloudAdmin ロールに、他の VMware クラウド ソリューションやオンプレミスのデプロイとは異なる vCenter 特権があります。

>[!IMPORTANT]
>ローカルの cloudadmin ユーザーは、プライベート クラウドの「中断グラス」シナリオの緊急アクセス アカウントとして扱う必要があります。 日常の管理アクティビティや他のサービスとの統合には適していません。 

- vCenter と ESXi のオンプレミスのデプロイでは、管理者は vCenter administrator\@vsphere.local アカウントにアクセスできます。 さらに多くの AD ユーザーとグループを割り当てることもできます。 

- Azure VMware Solution のデプロイでは、管理者が管理者ユーザー アカウントにアクセスすることはできません。 ただし、AD ユーザーとグループを vCenter の CloudAdmin ロールに割り当てることができます。  CloudAdmin ロールには、オンプレミスの LDAP や LDAPS サーバーなどの ID ソースを vCenter に追加するためのアクセス許可がありません。 ただし、実行コマンドを使用して ID ソースを追加し、cloudadmin ロールをユーザーとグループに割り当てることができます。
 
プライベート クラウド ユーザーは、Microsoft がサポートと管理を行う特定の管理コンポーネントにはアクセスできず、それらを構成することもできません。 (クラスター、ホスト、データストア、分散仮想スイッチなど)。

>[!NOTE]
>Azure VMware Solution では、プラットフォーム操作をサポートするために、 *vsphere.local* SSO ドメインが管理対象リソースとして提供されます。 ローカル グループと、プライベート クラウドで既定で提供されるユーザー以外のユーザーの作成と管理はサポートされていません。
