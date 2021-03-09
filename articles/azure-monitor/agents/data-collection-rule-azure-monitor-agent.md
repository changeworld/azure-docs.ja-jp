---
title: Azure Monitor エージェント用のデータ収集の構成 (プレビュー)
description: Azure Monitor エージェントを使用して、仮想マシンからデータを収集するデータ収集ルールを作成する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 93e244706d6d478155ac001d20fa3ce74fa6a887
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723641"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Azure Monitor エージェント用のデータ収集の構成 (プレビュー)

データ収集ルール (DCR) では、Azure Monitor に入ってくるデータを定義し、そのデータの送信先を指定します。 この記事では、Azure Monitor エージェントを使用して、仮想マシンからデータを収集するデータ収集ルールを作成する方法について説明します。

データ収集ルールの詳細については、「[Azure Monitor のデータ収集ルール (プレビュー)](data-collection-rule-overview.md)」を参照してください。

> [!NOTE]
> この記事では、現在プレビューの段階にある Azure Monitor エージェントで仮想マシンのデータを構成する方法について説明します。 一般公開されているエージェントの説明と、それらを使用してデータを収集する方法については、「[Azure Monitor エージェントの概要](agents-overview.md)」を参照してください。

## <a name="data-collection-rule-associations"></a>データ収集ルールの関連付け

DCR を仮想マシンに適用するには、仮想マシンの関連付けを作成します。 仮想マシンには複数の DCR への関連付けを指定できます。また、DCR に複数の仮想マシンを関連付けることもできます。 これにより、それそれが特定の要件に一致する一連の DCR を定義し、それらが該当する仮想マシンにのみそれらを適用することができます。 

たとえば、基幹業務アプリケーションを実行している一連の仮想マシンと SQL Server を実行しているほかの仮想マシンがある環境について考えてみます。 すべての仮想マシンに適用する 1 つの既定のデータ収集ルールと、基幹業務アプリケーション用と SQL Server 用に特別にデータを収集する個別のデータ収集ルールを指定することができます。 データ収集ルールへの仮想マシンの関連付けは、次の図のようになります。

![基幹業務アプリケーションと SQL Server をホストしている仮想マシンを示す図。基幹業務アプリケーションは central-it-default および lob-app、SQL Server は central-it-default および sql という名前のデータ収集ルールに関連付けられています。](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Azure portal でルールと関連付けを作成する

Azure portal を使用してデータ収集ルールを作成し、サブスクリプション内の仮想マシンをそのルールに関連付けることができます。 Azure Monitor エージェントは自動的にインストールされ、まだそれがインストールされていないすべての仮想マシンに対して、マネージド ID が作成されます。

> [!IMPORTANT]
> 現在、既知の問題があります。既にユーザーが割り当てたマネージド ID が設定されている仮想マシンで、データ収集ルールによってマネージド ID が作成された場合、ユーザーが割り当てた ID が無効になります。

Azure portal の **[Azure Monitor]** メニューで、 **[設定]** セクションから **[データ収集ルール]** を選択します。 **[追加]** をクリックして、新しいデータ収集ルールと割り当てを追加します。

[![データ収集ルール](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

**[追加]** をクリックして、新しいルールと一連の関連付けを作成します。 **[規則名]** を指定し、 **[サブスクリプション]** および **[リソース グループ]** を指定します。 これにより、DCR が作成される場所を指定します。 仮想マシンとそれらの関連付けは、テナント内の任意のサブスクリプションまたはリソース グループに配置できます。

[![データ収集ルールの基本](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

**[仮想マシン]** タブで、データ収集ルールを適用する必要がある仮想マシンを追加します。 環境内の Azure 仮想マシンと Azure Arc 対応サーバーの両方が一覧表示されます。 Azure Monitor エージェントが、まだインストールされていない仮想マシンにインストールされます。

[![データ収集ルール仮想マシン](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

**[収集と配信]** タブで、 **[データ ソースの追加]** をクリックして、データのソースおよびターゲット セットを追加します。 **[データソースの種類]** を選択すると、選択する対応する詳細が表示されます。 パフォーマンス カウンターでは、定義済みのオブジェクトのセットとそのサンプリング レートから選択できます。 イベントについては、一連のログまたは機能と重大度レベルから選択できます。 

[![データ ソースの基本](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


[現在サポートされているデータ ソース](azure-monitor-agent-overview.md#data-sources-and-destinations)のその他のログとパフォーマンス カウンターを指定する、または XPath クエリを使用してイベントをフィルター処理するには、 **[カスタム]** を選択します。 それにより、収集する特定の値の [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) を指定できます。 例については、[サンプル DCR](data-collection-rule-overview.md#sample-data-collection-rule) を参照してください。

[![データ ソース カスタム](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

**[送信先]** タブで、データ ソースの 1 つ以上の送信先を追加します。 Windows イベントおよび Syslog データ ソースは、Azure Monitor ログにのみ送信できます。 パフォーマンス カウンターは Azure Monitor メトリックと Azure Monitor ログの両方に送信できます。

[![到着地](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

**[データソースの追加]** 、 **[確認と作成]** の順にクリックして、データ収集ルールの詳細と、一連の VM との関連付けを確認します。 **[作成]** をクリックしてそれを作成します。

> [!NOTE]
> データ収集ルールと関連付けが作成されたら、データが送信先に送信されるまでに最大で 5 分かかることがあります。


## <a name="create-rule-and-association-using-rest-api"></a>REST API を使用したルールと関連付けを作成する

以下の手順に従って、REST API を使用したデータ収集ルールと関連付けを作成します。

1. [サンプル DCR](data-collection-rule-overview.md#sample-data-collection-rule) に示されている JSON 形式を使用して、DCR ファイルを手動で作成します。

2. [REST API](/rest/api/monitor/datacollectionrules/create#examples) を使用してルールを作成します。

3. [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples) を使用して、データ収集ルールと各仮想マシンの関連付けを作成します。


## <a name="create-association-using-resource-manager-template"></a>Resource Manager テンプレートを使用して関連付けを作成する

Resource Manager テンプレートを使用してデータ収集ルールを作成することはできませんが、Resource Manager テンプレートを使用して Azure 仮想マシンまたは Azure Arc 対応サーバー間の関連付けを作成することはできます。 サンプル テンプレートについては、「[Azure Monitor のデータ収集ルールの Resource Manager テンプレート サンプル](./resource-manager-data-collection-rules.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Monitor エージェント](azure-monitor-agent-overview.md)の詳細を確認します。
- [データ収集ルール](data-collection-rule-overview.md)の詳細を確認します。