---
title: Azure Firewall ルール処理ロジック
description: Azure Firewall には、NAT ルール、ネットワーク ルール、およびアプリケーション ルールがあります。 これらのルールは、ルールの種類に応じて処理されます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/09/2021
ms.author: victorh
ms.openlocfilehash: ec914df62c98ea7948c198949b34e37aa3c485d3
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137342"
---
# <a name="configure-azure-firewall-rules"></a>Azure Firewall 規則を構成する
従来の規則か Firewall Policy を使用して、Azure Firewall で NAT 規則、ネットワーク規則、アプリケーション規則を設定できます。 Azure Firewall では既定ですべてのトラフィックを拒否します。トラフィックを許可するには、手動で規則を設定する必要があります。

## <a name="rule-processing-using-classic-rules"></a>従来の規則を使用した規則の処理

ルール コレクションはルールの種類と優先順位に基づいて処理されます。優先順位は 100 から 65,000 までであり、数字の低い順から高い順に処理されます。 ルール コレクションの名前に使用できるのは、文字、数字、アンダースコア、ピリオド、ハイフンのみです。 先頭は文字または数字、末尾は文字、数字、またはアンダースコアでなければなりません。 名前の最大長は 80 文字です。

ルール コレクションの優先順位数の間隔はまず、100 の増分にすることをお勧めします (100、200、300 など)。そうすることで、必要に応じてルール コレクションを追加する余地が与えられます。

## <a name="rule-processing-using-firewall-policy"></a>Firewall Policy を使用した規則の処理

Firewall Policy では、Rule Collection と Rule Collection Group に規則を整理します。 Rule Collection Group には 0 個以上の Rule Collection が含まれます。 Rule Collection の種類には NAT、Network、Application があります。 単一の Rule Group に複数の Rule Collection を指定することもできます。 Rule Collection には 0 個以上の Rule を定義できます。 1 つの Rule Collection に属する Rule は、同じ種類 (NAT、Network または Application) である必要があります。    

Rule は Rule Collection Group の優先度と Rule Collection の優先度に基づいて処理します。 優先度は、100 (最高) から 65,000 (最低) のいずれかの数字で表されます。 優先度が最も高い Rule Collection Group を最初に処理します。 Rule Collection Group の中でも、優先度が最も高い (数字が最も小さい) Rule Collection を最初に処理します。  

Firewall Policy を親ポリシーから継承した場合、子ポリシーの優先度に関わりなく、親ポリシーに属する Rule Collection Group を常に優先します。  

> [!NOTE]
> Application 規則は常に Network 規則より後で処理します。Network 規則は、Rule Collection Group および Rule Collection の優先度とポリシーの継承に関わりなく、常に DNAT 規則より後で処理します。

ポリシーの例をここに挙げます。


|名前  |型  |優先順位  |ルール  |継承元
|---------|---------|---------|---------|-------|
|BaseRCG1      |Rule Collection Group           |200         |8         |親ポリシー|
|DNATRc1     |DNAT Rule Collection         |  600       |   7      |親ポリシー|
|NetworkRc1     |ネットワーク ルール コレクション  | 800        |    1     |親ポリシー|
|BaseRCG2  |Rule Collection Group         |300         | 3        |親ポリシー|
|AppRCG2     |アプリケーション ルール コレクション | 1200        |2         |親ポリシー
|NetworkRC2     |ネットワーク ルール コレクション         |1300         |    1     |親ポリシー|
|ChildRCG1  | Rule Collection Group        | 300        |5         |-|
|ChAppRC1     |アプリケーション ルール コレクション         |  700       | 3        |-|
|ChNetRC1       |   ネットワーク ルール コレクション      |    900     |    2     |-|
|ChildRCG2      |Rule Collection Group         | 650        |    9     |-|
|ChNetRC2      |ネットワーク ルール コレクション         |    1100     |  2       |-|
|ChAppRC2      |     アプリケーション ルール コレクション    |2000         |7         |-|
|ChDNATRC3     | DNAT Rule Collection        | 3000        |  2       |-|

規則の処理は次の順序で行います: DNATRC1、DNATRC3、ChDNATRC3、NetworkRC1、NetworkRC2、ChNetRC1、ChNetRC2、AppRC2、ChAppRC1、ChAppRC2。

ファイアウォール ポリシー規則セットの詳細については、「[Azure Firewall ポリシー規則セット](policy-rule-sets.md)」を参照してください。

