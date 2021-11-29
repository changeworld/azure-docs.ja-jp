---
title: Azure ハイブリッド特典用に SQL Server ライセンス割り当てを作成する
description: この記事では、Azure ハイブリッド特典用に SQL Server ライセンス割り当てを作成する方法を説明します。
author: bandersmsft
ms.author: banders
ms.date: 11/11/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisri
ms.openlocfilehash: e9ac5023807f435c20ddeceb097e5dce43316eae
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708488"
---
# <a name="create-sql-server-license-assignments-for-azure-hybrid-benefit"></a>Azure ハイブリッド特典用に SQL Server ライセンス割り当てを作成する

Azure portal の新しい一元化された Azure ハイブリッド特典エクスペリエンスでは、アカウント レベルまたは特定のサブスクリプション レベルでの SQL Server ライセンス割り当てがサポートされます。 アカウント レベルで割り当てが作成されると、Azure ハイブリッド特典割引が、アカウント内のすべてのサブスクリプション内の SQL リソースに、割り当てで指定されたライセンス数まで自動的に適用されます。

各ライセンス割り当てに対してスコープが選択され、そのスコープにライセンスが割り当てられます。 各スコープには、複数のライセンス エントリを含めることができます。

## <a name="prerequisites"></a>前提条件

SQL Server ライセンス割り当てを作成するには、次の前提条件を満たす必要があります。

- 組織に、サポート対象の契約の種類と、サポート対象のオファーがある。
- SQL ライセンスを割り当てるアクセス許可を持つロールのメンバーである。
- 組織に、ソフトウェア アシュアランスを含む SQL Server コア ライセンス、または Azure への割り当てに使用できるコア サブスクリプション ライセンスがある。
- 組織が、IaaS 拡張機能を使用した Azure SQL VM の自動登録に登録されている。 詳細については、「[SQL IaaS Agent 拡張機能への自動登録](../../azure-sql/virtual-machines/windows/sql-agent-extension-automatic-registration-all-vms.md)」を参照してください。
  > [!IMPORTANT]
  > この前提条件を満たしていないと、現在の Azure ハイブリッド特典の使用状況に関する不完全なデータが Azure によって作成されます。 その状況により、ライセンスの割り当てが正しく行われなくなり、SQL Server ライセンスに対する不要な従量課金制料金が発生する可能性があります。

前提条件のロールは、契約の種類によって異なります。

