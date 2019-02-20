---
title: Azure Stack Development Kit (ASDK) のインストール | Microsoft Docs
description: Azure Stack Development Kit (ASDK) をインストールする方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: a58f5a3794d352fa8671321f5a30d74d2598df75
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977730"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Azure Stack Development Kit (ASDK) のインストール
[ASDK ホスト コンピューターの準備](asdk-prepare-host.md)ができたら、この記事の次の手順に従って ASDK を CloudBuilder.vhdx イメージにデプロイできます。

## <a name="install-the-asdk"></a>ASDK のインストール
この記事の手順は、**asdk-installer.ps1** PowerShell スクリプトをダウンロードして実行することによって提供されるグラフィカル ユーザー インターフェイス (GUI) を使って ASDK をデプロイする方法を示しています。

> [!NOTE]
> Azure Stack Development Kit のインストーラー ユーザー インターフェイスは、WCF および PowerShell に基づくオープン ソースのスクリプトです。


1. ホスト コンピューターが CloudBuilder.vhdx イメージで正常に起動した後、ASDK インストールの[開発キットのホスト コンピューターを準備](asdk-prepare-host.md)するときに指定した管理者資格情報を使ってログインします。 これは、開発キット ホストのローカル管理者の資格情報と同じである必要があります。
2. 管理者特権の PowerShell コンソールを開き、**&lt;ドライブ文字>\AzureStack_Installer\asdk-installer.ps1** PowerShell スクリプトを実行します。 このスクリプトが現在、CloudBuilder.vhdx イメージの C:\ とは別のドライブに存在する可能性があることに注意してください。 **[インストール]** をクリックします。

    ![](media/asdk-install/1.PNG) 

3. ID プロバイダーの **[種類]** ドロップダウン ボックスで、**[Azure China Cloud]**、**[Azure US Government]**、**[AD FS]**、または **[Azure Cloud]** を選択します。 **[ローカル管理者のパスワード]** の **[パスワード]** ボックスに、(現在の構成済みのローカル管理者パスワードと同じ) ローカル管理者パスワードを入力し、**[次へ]** をクリックします。

    ![](media/asdk-install/2.PNG) 
  
   Azure サブスクリプション ID プロバイダーを選択する場合は、インターネット接続、Azure AD ディレクトリ テナントのフルネーム (*domainname*.onmicrosoft.com の形式) または Azure AD の検証済みのカスタム ドメイン名、および指定されたディレクトリの全体管理者の資格情報が必要です。<br><br>デプロイの後、Azure Active Directory の全体管理者のアクセス許可は必要ありません。 ただし、一部の操作では、全体管理者の資格情報が必要な場合があります。 たとえば、リソース プロバイダーのインストーラー スクリプトや、アクセス許可を付与する必要のある新機能などがあります。 アカウントの全体管理者のアクセス許可を一時的に再配置するか、*既定のプロバイダー サブスクリプション*の所有者である個別の全体管理者アカウントを使用するかのいずれかを行うことができます。<br><br>ID プロバイダーとして AD FS を使用している場合は、既定のスタンプ ディレクトリ サービスが使用されます。 サインインに使用する既定のアカウントは azurestackadmin@azurestack.local であり、使用するパスワードは、セットアップの一部として指定したものです。

  > [!NOTE]
  > AD FS を ID プロバイダーとして使って、インターネット接続されていない Azure Stack 環境を使う場合でも、最適な結果を得るには、インターネットへの接続中に ASDK をインストールすることが最良です。 そうすることにより、開発キット インストールに含まれている Windows Server 2016 の評価バージョンがデプロイ時にアクティブ化されます。

4. 開発キットに使用するネットワーク アダプターを選択して、**[次へ]** をクリックします。

    ![](media/asdk-install/3.PNG)

5. **[ネットワーク構成]** ページで、有効な**タイム サーバー IP** アドレスを指定します。 この必須フィールドでは、開発キットによって使われるタイム サーバーを設定します。 このパラメーターは、有効なタイム サーバーの IP アドレスとして指定する必要があります。 サーバー名はサポートされていません。

      > [!TIP]
      > タイム サーバーの IP アドレスを検索するには、[ntppool.org](https://www.ntppool.org/) にアクセスするか、time.windows.com に ping を実行します。 

    **オプションで**、**DNS フォワーダー** IP アドレスを指定できます。 DNS サーバーが Azure Stack のデプロイの一部として作成されます。 ソリューション内のコンピューターにスタンプ外の名前解決を許可するには、既存のインフラストラクチャの DNS サーバーを提供します。 スタンプ内の DNS サーバーが、このサーバーに不明な名前解決の要求を送信します。

    ![](media/asdk-install/4.PNG)

6. **[Verifying network interface card properties]\(ネットワーク インターフェイス カードのプロパティを確認しています\)** ページに、進行状況バーが表示されます。 検証が完了したら、**[次へ]** をクリックします。

    ![](media/asdk-install/5.PNG)

7. **[概要]** ページで、**[Deploy]\(デプロイ\)** をクリックして、開発キットのホスト コンピューターへの ASDK のインストールを開始します。

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > ここで、開発キットのインストールに使用される PowerShell のセットアップ コマンドをコピーすることもできます。 これは、[PowerShell を使って ASDK をホスト コンピューターに再デプロイする](asdk-deploy-powershell.md)必要がある場合に役立ちます。

8. Azure AD デプロイを実行している場合、セットアップを開始して数分後に、Azure AD のグローバル管理者アカウントの資格情報を入力するように求めるメッセージが表示されます。

9. デプロイ処理には数時間かかります。その間に、ホスト コンピューターは自動的に 1 回再起動されます。 デプロイの進行状況を監視する場合は、開発キットのホストが再起動された後に、azurestack\AzureStackAdmin としてサインインします。 デプロイが成功すると、PowerShell コンソールに次のように表示されます:**COMPLETE:Action 'Deployment' (完了: アクション 'デプロイ')**。 
    > [!IMPORTANT]
    > コンピューターが azurestack ドメインに参加した後にローカル管理者としてサインインした場合、デプロイの進行状況は表示されません。 デプロイは再実行せず、代わりに azurestack\AzureStackAdmin としてサインインし、それが実行中であることを確認します。

    ![](media/asdk-install/7.PNG)

これで、ASDK を正常にインストールしました。

なんらかの理由でデプロイが失敗した場合は、最初から[デプロイし直す](asdk-redeploy.md)か、同じ管理者特権の PowerShell ウィンドウで次の PowerShell コマンドを使って、前回の成功した手順からデプロイを再開することができます。

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>次の手順
[デプロイ後の構成](asdk-post-deploy.md)
