---
title: Azure Stack のサービスとしての検証用テスト パラメーター | Microsoft Docs
description: Azure Stack のサービスとしての検証用の構成ファイルとテスト成功パラメーターに関するリファレンス トピック。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9f042779e3463f0d75dc6327b3553156edbf162a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162225"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Azure Stack のサービスとしての検証用テスト パラメーター

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

サービスとしての検証 (VaaS) からテスト スイートを実行する前に、ソリューションを選択し、テスト成功を作成します。

- 登録済みの VaaS テナント資格情報でサインインします。
- 新しいソリューションを作成 (**[ソリューションの追加]** を選択) するか、既存のソリューションを選択します。
- **[テスト成功]** ワークフロー タイルから **[開始]** ボタンを選択します。

> [!Note]  
> 検証する一意のマシン セット/ハードウェア構成ごとに新しいソリューションのエントリを作成し、さらにそのマシン セット上にあるすべてのビルドのデプロイ用に新しいテスト成功を作成します。

**[テスト成功情報]** ページで、テストを実行するために必要なパラメーターを入力する必要があります。 新しいテスト成功または検証の実行を開始するときに、これらのパラメーターを指定します。 ほとんどのパラメーターの値は、Azure Stack のデプロイ時に指定したものと同じです。

[テスト パラメーターの表](#test-parameters)に記載されている値を手動で入力することも、Azure Stack スタンプ情報全体を収めたデプロイ構成ファイルをアップロードすることもできます。 アップロードすると、ファイルの値がポータルに読み込まれます。

> [!Note]  
> 構成ファイルを見つけてポータルに読み込めば、テスト パラメーター値の検索と入力ができます。

## <a name="export-the-test-parameters-using-powershell"></a>PowerShell を使用したテスト パラメーターのエクスポート

1. DVM マシンまたは Azure Stack 環境にアクセスできる任意のマシンにサインインします。
2. 管理者特権の PowerShell ウィンドウを開き、以下を実行します。

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. **stampinfoproperties.json** を VaaS ポータルにアップロードします。

## <a name="find-the-test-parameters-in-the-configuration-file"></a>構成ファイル内のテスト パラメーターの検索

ECE **構成ファイル**を開いて、テスト パラメーター値を検索することもできます。 このファイルは、DVM マシンの次のパスにあります。  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>テスト パラメーター 

| パラメーター    | 説明 |
|-------------|-----------------|
| Azure Stack ビルド                      | デプロイされた Azure Stack のビルド番号またはバージョン番号。例: 1.0.170330.0 | 
| テナント ID                              | Azure Stack のデプロイ時に指定される Azure Active Directory テナント。 構成ファイルで **AADTenant** を検索し、`Id` タグ内の **GUID** 値を選択します。 | 
| リージョン                                 | Azure Stack のデプロイ リージョン。 構成ファイルで **Region** を検索します。 | 
| テナントの Resource Manager                | テナントの Azure Resource Manager エンドポイント。例: `https://management.<ExternalFqdn>` | 
| 管理者の Resource Manager                 | 管理者の Azure Resource Manager エンドポイント。 例: `https://adminmanagement.<ExternalFqdn>` | 
| 外部 FQDN                          | 環境の外部 FQDN。 構成ファイルで **ExternalFqdn** を検索します。 | 
| テナント ユーザー                            | Azure AD ディレクトリのサービス管理者によって既にプロビジョニングされている、またはプロビジョニングする必要がある Azure Active Directory テナント管理者。 テナント アカウントのプロビジョニングの詳細については、「[新しい Azure Stack テナント アカウントを Azure Active Directory に追加する](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad)」を参照してください。 この値は、リソース (VM、ストレージ アカウントなど) のプロビジョニングやワークロードを実行する目的で、テンプレートのデプロイなどのテナント レベルの操作を行うためにテストによって使用されます。 | 
| サービス管理者ユーザー             | Azure Stack のデプロイ中に指定される Azure AD ディレクトリ テナントの Azure Active Directory の管理者。 構成ファイルで **AADTenant** を検索し、構成ファイルの `UniqueName` タグの値を選択します。 | 

## <a name="checks-before-starting-the-tests"></a>テスト開始前のチェック

テストではリモート操作が実行されます。 テストを実行するマシンは、Azure Stack エンドポイントにアクセスできる必要があります。 それ以外の場合、テストは機能しません。 VaaS のオンプレミスのエージェントを使用する場合、エージェントを実行するマシンを使用します。 次のチェックを実行すると、マシンが Azure Stack ポイントにアクセスできることを確認できます。

1. ベース URI に到達できることを確認します。 CMD プロンプトまたは Bash シェルを開き、次のコマンドを実行します。

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. MAS ポータルに到達できることを確認するために、Web ブラウザーを開き、`https://adminportal.<EXTERNALFQDN>` に移動します。

3. テスト成功の作成時に指定した Azure AD サービス管理者の名前とパスワードの値を使用してサインインします。

## <a name="next-steps"></a>次の手順

- [Azure Stack のサービスとしての検証](https://docs.microsoft.com/azure/azure-stack/partner)について、さらに詳しい情報をご覧ください。