### <a name="threat-intelligence"></a>[脅威インテリジェンス]

脅威インテリジェンスを利用したフィルターを有効にすると、それらの規則は優先度が最高になり、常に最初に (Network および Application 規則より先に) 処理されます。 構成したルールが処理される前に、脅威インテリジェンス フィルターによってトラフィックが拒否されることがあります。 詳細については、「[Azure Firewall の脅威インテリジェンスベースのフィルター処理](threat-intel.md)」を参照してください。

### <a name="idps"></a>IDPS

IDPS を *[Alert]\(警告\)* に設定すると、IDPS エンジンが規則処理ロジックと並列して動作し、受信と送信両方のトラフィックで、シグネチャへの一致に対して警告を出します。IDPS シグネチャへの一致が見付かった場合、ファイアウォールのログに警告を記録します。 3IDPS エンジンは規則処理エンジンと並列して動作するため、Application/Network 規則で拒否/許可されたトラフィックについて、もう1 つ別の項目がログに記録される場合があります。 

IDPS を *[Alert and Deny]\(警告と拒否\)*  モードに設定すると、IDPS エンジンがインラインになり、規則処理エンジンの後でアクティブになります。 つまり、両方のエンジンによって警告を出し、一致するトラフィックを遮断します。  

IDPS によってセッションを切断する場合、通知なしにトラフィックを遮断します。 つまり、TCP レベルで RST は送信されません。トラフィックに対する IDPS の検査は常に、Network/Application 規則 (許可/拒否の規則) への一致が見付かって、それをログに記録した後で実施します。そのため、もう1 つ別の *[Drop]\(切断\)* メッセージをログに記録し、シグネチャへの一致が見付かったため IDPS でセッションを切断したことを、そこに記載します。 

TLS インスペクションを有効にすると、暗号化していないトラフィックと暗号化しているトラフィックを両方検査します。  

## <a name="outbound-connectivity"></a>送信接続

### <a name="network-rules-and-applications-rules"></a>ネットワーク ルールとアプリケーション ルール

ネットワーク ルールとアプリケーション ルールを構成すると、優先順位に従いネットワーク ルールがアプリケーション ルールよりも先に適用されます。 その後、これらのルールが終了します。 そのため、Network 規則への一致が見付かった場合は、他の規則の処理を行いません。 IDPS を有効にすると、通過するトラフィックすべてに対してこれを実施し、シグネチャへの一致が見付かった場合は、警告を出すか、不審なトラフィックを遮断するか、またはその両方を実行します。  

Network 規則への一致がなく、プロトコルに HTTP、HTTPS または MSSQL を使用している場合は、その後で、 Application 規則により、優先度順で、パケットを評価します。  

HTTP については、Azure Firewall で Application 規則への一致を調べるとき、ホスト ヘッダーの情報を使用します。 HTTPS については、Azure Firewall で Application 規則への一致を調べるとき、SNI のみを使用します。  

HTTP と TLS インスペクションを有効にした HTTPS では、ファイアウォールでパケットの宛先 IP アドレスを無視し、DNS でホスト ヘッダーから解決した IP アドレスを使用します。 ファイアウォールでは、ホスト ヘッダーからポート番号を取得できることを想定していますが、これができない場合は、標準である 80 番ポートを使用するものとみなします。 実際の TCP ポートとホスト ヘッダーのポートが一致しない場合は、トラフィックを遮断します。ファイアウォールで有効にしてある場合、Azure DNS またはカスタム DNS で DNS 解決を行います。  

> [!NOTE]
> Azure Firewall によって、HTTP と HTTPS (TLS インスペクションあり) のメッセージには常に、元の送信元 IP アドレスに対応する XFF (X-Forward-For) ヘッダーが付けられます。  

TLS インスペクションを含む Application 規則では、ファイアウォールの規則エンジンで、SNI、ホスト ヘッダー、URL の規則への一致を調べます。 

それでも Application 規則で一致が見つからない場合は、パケットをインフラストラクチャ Rule Collection と照合します。 それでも一致するものがない場合、パケットは既定で拒否されます。 

