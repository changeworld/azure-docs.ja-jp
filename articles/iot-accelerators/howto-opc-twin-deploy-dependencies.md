---
title: Azure で OPC Twin クラウドの依存関係をデプロイする方法 | Microsoft Docs
description: この記事では、ローカル開発とデバッグに必要な OPC Twin Azure 依存関係をデプロイする方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645814"
---
# <a name="deploying-dependencies-for-local-development"></a>ローカル開発の依存関係をデプロイする

> [!IMPORTANT]
> この記事は更新される予定ですが、最新の内容については、[Azure 産業用 IoT](https://azure.github.io/Industrial-IoT/) に関するページを参照してください。

この記事では、ローカル環境で開発とデバッグを行うために必要な Azure Platform サービスだけをデプロイする方法について説明します。   最後には、ローカル環境での開発とデバッグに必要なすべてのものを含むリソース グループがデプロイされます。

## <a name="deploy-azure-platform-services"></a>Azure Platform サービスをデプロイする

1. PowerShell および [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) 拡張機能がインストールされていることを確認します。  コマンド プロンプトまたはターミナルを開き、次を実行します。

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. プロンプトに従って、デプロイのためのリソース グループに名前を割り当てます。  スクリプトでは、Azure サブスクリプションのこのリソース グループに依存関係だけがデプロイされ、マイクロサービスはデプロイされません。  また、スクリプトによって、Azure AD にアプリケーションが登録されます。  これは、OAUTH ベースの認証をサポートするために必要です。  デプロイには数分かかることがあります。

3. スクリプトが完了したら、.env ファイルの保存を選択できます。  .env 環境ファイルは、開発用コンピューター上で実行するすべてのサービスとツールの構成ファイルです。  

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング

### <a name="resource-group-name"></a>リソース グループ名

短くてシンプルなリソース グループ名を使用してください。  この名前はリソースの命名にも使用されるので、リソースの命名要件に従っている必要があります。  

### <a name="azure-active-directory-ad-registration"></a>Azure Active Directory (AD) の登録

デプロイ スクリプトによって、Azure AD への Azure AD アプリケーションの登録が試みられます。  選択した Azure AD テナントに対する権限によっては、これが失敗する可能性があります。 次の 3 つのオプションがあります。

1. テナントの一覧から Azure AD テナントを選択した場合は、スクリプトを再起動して別のものを一覧から選択します。
2. または、プライベート Azure AD テナントをデプロイし、スクリプトを再起動して、それを使用します。
3. 認証なしで続行します。  これはローカル環境でマイクロサービスを実行しているために許されることであり、運用環境では行わないでください。  

## <a name="next-steps"></a>次の手順

既存プロジェクトへの OPC Twin サービスのデプロイが正常に済んだので、次の手順が推奨されます。

> [!div class="nextstepaction"]
> [OPC Twin モジュールをデプロイする方法について学習する](howto-opc-twin-deploy-modules.md)