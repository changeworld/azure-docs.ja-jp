---
title: Windows Virtual Desktop 管理ツール - Azure
description: Windows Virtual Desktop 管理ツールに関する問題のトラブルシューティング方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb9206d2c2c15c036b0cf1184b79ed126966eb3b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614076"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Windows Virtual Desktop 管理ツールのトラブルシューティング

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Fall 2019 リリースに適用されます。

この記事では、Windows Virtual Desktop 管理ツールのデプロイ中に発生する可能性のある問題とその解決方法について説明します。

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>エラー:管理ツール サービスは構成されているが、自動セットアップが失敗する

管理ツールのサービスのセットアップを正常に完了しても、自動セットアップが失敗する場合、次のエラー メッセージが表示されます。

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

これは通常、次の 2 つのいずれかを意味します。

- ユーザーは、サブスクリプションおよびテナント レベルのグローバル管理者の所有者アクセス許可を持っていますが、Azure にサインインできません。
- ユーザーのアカウント設定で、多要素認証が有効になっています。

この問題を解決するには:

1. Azure Active Directory ユーザー プリンシパル名用に作成したユーザーのサブスクリプション レベルが "共同作成者" であることを確認します。
2. UPN アカウントを使用して <portal.azure.com> にサインインし、アカウント設定を確認し、多要素認証が有効ではないことを確認します。 有効な場合は無効にします。
3. Windows Virtual Desktop の [同意] ページにアクセスし、サーバーおよびクライアント アプリに同意済みであることを確認します。
4. 問題が引き続き発生する場合は、「[管理ツールをデプロイする](manage-resources-using-ui.md)」のチュートリアルを参照してください。

## <a name="error-job-with-specified-id-already-exists"></a>エラー:指定された ID のジョブが既に存在する

"指定された ID のジョブが既に存在しています" というエラー メッセージが表示される場合は、テンプレートをデプロイするときに [アプリケーション名] パラメーターに一意の名前を指定していないことが原因です。

この問題を解決するには、[アプリケーション名] パラメーターを入力して管理ツールを再デプロイします。

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>管理ツールを開くときに同意のプロンプトがすぐに表示されない

管理ツールをデプロイするときに、同意のプロンプトがすぐに表示されない場合があります。 これは、Azure Web アプリ サービスの読み込みに通常よりも長い時間がかかっていることを意味します。 Azure Web の読み込みが完了すると、プロンプトが表示されます。

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>ユーザーが米国東部リージョンに管理ツールをデプロイできない

顧客がリージョンを米国東部に設定している場合、管理ツールをデプロイすることはできません。

この問題を解決するには、別のリージョンに管理ツールをデプロイします。 別のリージョンにツールを再デプロイしても、ユーザー エクスペリエンスには影響しません。

## <a name="next-steps"></a>次のステップ

- エスカレーション トラックの詳細については、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview-2019.md)」を参照してください。
- Windows Virtual Desktop ツールの問題の報告方法については、「[Remote Desktop Services 用の ARM テンプレート](https://github.com/Azure/RDS-Templates/blob/master/README.md)」を参照してください。
- 管理ツールをデプロイする方法については、「[管理ツールをデプロイする](manage-resources-using-ui.md)」を参照してください。