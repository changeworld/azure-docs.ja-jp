---
title: Azure Stack 用検証レポート | Microsoft Docs
description: Azure Stack 適合性チェッカー レポートを使用して、検証結果を確認します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 1b2b06c02dc54c4369dd8490b714d1444d4b3b01
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986187"
---
# <a name="azure-stack-validation-report"></a>Azure Stack 検証レポート
Azure Stack 適合性チェッカー ツールでは、Azure Stack 環境のデプロイとサービスをサポートする検証が実行されます。 このツールでは、結果が .json レポート ファイルに書き込まれます。 レポートには、Azure Stack デプロイの前提条件の状態に関する詳細データと概要データが表示されます。 また、既存のAzure Stack デプロイのシークレット ローテーションについての情報も表示されます。  

 ## <a name="where-to-find-the-report"></a>レポートの場所
ツールを実行すると、結果のログが **AzsReadinessCheckerReport.json** に出力されます。 また、ツールにより、**AzsReadinessChecker.log** という名前のログも作成されます。 これらのファイルの場所は、PowerShell に検証結果と共に表示されます。

![run-validation](./media/azure-stack-validation-report/validation.png)

両方のファイルに、同じコンピューター上で実行された以降の検証チェックの結果が保持されます。  たとえば、ツールを実行して証明書を検証した後、再度実行して Azure ID を検証し、3 回目に実行では登録を検証することができます。 この 3 回の検証の結果はすべて、結果として生成される .json レポートで確認できます。  

既定では、両方のファイルが *C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json* に書き込まれます。  
- 別のレポートの場所を指定するには、実行コマンド ラインの末尾で **-OutputPath** ***&lt;パス&gt;*** パラメーターを使用します。   
- ツールの以前の実行に関する情報を *AzsReadinessCheckerReport.json* からクリアするには、実行コマンドの末尾で **-CleanReport**  パラメーターを使用します。

## <a name="view-the-report"></a>レポートを表示する
PowerShell でレポートを表示するには、レポートへのパスを **-ReportPath** の値として指定します。 このコマンドにより、レポートのコンテンツが表示されます。また、結果がまだ出力されていない検証が特定されます。

たとえば、次のように実行すると、レポートが配置されている場所で使用できる PowerShell プロンプトからレポートを表示するには、次を実行します。 
   > `Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json` 

出力結果は、以下のようになります。

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters : 
````

## <a name="view-the-report-summary"></a>レポートの概要を表示する
レポートの概要を表示するには、**-Summary** スイッチを、PowerShell コマンド ラインの末尾に追加します。 例:  
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary`  

概要には、結果がない検証が表示されます。この概要には、完了した結果が成功したか失敗したかが示されます。 出力結果は、以下のようになります。

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

    Certificate Validation found no errors or warnings.
    
############### Registration Validation Summary ###############

    Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
````


## <a name="view-a-filtered-report"></a>フィルター処理されたレポートを表示する
1 種類の検証でフィルター処理されたレポートを表示するには、**-ReportSections** パラメーターを使用して、次のいずれかの値を指定します。
- 証明書
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- [ジョブ]   
- All  

たとえば、証明書のレポート概要のみを表示するには、次の PowerShell コマンド ラインを使用します。 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>関連項目