> [!NOTE]
> Network 規則は *TCP*、 *UDP*、 *ICMP*、 *Any* IP protocol (あらゆる IP プロトコル) に対して設定できます。 “Any IP protocol” (あらゆる IP プロトコル) には、Internet Assigned Numbers Authority (IANA) プロトコル番号のドキュメントに定められたすべての IP プロトコルが含まれます。 宛先ポートが明示的に構成されている場合、ルールは TCP+UDP ルールに変換されます。 2020 年 11 月 9 日より前は、 *Any*  は TCP、UDP および ICMP を指していました。 そのため、この日より前に設定した規則では、**Protocol = Any** かつ **destination ports = '*'** となっていることがあり得ます。 Any の現在の語義に従ってあらゆる IP プロトコルを許可する意図がない場合は、その規則を修正して必要なプロトコル (TCP、UDP または ICMP) を明示的に指定します。 

## <a name="inbound-connectivity"></a>受信接続

### <a name="dnat-rules-and-network-rules"></a>DNAT 規則と Network 規則

インバウンド インターネット接続を有効にするには、宛先ネットワーク アドレス変換 (DNAT) を「[チュートリアル:Azure portal で Azure Firewall DNAT を使用して受信トラフィックをフィルター処理する](tutorial-firewall-dnat.md)」の説明に従って構成します。 NAT 規則は、ネットワーク ルールよりも優先的に適用されます。 一致が見つかると、変換されたトラフィックを許可する暗黙的な対応するネットワーク ルールが追加されます。 セキュリティ上の理由から、推奨される方法は、ネットワークへの DNAT アクセスを許可するために特定のインターネット ソースを追加し、ワイルドカードは使用しないようにすることです。

アプリケーション ルールは、受信接続には適用されません。 したがって、インバウンド HTTP/S トラフィックをフィルター処理する場合は、Web アプリケーション ファイアウォール (WAF) を使用する必要があります。 詳細については、「[Azure Web アプリケーション ファイアウォールとは](../web-application-firewall/overview.md)」を参照してください

## <a name="examples"></a>例

次の例は、これらのルールを組み合わせた結果を示しています。

### <a name="example-1"></a>例 1

ネットワーク ルールが一致しているため、google.com への接続は許可されます。

**ネットワーク ルール**

- アクション:Allow


|name  |Protocol  |送信元の種類  |source  |変換先の型  |宛先アドレス  |宛先ポート|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|IP アドレス|*|IP アドレス|*|80,443

**アプリケーション ルール**

- アクション:拒否

|name  |送信元の種類  |source  |プロトコル:ポート|ターゲットの FQDN|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |IP アドレス|*|http: 80、https: 443|google.com

**結果**

パケットが *Allow-web* ネットワーク ルールと一致するため、google.com への接続は許可されます。 この時点で、ルールの処理が停止します。

### <a name="example-2"></a>例 2

優先順位の高い "*拒否*" ネットワーク ルール コレクションによってブロックされるため、SSH トラフィックは拒否されます。

**ネットワーク ルール コレクション 1**

- 名前:許可コレクション
- 優先順位:200
- アクション:Allow

|name  |Protocol  |送信元の種類  |source  |変換先の型  |宛先アドレス  |宛先ポート|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|IP アドレス|*|IP アドレス|*|22

**ネットワーク ルール コレクション 2**

- 名前:拒否コレクション
- 優先順位:100
- アクション:拒否

|name  |Protocol  |送信元の種類  |source  |変換先の型  |宛先アドレス  |宛先ポート|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|IP アドレス|*|IP アドレス|*|22

**結果**

優先順位の高いネットワーク ルール コレクションによってブロックされるため、SSH 接続は拒否されます。 この時点で、ルールの処理が停止します。

## <a name="rule-changes"></a>ルールの変更

以前に許可されたトラフィックを拒否するようにルールを変更すると、関連する既存のセッションがすべて削除されます。

## <a name="3-way-handshake-behavior"></a>3 方向ハンドシェイクの動作

ステートフル サービスとして、Azure Firewall では、送信元から送信先への許可されたトラフィックに対する TCP 3 方向ハンドシェイクを行います。たとえば、VNet-A から VNet-B などです。

VNet-A から VNet-B への許可規則を作成しても、VNet-B から VNet-A への新たに開始される接続が許可されるわけではありません。

そのため、VNet-B から VNet-A への明示的な拒否規則を作成する必要はありません。 この拒否規則を作成すると、VNet-A から VNet-B への最初の許可規則の 3 方向ハンドシェイクを中断することになります。 

## <a name="next-steps"></a>次のステップ

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。
