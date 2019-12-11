---
title: Azure AD Connect クラウド プロビジョニング エージェント:自動アップグレード | Microsoft Docs
description: このトピックでは、Azure AD Connect クラウド プロビジョニング エージェントの組み込みの自動アップグレード機能について説明します。
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
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795518"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect クラウド プロビジョニング エージェント:自動アップグレード

Azure AD Connect クラウド プロビジョニング エージェントのインストールを常に最新の状態に保つことは、 **自動アップグレード** 機能によって、これまでよりも簡単になりました。 この機能は既定で有効になっており、無効にすることはできません。

エージェントは次の場所にインストールされます。 **"Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"**

実行可能ファイルを右クリックし、[プロパティ]、[詳細] の順に選択して、バージョンを確認できます。

![エージェント ファイルのバージョン](media/how-to-automatic-upgrade/agent1.png)

エージェント アップデーターは次の場所にインストールされます。 **"Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"**

実行可能ファイルを右クリックし、[プロパティ]、[詳細] の順に選択して、バージョンを確認できます。

![エージェントアップデーターのバージョン](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>エージェントのアンインストール
エージェントを削除するには、 **[プログラムのアンインストールまたは変更]** に移動して、次のものをアンインストールします。

- Microsoft Azure AD Connect Agent Updater
- Microsoft Azure AD Connect プロビジョニング エージェント
- Microsoft Azure AD Connect Provisioning Agent Package

![エージェントの削除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>次の手順 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)

