---
title: 'Azure AD Connect 単一オブジェクト同期 '
description: トラブルシューティングのために、1 つのオブジェクトを Active Directory から Azure AD に同期する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104725463"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect 単一オブジェクト同期 

Azure AD Connect 単一オブジェクト同期ツールは、個々のオブジェクトを Active Directory から Azure Active Directory に同期するために使用できる PowerShell コマンドレットです。 生成されたレポートを使用して、オブジェクトごとの同期に関する問題の調査とトラブルシューティングを行うことができます。 

> [!NOTE]
> このツールでは、Active Directory から Azure Active Directory への同期がサポートされています。 Azure Active Directory から Active Directory への同期はサポートされていません。 
>
> このツールでは、追加および更新のオブジェクト変更の同期がサポートされています。 削除のオブジェクト変更の同期はサポートされていません。 

## <a name="how-it-works"></a>しくみ
単一オブジェクト同期ツールでは、インポートするソース コネクタとパーティションを検索するために、入力として Active Directory 識別名が必要です。 変更は Azure Active Directory にエクスポートされます。 このツールでは、**provisioningObjectSummary** のリソースの種類と同様の JSON 出力が生成されます。 

単一オブジェクト同期ツールでは、次の手順が実行されます。 

 1. 同期スコープにオブジェクトの (ソース) ドメイン (Active Directory コネクタとパーティション) があるかどうかを判断します。 
 2. 同期スコープにオブジェクトの (ターゲット) ドメイン (Azure Active Directory コネクタとパーティション) があるかどうかを判断します。 
 3. 同期スコープにオブジェクトの組織単位があるかどうかを判断します。 
 4. コネクタ アカウントの資格情報を使用してオブジェクトにアクセスできるかどうかを判断します。 
 5. 同期スコープにオブジェクトの種類があるかどうかを判断します。 
 6. グループ フィルターが有効になっている場合は、オブジェクトが同期スコープ内にあるかどうかを判断します。 
 7. Active Directory から Active Directory コネクタ スペースにオブジェクトをインポートします。 
 8. Azure Active Directory から Azure Active Directory コネクタ スペースにオブジェクトをインポートします。 
 9. Active Directory コネクタ スペースからオブジェクトを同期します。 
 10. Azure Active Directory コネクタ スペースから Azure Active Directory にオブジェクトをエクスポートします。 

このツールでは、JSON 出力に加えて、同期操作のすべての詳細を含む HTML レポートが生成されます。 HTML レポートは **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult-<date>.htm** にあります。 この HTML レポートを必要に応じてサポート チームと共有し、さらなるトラブルシューティングを行うこともできます。 

HTML レポートには、次のものがあります。 

|タブ|説明|
|-----|-----|
|手順|オブジェクトを同期するために実行される手順の概要が示されます。 各手順には、トラブルシューティングの詳細が記載されています。 インポート、同期、およびエクスポートの各手順には、名前、複数値、型、値、値の追加、値の削除、操作、同期規則、マッピングの種類、データ ソースなどの追加の属性情報が含まれています。| 
|トラブルシューティングと推奨事項|エラー コードと理由が表示されます。 エラー情報は、障害が発生した場合にのみ利用できます。| 
|変更されたプロパティの比較|古い値と新しい値が表示されます。 古い値が存在しない場合、または新しい値が削除された場合、そのセルは空白になります。 複数値属性の場合、カウントが表示されます。 属性名は、手順タブのへのリンクです。Azure Active Directory コネクタ スペースから Azure Active Directory へのオブジェクトをエクスポートします。属性情報には、属性の名前、複数値、型、値、値の追加、値の削除、操作、同期規則、マッピングの種類、データソースなどの属性の詳細が含まれています。| 
|まとめ|ソースとターゲットのシステムのオブジェクトの発生内容の概要と識別子が表示されます。| 

## <a name="prerequisites"></a>前提条件 

単一オブジェクト同期ツールを使用するには、2021 年 3 月の Azure AD Connect 以降のリリースを使用する必要があります。 

### <a name="run-the-single-object-sync-tool"></a>単一オブジェクト同期ツールを実行する 

単一オブジェクト同期ツールを実行するには、次の手順を実行します。 

 1. [管理者として実行] オプションを指定して、Azure AD Connect サーバーで新しい Windows PowerShell セッションを開きます。 

 2. [実行ポリシー](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy)を RemoteSigned または Unrestricted に設定します。 

 3. 同期操作が実行されていないことを確認した後、同期スケジューラを無効にします。 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. AdSync 診断モジュールをインポートします 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. 単一オブジェクト同期コマンドレットを呼び出します。 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. 同期スケジューラを再度有効にします。 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|単一オブジェクト同期の入力パラメーター|説明| 
|-----|----|
|DistinguishedName|これは必須の文字列パラメーターです。 </br></br>これは、同期とトラブルシューティングが必要な Active Directory オブジェクトの識別名です。| 
|StagingMode|これは省略可能なスイッチ パラメーターです。</br></br>このパラメーターを使用すると、Azure Active Directory への変更のエクスポートを防ぐことができます。</br></br>**注**: このコマンドレットは、同期操作をコミットします。 </br></br>**注**: Azure AD Connect ステージング サーバーでは Azure Active Directory への変更はエクスポートされません。|
|NoHtmlReport|これは省略可能なスイッチ パラメーターです。</br></br>このパラメーターは、HTML レポートが生成されないようにするために使用できます。 

## <a name="single-object-sync-throttling"></a>単一オブジェクト同期のスロットリング 

単一オブジェクト同期ツールは、オブジェクトの同期に関する問題の調査とトラブルシューティングを目的と **しています**。 スケジューラによって実行される同期サイクルを置き換えることは想定されて **いません**。 Azure Active Directory からのインポートと Azure Active Directory へのエクスポートには、スロットリングの制限が適用されます。 スロットリングの制限に達した場合は、5 分後に再試行してください。 

## <a name="next-steps"></a>次のステップ
- [オブジェクトの同期に関するトラブルシューティング](tshoot-connect-objectsync.md)
- [同期されないオブジェクトに関するトラブルシューティング](tshoot-connect-object-not-syncing.md)
- [Azure AD Connect オブジェクトおよび属性のエンドツーエンド トラブルシューティング](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
