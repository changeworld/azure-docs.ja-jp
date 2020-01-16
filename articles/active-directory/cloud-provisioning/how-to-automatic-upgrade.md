---
title: Azure AD Connect クラウド プロビジョニング エージェント:自動アップグレード | Microsoft Docs
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントの組み込みの自動アップグレード機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3786386f75f4b85fe89562254eab63471de6cb2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549399"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect クラウド プロビジョニング エージェント:自動アップグレード

Azure Active Directory (Azure AD) Connect クラウド プロビジョニング エージェントのインストールを常に最新の状態に保つことは、自動アップグレード機能を使用すれば簡単です。 この機能は既定で有効になっており、無効にすることはできません。

エージェントは次の場所にインストールされます。"Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

バージョンを確認するには、実行可能ファイルを右クリックして、[プロパティ]、[詳細] の順に選択します。

![エージェント ファイルのバージョン](media/how-to-automatic-upgrade/agent1.png)

エージェント アップデーターは次の場所にインストールされます。"Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

バージョンを確認するには、実行可能ファイルを右クリックして、[プロパティ]、[詳細] の順に選択します。

![エージェントアップデーターのバージョン](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>エージェントのアンインストール
エージェントを削除するには、 **[プログラムのアンインストールまたは変更]** に移動して、次のものをアンインストールします。

- **Microsoft Azure AD Connect エージェント アップデーター**
- **Microsoft Azure AD Connect プロビジョニング エージェント**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![エージェントの削除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)

