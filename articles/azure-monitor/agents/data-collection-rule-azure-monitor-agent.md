---
title: Azure Monitor エージェント用のデータ収集の構成 (プレビュー)
description: Azure Monitor エージェントを使用して、仮想マシンからデータを収集するデータ収集ルールを作成する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/16/2021
ms.openlocfilehash: 26aa625d7ada93c4524276ee195fd279d155e8c8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710327"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent"></a>Azure Monitor エージェント用のデータ収集の構成

データ収集ルール (DCR) では、Azure Monitor に入ってくるデータを定義し、そのデータの送信先を指定します。 この記事では、Azure Monitor エージェントを使用して、仮想マシンからデータを収集するデータ収集ルールを作成する方法について説明します。

データ収集ルールの詳細については、「[Azure Monitor のデータ収集ルール](data-collection-rule-overview.md)」を参照してください。

> [!NOTE]
> この記事では、Azure Monitor エージェントのみで仮想マシンのデータを構成する方法について説明します。

## <a name="data-collection-rule-associations"></a>データ収集ルールの関連付け

DCR を仮想マシンに適用するには、仮想マシンの関連付けを作成します。 仮想マシンには複数の DCR への関連付けを指定できます。また、DCR に複数の仮想マシンを関連付けることもできます。 これにより、それそれが特定の要件に一致する一連の DCR を定義し、それらが該当する仮想マシンにのみそれらを適用することができます。 

たとえば、基幹業務アプリケーションを実行している一連の仮想マシンと SQL Server を実行しているほかの仮想マシンがある環境について考えてみます。 すべての仮想マシンに適用する 1 つの既定のデータ収集ルールと、基幹業務アプリケーション用と SQL Server 用に特別にデータを収集する個別のデータ収集ルールを指定することができます。 データ収集ルールへの仮想マシンの関連付けは、次の図のようになります。

