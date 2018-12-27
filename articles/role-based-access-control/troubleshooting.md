---
title: Azure での RBAC のトラブルシューティング | Microsoft Docs
description: Azure ロールベースのアクセス制御 (RBAC) に関する問題を解決します。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d1a0e46fe348bbc60a4d02a4727a9bb27cb26742
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223298"
---
# <a name="troubleshoot-rbac-in-azure"></a>Azure での RBAC のトラブルシューティング

この記事では、Azure portal でロールを使用するときに予想されることをユーザーが理解し、アクセスの問題を解決できるように、ロールベースのアクセス制御 (RBAC) に関する一般的な質問に回答します。

## <a name="web-app-features-that-require-write-access"></a>書き込みアクセス権限を必要とする Web アプリ機能

1 つの Web アプリに対する読み取り専用アクセスをユーザーに付与する場合、予期しない機能が無効になることがあります。 以下の管理機能には、Web アプリに対する**書き込み**アクセス権 (共同作成者または所有者) が必要なので、読み取り専用のシナリオでは利用できません。

* コマンド (開始や停止など)
* 一般的な構成、スケール設定、バックアップ設定、監視設定などの設定の変更。
* 発行資格情報およびその他の機密情報 (アプリケーション設定や接続文字列など) へのアクセス。
* ストリーミング ログ
* 診断ログの構成
* コンソール (コマンド プロンプト)
* アクティブな最新のデプロイ (ローカル Git の継続的デプロイの場合)
* 所要時間の見積もり
* Web テスト
* 仮想ネットワーク (書き込みアクセス権を持つユーザーが仮想ネットワークを事前に構成している場合のみ閲覧者が参照できる)。

これらのタイルのいずれにもアクセスできない場合、管理者に問い合わせて Web アプリに対する共同作成者アクセス権を得る必要があります。

## <a name="web-app-resources-that-require-write-access"></a>書き込みアクセス権限を必要とする Web アプリ リソース

相互作用する数種類のリソースがあると、Web アプリは複雑になります。 複数の Web サイトから成る代表的なリソース グループを以下に示します。

![Web アプリ リソース グループ](./media/troubleshooting/website-resource-model.png)

結果として、Web アプリのみに対するアクセス権を付与すると、Azure Portal の Web サイト ブレード上の多数の機能が使用できなくなります。

以下の項目には、Web サイトに対応する **App Service プランへ**の**書き込み**アクセス権が必要です。  

* Web アプリの価格レベル (Free または Standard) の表示  
* スケールの構成 (インスタンスの数、仮想マシンのサイズ、自動スケールの設定)  
* クォータ (ストレージ、帯域幅、CPU)  

以下の項目には、Web サイトが含まれる**リソース グループ**全体に対する**書き込み**アクセス権が必要です。  

* SSL 証明書とバインド (SSL 証明書は同じリソース グループや地理的な場所にあるサイト間で共有できるため)  
* アラート ルール  
* 自動スケールの設定  
* Application Insights コンポーネント  
* Web テスト  

## <a name="virtual-machine-features-that-require-write-access"></a>書き込みアクセス権限を必要とする仮想マシン機能

Web アプリと同様、仮想マシン ブレード上の機能にも、仮想マシンかリソース グループ内の他のリソースに対する書き込みアクセス権が必要なものがあります。

仮想マシンは、ドメイン名、仮想ネットワーク、ストレージ アカウント、アラート ルールなどのリソースと関連しています。

以下の項目には、**仮想マシン**に対する**書き込み**アクセス権が必要です。

* エンドポイント  
* IP アドレス  
* ディスク  
* 拡張機能  

以下には、**仮想マシン**と、その仮想マシンが含まれる**リソース グループ** (とドメイン名) の両方に対する**書き込み**アクセス権が必要です。  

* 可用性セット  
* 負荷分散セット  
* アラート ルール  

これらのタイルのいずれにもアクセスできない場合、管理者に問い合わせてリソース グループに対する共同作成者アクセス権を入手してください。

## <a name="azure-functions-and-write-access"></a>Azure Functions と書き込みアクセス権限

[Azure Functions](../azure-functions/functions-overview.md) の一部の機能では、書き込みアクセスが必要です。 たとえば、ユーザーに閲覧者ロールが割り当てられている場合、そのユーザーは関数アプリ内の関数を表示することができません。 ポータルには **(アクセスなし)** が表示されます。

![Function App のアクセスなし](./media/troubleshooting/functionapps-noaccess.png)

閲覧者は、**[プラットフォーム機能]** タブをクリックし、**[すべての設定]** をクリックすることで、関数アプリ (Web アプリに類似) に関連する一部の設定を表示できます。ただし、これらの設定を変更することはできません。

## <a name="rbac-changes-are-not-being-detected"></a>RBAC の変更が検出されない

Azure Resource Manager は、パフォーマンスを高めるために構成やデータをキャッシュすることがあります。 ロールの割り当てを作成または削除した場合、その変更が有効となるまでに最大 30 分かかる場合があります。 Azure portal、Azure PowerShell、Azure CLI のいずれかを使用している場合は、一度サインアウトしてからサインインすることで、ロールの割り当てを強制的に最新の情報に更新し、その変更を有効にすることができます。 REST API 呼び出しでロールの割り当てを変更する場合は、アクセス トークンを更新することによって、最新の情報への更新を強制することができます。

## <a name="next-steps"></a>次の手順
* [RBAC と Azure portal を使用してアクセスを管理する](role-assignments-portal.md)
* [RBAC の変更のアクティビティ ログを表示する](change-history-report.md)

