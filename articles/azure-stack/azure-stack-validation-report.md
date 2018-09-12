---
title: Azure Stack 用検証レポート | Microsoft Docs
description: Azure Stack 適合性チェッカー レポートを使用して、検証結果を確認します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 06b5660a9428e98d2e99b5d447a05700968ec884
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381915"
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
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

出力結果は、以下のイメージのようになります。

![view-report](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>レポートの概要を表示する
レポートの概要を表示するには、**-Summary** スイッチを、PowerShell コマンド ラインの末尾に追加します。 例:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

概要には、結果がない検証が表示されます。この概要には、完了した結果が成功したか失敗したかが示されます。 出力結果は、以下のイメージのようになります。

![report-summary](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>フィルター処理されたレポートを表示する
1 種類の検証でフィルター処理されたレポートを表示するには、**-ReportSections** パラメーターを使用して、次のいずれかの値を指定します。
- 証明書
- AzureRegistration
- AzureIdentity
- [ジョブ]   
- All  

たとえば、証明書のレポート概要のみを表示するには、次の PowerShell コマンド ラインを使用します。 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>関連項目
[Start-AzsReadinessChecker コマンドレット リファレンス](azure-stack-azsreadiness-cmdlet.md)
