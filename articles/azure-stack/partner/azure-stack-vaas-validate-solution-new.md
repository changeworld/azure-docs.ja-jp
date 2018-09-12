---
title: 新しい Azure Stack ソリューションの検証 | Microsoft Docs
description: サービスとしての検証を使って新しい Azure Stack ソリューションを検証する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1e908a8cf5576ce3bc3d58d1ef6f29d596ebc58b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158179"
---
# <a name="validate-a-new-azure-stack-solution"></a>新しい Azure Stack ソリューションの検証

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

ソリューションの検証ワークフローを使って新しい Azure Stack ソリューションを認定する方法について説明します。

Azure Stack ソリューションは、Microsoft と共同で合意され、かつ Windows Server ロゴ認定要件に合格したハードウェア部品表 (BoM) です。 ソリューションの "*区分変更*" につながるような変化がハードウェア BoM にあった場合に、ソリューションの検証ワークフローを使うこともできます。 ソリューションの**区分変更**や**再認定**を引き起こす可能性のある要因についてご質問があれば、[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) にお問い合わせください。

ソリューションを認定するために、ワークフローは 2 回実行してください。 1 回目は、サポートされる "*最小*" 構成で実行します。 2 回目は、"*最大*" 構成で実行します。 両方の構成ですべてのテストに合格したソリューションが Microsoft によって認定されます。

このクイック スタートでは、ソリューションを追加してテストを実行するプロセスを紹介します。

## <a name="add-a-new-solution"></a>新しいソリューションの追加

1. [検証ポータル](https://azurestackvalidation.com)にサインインします。
2. **[新しいソリューション]** を選択します。
3. ソリューションの名前を入力し、**[保存]** を選択します。

## <a name="create-a-solution-validation-workflow"></a>ソリューション検証ワークフローの作成

1. ソリューション名を選択します。
2. **[Solution Validations]\(ソリューションの検証\)** タイルの **[管理]** を選択します。

    ![ソリューションの検証](media/image2.png)

## <a name="create-a-solution-workflow"></a>ソリューション ワークフローの作成

1. **[New solution validation]\(新しいソリューション検証\)** を選択します。
2. 検証の名前を入力します。
3. **[最小]** または **[最大]** を選択します。  
    - **最小**  
    ソリューションは、サポートされる最小ノード数で構成されます。  
    - **[最大]**  
    ソリューションは、サポートされる最大ノード数で構成されます。
4. **[アップロード]** を選択して、デプロイ構成ファイルを追加します。 これは省略可能な手順です。 次のセクションの手順に従ってテスト パラメーターを追加することもできます。

    > [!note]  
    > 構成ファイルは、環境パラメーターと共通テスト パラメーターの各セクションに記載されているパラメーターをインターフェイスから追加することによって作成できます。 サービスによって生成されるファイルは、検証対象となる Azure Stack デプロイから取得できます。 その手順については、「[Workflow common parameters for Azure Stack validation as a service (Azure Stack のサービスとしての検証に使用されるワークフロー共通パラメーター)](azure-stack-vaas-parameters.md)」を参照してください。

5. 環境パラメーターを追加します。 詳細については、「[環境パラメーターの追加](#add-environmental-parameters)」を参照してください。
6. 共通のテスト パラメーターを追加します。 詳細については、「[共通のテスト パラメーターの追加](#add-common-test-parameters)」を参照してください。

    テストの定義によっては、共通のパラメーター以外に値を入力しなければならない場合があります。または、共通のパラメーターの値を上書きすることもできます。

7. **[送信]** をクリックするとテストがスケジュールされます。

## <a name="add-environmental-parameters"></a>環境パラメーターの追加

次の環境パラメーターを追加します。

| テスト パス情報 | 必須 | 説明 |
| --- | --- | --- | --- |
| Azure Stack のビルド | 必須 | Azure Stack のビルド番号 (例: 20170501.1)。この値は、有効な Azure Stack のビルド番号またはバージョンであることが必要です (例: 1.0.170330.9) |
| テナント ID | 必須 | Active Directory のテナント ID。 GUID を指定してください (例: ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| リージョン | 必須 | Azure Stack のデプロイ リージョン |
| テナントの Resource Manager エンドポイント | 必須 | テナントの Azure Resource Manager 操作のエンドポイント (例: https://management.loc-ext.domain.com) |
| 管理者の Resource Manager エンドポイント | 必要なし | テナントの Azure Resource Manager 操作のエンドポイント (例: https://management.loc-ext.domain.com) |
| 外部 FQDN | 必要なし | エンドポイントのサフィックスとして使用される外部の完全修飾ドメイン名  (例: local.azurestack.external、redmond.contoso.com) |
| ノードの数 | 必須 | ソリューション内のノード数。 |

## <a name="add-common-test-parameters"></a>共通のテスト パラメーターの追加

次に示した共通のテスト パラメーターを追加します。

| テスト パス情報 | 必須 | 説明 |
| --- | --- | --- |
| テナント ユーザー名 | 必須 | テナント ユーザー名 (例: tenant@contoso.onmicrosoft.com) |
| テナント パスワード | 必須 | テナントのパスワード。 |
| サービス管理者のユーザー名 | 必要なし | テナント ユーザー名 (例: tenant@contoso.onmicrosoft.com) |
| サービス管理者のパスワード | 必要なし | サービス管理者のユーザー名 (例: serviceadmin@contoso.onmicrosoft.com) |
| クラウド管理者のユーザー名 | 必要なし | Azure Stack ドメイン管理者アカウント (例: contoso\cloudadmin) |
| クラウド管理者のパスワード | 必要なし | |
|  診断接続文字列 | 必要なし | テストの実行中に診断ログがコピーされる Azure Storage アカウントの SAS URI。 「[Create an Azure storage blob to store logs (ログを格納する Azure Storage Blob の作成)](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)」を参照してください。 <br><br>**[診断接続文字列]** 共通パラメーターの値はサービスによって格納され、ワークフロー内でこのパラメーターを使うすべてのテストに、スケジュールされたタイミングで与えられます。 SAS URL の有効期限まで 30 日を切ると、共通パラメーター ページで新しい SAS URL を入力するよう求められます。 |
| タグ - 名前 | 必要なし |  ワークフローにラベルを付けるために、わかりやすいタグを入力できます。 これがタグの名前になります。 |
| タグ - 値 | 必要なし | ワークフローにラベルを付けるために、わかりやすいタグを入力できます。 これがタグの値になります。 |

## <a name="next-steps"></a>次の手順

- [テストのスケジュール変更とキャンセル](azure-stack-vaas-monitor-test.md#reschedule-a-test)
- [Azure Stack のサービスとしての検証](https://docs.microsoft.com/azure/azure-stack/partner)について、さらに詳しい情報をご覧ください。