| アグリーメントの種類 | 必要なロール | サポートされるオファー |
| --- | --- | --- |
| Enterprise Agreement | _エンタープライズ管理者_<p> このロールのメンバーになる方法の詳細については、「[別のエンタープライズ管理者を追加する](../manage/ea-portal-administration.md#add-a-department-administrator)」を参照してください。 | - MS-AZR-0017P (Microsoft Azure Enterprise)<br>- MS-AZR-USGOV-0017P (Azure Government Enterprise) |
| Microsoft 顧客契約 | *請求先アカウント所有者*<br> *請求先アカウント共同作成者* <br> *課金プロファイル所有者*<br> *課金プロファイル共同作成者*<br> このロールのメンバーになる方法の詳細については、[課金ロールの管理](../manage/understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)に関するページを参照してください。 | MS-AZR-0017G (Microsoft Azure プラン)|
| WebDirect/従量課金制 | 使用不可 | なし |
| CSP/パートナー主導の顧客 | 使用不可 | なし |

> [!NOTE]
> スコープに対して一元的にライセンスを割り当ててる操作は、スポンサー、MSDN クレジット サブスクリプション、または MPN サブスクリプションに使うことはできません。 Dev/Test サブスクリプション (MS-AZR-0148P または MS-AZR-0023P という種類のオファー) では、SQL ソフトウェアの使用は無料です。

## <a name="create-a-sql-license-assignment"></a>SQL ライセンス割り当てを作成する

次の手順では、 **[コストの管理と請求]** から **[予約とハイブリッド特典]** に移動します。 Azure ホーム ページからは **[予約]** に移動しないでください。 このように移動すると、ライセンス割り当てエクスペリエンスを表示するために必要なスコープが表示されません。 

1. Azure portal にサインインし、 **[コストの管理と請求]** に移動します。  
    :::image type="content" source="./media/create-sql-license-assignments/select-cost-management.png" alt-text="Azure portal での [コストの管理と請求] への移動を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/select-cost-management.png" :::
1. 契約の種類に応じて、以下に示す 2 つの手順のいずれかを使用します。
    - Enterprise Agreement を結んでいる場合は、課金スコープを選択します。  
        :::image type="content" source="./media/create-sql-license-assignments/select-billing-scope.png" alt-text="EA 課金スコープの選択を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/select-billing-scope.png" :::
    - Microsoft 顧客契約を結んでいる場合は、課金プロファイルを選択します。  
        :::image type="content" source="./media/create-sql-license-assignments/select-billing-profile.png" alt-text="課金プロファイルの選択を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/select-billing-profile.png" :::
1. 左側のメニューで **[予約とハイブリッド特典]** を選択します。  
    :::image type="content" source="./media/create-sql-license-assignments/select-reservations.png" alt-text="[予約とハイブリッド特典] の選択を示すスクリーンショット。"  :::
1. **[追加]** を選択し、一覧で **[Azure ハイブリッド特典 (プレビュー)]** を選択します。  
    :::image type="content" source="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" alt-text="Azure ハイブリッド特典の選択を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" :::
1. 次の画面で、 **[ライセンス割り当ての開始]** を選択します。  
    :::image type="content" source="./media/create-sql-license-assignments/get-started-centralized.png" alt-text="[SQL ハイブリッド特典の追加] の選択を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/get-started-centralized.png" :::
1. スコープを選択し、各 SQL Server エディションに使用するライセンス数を入力します。 特定のエディションに割り当てるライセンスがない場合は、「0」を入力します。  
    > [!NOTE]
    > この手順を実行する場合は、スコープ レベルのマネージド ライセンス エクスペリエンスに入力したエントリが正確であり、ライセンスの義務を満たすことを判断する責任があります。 ライセンス割り当てを行う際は、それに役立つライセンス使用状況情報が表示されます。 ただし、さまざまな要因により、表示される情報が不完全または不正確である可能性があります。
    >
    > 入力したライセンス数が、現在使用している数より少ない場合は、"_このスコープで Azure ハイブリッド特典 に現在使用しているライセンスよりも少ない数が入力されました。このスコープの請求金額が増加します。_ "という警告メッセージが表示されます。  
    
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition.png" alt-text="スコープの選択とライセンス数を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition.png" :::
1. 必要に応じて、 **[使用状況の詳細]** タブを選択して、リソース スコープで有効になっている、現在の Azure ハイブリッド特典使用状況を表示します。  
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" alt-text="[使用状況の詳細] タブを示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" :::
1. **[追加]** を選択します。
1. 必要に応じて、既定のライセンス割り当て名を変更します。 レビュー日は、1 年後の日付に自動的に設定され、変更することはできません。 その目的は、ライセンス割り当てを定期的に確認するよう促すことです。  
    :::image type="content" source="./media/create-sql-license-assignments/license-assignment-commit.png" alt-text="既定のライセンス割り当て名を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/license-assignment-commit.png" :::
1. 選択内容を確認した後に **[次へ: 確認 と適用]** を選択します。
1. **[&quot;適用&quot; の選択]** 構成証明オプションを選択して、Azure ハイブリッド特典 と十分な SQL Server ライセンスを適用する権限があることと、割り当てられている限りライセンスを維持することを確定します。  
    :::image type="content" source="./media/create-sql-license-assignments/confirm-apply-attestation.png" alt-text="構成証明オプションを示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/confirm-apply-attestation.png" :::
1. **[適用]** を選択し、次に **[はい]** を選択します。
1. 割り当てられたライセンスの一覧が表示されます。  
    :::image type="content" source="./media/create-sql-license-assignments/assigned-licenses.png" alt-text="割り当てられたライセンスの一覧を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/assigned-licenses.png" ::: 

## <a name="track-assigned-license-use"></a>割り当てられたライセンスの使用状況を追跡する

**[コストの管理と請求]** に移動し、 **[予約とハイブリッド特典]** を選択します。

すべての予約とライセンスの割り当ての一覧が表示されます。 ライセンス割り当てのみが表示されるように結果をフィルター処理する場合は、**SQL ハイブリッド特典** のフィルターを設定します。

:::image type="content" source="./media/create-sql-license-assignments/view-the-assignments.png" alt-text="予約とライセンスの一覧を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/view-the-assignments.png" :::

**[最終日の使用状況]** または **[7 日間の使用状況]** で、割合 (空白またはゼロの場合もあります) を選択して、割り当ての使用状況履歴を詳細を表示します。

:::image type="content" source="./media/create-sql-license-assignments/assignment-utilization-view.png" alt-text="割り当ての使用状況の詳細を示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/assignment-utilization-view.png" :::

ライセンス割り当ての使用状況が 100% の場合は、スコープ内の一部のリソースで、SQL Server にの従量課金制料金が発生していると考えられます。 ライセンス割り当てエクスペリエンスを再度使用して、割り当てられたライセンスの対象となっている使用量を確認することをお勧めします。 その後、調達部門またはソフトウェア資産管理部門との相談、使用可能な追加ライセンスの確認、ライセンスの割り当てなど、以前と同じプロセスを実行します。

## <a name="changes-after-license-assignment"></a>ライセンス割り当て後の変更

SQL ライセンス割り当てを作成した後は、Azure portal での Azure ハイブリッド特典のエクスペリエンスが変わります。

- 個々の SQL リソース用に構成されている既存の Azure ハイブリッド特典の選択は適用されなくなります。 代わりに、サブスクリプションまたはアカウント レベルで作成された SQL ライセンス割り当てが適用されます。
- ハイブリッド特典オプションは、SQL リソース構成内と同様には表示されません。
- ハイブリッド特典をプログラムによって構成するアプリケーションまたはスクリプトは、引き続き機能しますが、設定の効果はありません。
- SQL ソフトウェア割引が、スコープ内の SQL リソースに適用されます。 スコープは、リソースが作成されたアカウントのサブスクリプション用に作成されているライセンス割り当てのライセンス数に基づいています。
- ハイブリッド特典用に構成された特定のリソースには、他のリソースによってすべてのライセンスが使用されている場合は、割引が適用されないことがあります。 ただし、ライセンス数に基づいて最大割引がスコープに適用されます。 割引の適用方法の詳細については、「[一元管理された Azure ハイブリッド特典とは](overview-azure-hybrid-benefit-scope.md)」を参照してください。

## <a name="cancel-a-license-assignment"></a>ライセンス割り当てを取り消す

ライセンス割り当てを取り消す前に、ライセンスの状況を確認してください。 ライセンス割り当てを取り消すと、その割引が受けられなくなります。 したがって、Azure の請求金額が増加する可能性があります。 残っている最後のライセンス割り当てを取り消した場合は、Azure ハイブリッド特典管理が個々のリソース レベルに戻ります。

### <a name="to-cancel-a-license-assignment"></a>ライセンス割り当てを取り消すには:

1. 予約とライセンス割り当ての一覧で、いずれかのライセンス割り当てを選択します。
1. ライセンス割り当ての詳細のページで **[キャンセル]** を選択します。  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment-symbol.png" alt-text="[キャンセル] オプションを示すスクリーンショット。" :::
1. [キャンセル] ページで通知を確認し、[**はい、キャンセルします]** を選択します。  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment.png" alt-text="[キャンセル] ページを示すスクリーンショット。" lightbox="./media/create-sql-license-assignments/cancel-assignment.png" :::

## <a name="next-steps"></a>次のステップ

- 「[一元管理された Azure ハイブリッド特典の FAQ](faq-azure-hybrid-benefit-scope.yml)」を確認します。
- [一元管理された Azure ハイブリッド特典の概要](overview-azure-hybrid-benefit-scope.md)に関するページで、割引が適用される方法について理解します。