![基幹業務アプリケーションと SQL Server をホストしている仮想マシンを示す図。基幹業務アプリケーションは central-it-default および lob-app、SQL Server は central-it-default および sql という名前のデータ収集ルールに関連付けられています。](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Azure portal でルールと関連付けを作成する

Azure portal を使用してデータ収集ルールを作成し、サブスクリプション内の仮想マシンをそのルールに関連付けることができます。 Azure Monitor エージェントは自動的にインストールされ、まだそれがインストールされていないすべての仮想マシンに対して、マネージド ID が作成されます。

> [!IMPORTANT]
> ポータルを使用してデータ収集ルールを作成すると、既存のユーザー割り当て ID (存在する場合) に加えて、ターゲット リソースのシステム割り当てマネージド ID も有効になります。 既存のアプリケーションでは、ユーザー割り当て ID を要求で指定しない限り、マシンでは既定でシステム割り当て ID が代わりに使用されます。 [詳細情報](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)

                    

> [!NOTE]
> Log Analytics にデータを送信する場合は、Log Analytics ワークスペースが存在するのと **同じリージョン** にデータ収集ルールを作成する必要があります。 ルールは、サポートされている他のリージョンのマシンに関連付けることができます。

Azure portal の **[Azure Monitor]** メニューで、 **[設定]** セクションから **[データ収集ルール]** を選択します。 **[作成]** をクリックして、新しいデータ収集ルールと割り当てを作成します。

[![データ収集ルール](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules-updated.png#lightbox)

**[追加]** をクリックして、新しいルールと一連の関連付けを作成します。 **[ルール名]** を指定し、 **[サブスクリプション]** 、 **[リソース グループ]** 、および **[リージョン]** を指定します。 これにより、DCR が作成される場所を指定します。 仮想マシンとそれらの関連付けは、テナント内の任意のサブスクリプションまたはリソース グループに配置できます。
さらに、このルールを適用できるリソースの種類を指定する適切な **[プラットフォームの種類]** を選択します。 [カスタム] を選択すると、Windows と Linux の両方の種類が許可されます。 これにより、選択したプラットフォームの種類にスコープが設定されたオプションを使用して、事前にキュレーションされた作成エクスペリエンスが可能になります。

[![データ収集ルールの基本](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics-updated.png#lightbox)

**[リソース]** タブで、データ収集ルールを適用する必要があるリソース (仮想マシン、仮想マシン スケール セット、Arc for servers) を追加します。 Azure Monitor エージェントがまだインストールされていないリソースにインストールされ、Azure マネージド ID も有効になります。

[![データ収集ルール仮想マシン](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines-updated.png#lightbox)

**[収集と配信]** タブで、 **[データ ソースの追加]** をクリックして、データのソースおよびターゲット セットを追加します。 **[データソースの種類]** を選択すると、選択する対応する詳細が表示されます。 パフォーマンス カウンターでは、定義済みのオブジェクトのセットとそのサンプリング レートから選択できます。 イベントについては、一連のログまたは機能と重大度レベルから選択できます。 

[![データ ソースの基本](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic-updated.png#lightbox)


[現在サポートされているデータ ソース](azure-monitor-agent-overview.md#data-sources-and-destinations)のその他のログとパフォーマンス カウンターを指定する、または XPath クエリを使用してイベントをフィルター処理するには、 **[カスタム]** を選択します。 それにより、収集する特定の値の [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) を指定できます。 例については、[サンプル DCR](data-collection-rule-overview.md#sample-data-collection-rule) を参照してください。

[![データ ソース カスタム](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom-updated.png#lightbox)

**[送信先]** タブで、データ ソースの 1 つ以上の送信先を追加します。 Windows イベントおよび Syslog データ ソースは、Azure Monitor ログにのみ送信できます。 パフォーマンス カウンターは Azure Monitor メトリックと Azure Monitor ログの両方に送信できます。

[![到着地](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

**[データソースの追加]** 、 **[確認と作成]** の順にクリックして、データ収集ルールの詳細と、一連の VM との関連付けを確認します。 **[作成]** をクリックしてそれを作成します。

> [!NOTE]
> データ収集ルールと関連付けが作成されたら、データが送信先に送信されるまでに最大で 5 分かかることがあります。

## <a name="limit-data-collection-with-custom-xpath-queries"></a>カスタム XPath クエリを使用してデータ収集を制限する
Log Analytics ワークスペースに収集されたデータに対して課金されるため、必要なデータのみを収集する必要があります。 Azure portal で基本構成を使用している場合、収集するイベントをフィルター処理する機能は限定されています。 アプリケーションおよびシステムのログの場合、これは特定の重要度が設定されたすべてのログです。 セキュリティ ログの場合、これは、すべての監査の成功またはすべての監査の失敗ログです。

追加のフィルターを指定するには、カスタム構成を使用して、不要なイベントを除外する XPath を指定する必要があります。 XPath エントリは、`LogName!XPathQuery` の形式で記述されます。 たとえば、イベント ID が 1035 のイベントのみをアプリケーション イベント ログから返す必要があるとします。 これらのイベントを対象とする XPathQuery は `*[System[EventID=1035]]` になります。 アプリケーション イベント ログからイベントを取得する必要があるため、XPath は `Application!*[System[EventID=1035]]` になります

Windows イベント ログでサポートされている XPath の制限事項の一覧については、「[XPath 1.0 の制限事項](/windows/win32/wes/consuming-events#xpath-10-limitations)」を参照してください。

> [!TIP]
> XPathQuery の有効性をテストするには、`FilterXPath` パラメーターを指定した PowerShell コマンドレット`Get-WinEvent` を使用します。 次のスクリプトは、一例を示しています。
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - **上のコマンドレットで、"-LogName" パラメーターの値は XPath クエリの "!" までの最初の部分ですが、$XPath パラメーターに渡されるのは XPath クエリの残りの部分だけです。**
> - イベントが返されたら、クエリは有効です。
> - *[No events were found that match the specified selection criteria.]\(指定した選択条件に一致するイベントは見つかりませんでした。\)* というメッセージが表示された場合は、クエリはおそらく有効ですが、一致するイベントがローカル コンピューターにありません。
> - *[The specified query is invalid]\(指定したクエリは無効です\)* というメッセージが表示された場合は、クエリ構文が無効です。 

次の表は、カスタム XPath を使用してイベントをフィルター処理する例を示しています。

| 説明 |  XPath |
|:---|:---|
| イベント ID = 4648 のシステム イベントのみを収集する |  `System!*[System[EventID=4648]]`
| イベント ID = 4648 で、プロセス名が consent.exe であるシステム イベントのみを収集する | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| イベント ID = 6 (ドライバーの読み込み) を除くすべての重大、エラー、警告、および情報のイベントをシステム イベント ログから収集する |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| イベント ID 4624 (成功したログオン) を除くすべての成功および失敗のセキュリティ イベントを収集する |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>REST API を使用したルールと関連付けを作成する

以下の手順に従って、REST API を使用したデータ収集ルールと関連付けを作成します。

> [!NOTE]
> Log Analytics にデータを送信する場合は、Log Analytics ワークスペースが存在するのと **同じリージョン** にデータ収集ルールを作成する必要があります。 ルールは、サポートされている他のリージョンのマシンに関連付けることができます。

1. [サンプル DCR](data-collection-rule-overview.md#sample-data-collection-rule) に示されている JSON 形式を使用して、DCR ファイルを手動で作成します。

2. [REST API](/rest/api/monitor/datacollectionrules/create#examples) を使用してルールを作成します。

3. [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples) を使用して、データ収集ルールと各仮想マシンの関連付けを作成します。


## <a name="create-rule-and-association-using-resource-manager-template"></a>Resource Manager テンプレートを使用してルールと関連付けを作成する

> [!NOTE]
> Log Analytics にデータを送信する場合は、Log Analytics ワークスペースが存在するのと **同じリージョン** にデータ収集ルールを作成する必要があります。 ルールは、サポートされている他のリージョンのマシンに関連付けることができます。

Resource Manager テンプレートを使用して Azure 仮想マシンまたは Azure Arc 対応サーバーのルールと関連付けを作成することができます。 サンプル テンプレートについては、「[Azure Monitor のデータ収集ルールの Resource Manager テンプレート サンプル](./resource-manager-data-collection-rules.md)」を参照してください。


## <a name="manage-rules-and-association-using-powershell"></a>PowerShell を使用してルールと関連付けを管理する

> [!NOTE]
> Log Analytics にデータを送信する場合は、Log Analytics ワークスペースが存在するのと **同じリージョン** にデータ収集ルールを作成する必要があります。 ルールは、サポートされている他のリージョンのマシンに関連付けることができます。

**データ収集ルール**

| アクション | コマンド |
|:---|:---|
| ルールを取得する | [Get-AzDataCollectionRule](/powershell/module/az.monitor/get-azdatacollectionrule?view=azps-5.4.0&preserve-view=true) |
| 規則を作成する | [New-AzDataCollectionRule](/powershell/module/az.monitor/new-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| ルールを更新する | [Set-AzDataCollectionRule](/powershell/module/az.monitor/set-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 規則を削除する | [Remove-AzDataCollectionRule](/powershell/module/az.monitor/remove-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| ルールの "タグ" を更新する | [Update-AzDataCollectionRule](/powershell/module/az.monitor/update-azdatacollectionrule?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |

**データ収集ルールの関連付け**

| アクション | コマンド |
|:---|:---|
| 関連付けを取得する | [Get-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/get-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 関連付けを作成する | [New-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/new-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |
| 関連付けを削除する | [Remove-AzDataCollectionRuleAssociation](/powershell/module/az.monitor/remove-azdatacollectionruleassociation?view=azps-6.0.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) |



## <a name="manage-rules-and-association-using-azure-cli"></a>Azure CLI を使用してルールと関連付けを管理する

> [!NOTE]
> Log Analytics にデータを送信する場合は、Log Analytics ワークスペースが存在するのと **同じリージョン** にデータ収集ルールを作成する必要があります。 ルールは、サポートされている他のリージョンのマシンに関連付けることができます。

これは、Azure CLI **monitor-control-service** 拡張機能の一部として有効になります。 [すべてのコマンドを表示する](/cli/azure/monitor/data-collection/rule?view=azure-cli-latest&preserve-view=true)


## <a name="next-steps"></a>次の手順

- [Azure Monitor エージェント](azure-monitor-agent-overview.md)の詳細を確認します。
- [データ収集ルール](data-collection-rule-overview.md)の詳細を確認します。
