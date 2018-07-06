---
title: Azure Log Analytics のログ検索におけるコンピューター グループ | Microsoft Docs
description: Log Analytics では、コンピューター グループを使用して、ログ検索の範囲を特定のコンピューターの集合に限定することができます。  この記事では、コンピューター グループを作成する各種の方法とそれらをログ検索で使用する方法について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 23ac75b4afb07c3f8d5f0d90755a5cf2087087e1
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130677"
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Log Analytics のログ検索におけるコンピューター グループ

Log Analytics では、コンピューター グループを使用して、[ログ検索](log-analytics-log-search-new.md)の範囲を特定のコンピューターの集合に限定することができます。  それぞれのグループには、自分で定義したクエリを使用するか、さまざまなソースからグループをインポートすることでコンピューターを追加します。  そのグループをログの検索に含めると、対応するグループ内のコンピューターと一致するレコードに検索結果が限定されます。

## <a name="creating-a-computer-group"></a>コンピューター グループの作成
Log Analytics のコンピューター グループは、以下の表に示した方法のいずれかで作成できます。  それぞれの方法について、以降のセクションで詳しく説明します。 

| 方法 | 説明 |
|:--- |:--- |
| ログ検索 |コンピューターの一覧を返すログ検索を作成します。 |
| Log Search API |ログ検索の結果に基づいてプログラムからコンピューター グループを作成するには、Log Search API を使用します。 |
| Active Directory |Active Directory ドメインに属しているエージェント コンピューターのグループ メンバーシップを自動的にスキャンし、セキュリティ グループごとのグループを Log Analytics に作成します。 |
| 構成マネージャー | System Center Configuration Manager からコレクションをインポートし、各々の Log Analytics でグループを作成します。 |
| Windows Server Update Services |WSUS のサーバーまたはクライアントを自動的にスキャンして WSUS の対象グループを取得し、それぞれのグループを Log Analytics に作成します。 |

### <a name="log-search"></a>ログ検索
ログ検索で作成されたコンピューター グループには、定義したクエリによって返されたすべてのコンピューターが含まれます。  このクエリは、コンピューター グループを使用するたびに実行されます。そうすることで、グループの作成以降に行われた変更が反映されます。  

コンピューター グループにはどのクエリでも使用できますが、クエリは `distinct Computer` を使用して明確な一連のコンピューターを返す必要があります。  コンピューター グループに使用できる一般的な検索の例を次に示します。

    Heartbeat | where Computer contains "srv" | distinct Computer

次の表では、コンピューター グループを定義するプロパティについて説明しています。

| プロパティ | 説明 |
|:---|:---|
| 表示名   | ポータルに表示する検索の名前。 |
| カテゴリ       | ポータル内で検索を整理するためのカテゴリ。 |
| クエリ          | コンピューター グループのクエリ。 |
| 関数のエイリアス | クエリ内でコンピューター グループを識別するのに使用される一意のエイリアス。 |

Azure Portal でログ検索からコンピューター グループを作成するには、次の手順に従います。

2. **[ログ検索]** を開いて、画面の上部の **[保存された検索]** をクリックします。
3. **[追加]** をクリックして、コンピューター グループの各プロパティの値を指定します。
4. **[このクエリを次のコンピューター グループとして保存します]** を選択して **[OK]** をクリックします。



### <a name="active-directory"></a>Active Directory
Active Directory グループのメンバーシップをインポートするように Log Analytics を構成した場合、OMS エージェントがインストールされている、ドメインに参加しているすべてのコンピューターのグループ メンバーシップが分析されます。  Log Analytics には、Active Directory 内のセキュリティ グループごとにコンピューター グループが作成され、それぞれのコンピューターは、属しているセキュリティ グループに対応したコンピューター グループに追加されます。  このメンバーシップは絶えず 4 時間おきに更新されます。  

Azure Portal の Log Analytics の **[詳細設定]** から Active Directory のセキュリティ グループをインポートするように Log Analytics を構成します。  **[コンピューター グループ]**、**[Active Directory]** の順に選択し、**[コンピューターから Active Directory のグループ メンバーシップをインポートします]** を選択します。  さらに手動で構成する必要はありません。

![Active Directory からのコンピューター グループ](media/log-analytics-computer-groups/configure-activedirectory.png)

グループがインポートされると、検出されたグループ メンバーシップを持つコンピューターの数とインポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

### <a name="windows-server-update-service"></a>Windows Server Update Service
WSUS グループのメンバーシップをインポートするように Log Analytics を構成した場合、OMS エージェントがインストールされているすべてのコンピューターについて、WSUS の対象グループのメンバーシップが分析されます。  クライアント側のターゲット指定方式を使用している場合、Log Analytics に接続されていて、かつ WSUS の対象グループに属しているすべてのコンピューターのグループ メンバーシップが Log Analytics にインポートされます。 サーバー側のターゲット指定方式を使用している場合、グループ メンバーシップ情報を Log Analytics にインポートするためには、WSUS サーバーに OMS エージェントがインストールされている必要があります。  このメンバーシップは絶えず 4 時間おきに更新されます。 

Azure Portal の Log Analytics の **[詳細設定]** から WSUS グループをインポートするように Log Analytics を構成します。  **[コンピューター グループ]**、**[WSUS]** の順に選択し、**[WSUS のグループ メンバーシップをインポートします]** を選択します。  さらに手動で構成する必要はありません。

![WSUS のコンピューター グループ](media/log-analytics-computer-groups/configure-wsus.png)

グループがインポートされると、検出されたグループ メンバーシップを持つコンピューターの数とインポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Configuration Manager のコレクション メンバーシップをインポートするように Log Analytics を構成すると、各コレクションのコンピューター グループが作成されます。  コンピューター グループを最新に保つために、コレクション メンバーシップ情報は 3 時間ごとに取得されます。 

Configuration Manager のコレクションをインポートするには、[Log Analytics に Configuration Manager を接続しておく](log-analytics-sccm.md)必要があります。  これで、Azure Portal の Log Analytics の **[詳細設定]** からインポートを構成できます。  **[コンピューター グループ]**、**[SCCM]** の順に選択して、**[Configuration Manager コレクション メンバーシップをインポートする]** を選択します。  さらに手動で構成する必要はありません。

![SCCM のコンピューター グループ](media/log-analytics-computer-groups/configure-sccm.png)

コレクションがインポートされると、検出されたコンピューターおよびグループ メンバーシップの数と、インポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

## <a name="managing-computer-groups"></a>コンピューター グループの管理
ログ検索または Log Search API から作成されたコンピューター グループは、Azure Portal の Log Analytics の **[詳細設定]** から表示できます。  **[コンピューター グループ]** を選択してから、**[保存済みグループ]** を選択します。  

**[削除]** 列の **[x]** をクリックすると、コンピューター グループが削除されます。  グループの **[メンバーの表示]** アイコンをクリックすると、グループのログ検索が実行されて、そのメンバーが返されます。  コンピューター グループは変更できませんが、コンピューター グループを削除し、変更された設定で再度作成する必要があります。

![保存されているコンピューター グループ](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>ログ検索におけるコンピューター グループの使用
ログ検索から作成されたコンピューター グループをクエリで使用するには、エイリアスを関数として使用します。通常、次の構文となります。

  `Table | where Computer in (ComputerGroup)`

たとえば、以下を使用して、mycomputergroup という名前のコンピューター グループ内のコンピューターのみを対象とした UpdateSummary レコードを返すことができます。
 
  `UpdateSummary | where Computer in (mycomputergroup)`


インポートされたコンピュータ グループとそれらに含まれるコンピューターは、**ComputerGroup** テーブルに格納されます。  たとえば、次のクエリは、Active Directory の Domain Computers グループにコンピューターの一覧を返します。 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

次のクエリは、Domain Computers 内のコンピューターの UpdateSummary レコードのみを返します。

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>コンピューター グループのレコード
Active Directory または WSUS から作成されたコンピューター グループでは、そのメンバーシップごとのレコードが Log Analytics ワークスペースに作成されます。  これらは **ComputerGroup** タイプのレコードとして、次の表に示すプロパティを持ちます。  ログ検索に基づくコンピューター グループにはレコードが作成されません。

| プロパティ | 説明 |
|:--- |:--- |
| type |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computer |メンバー コンピューターの名前。 |
| グループ |グループの名前。 |
| GroupFullName |ソースとソース名を含んだグループの完全パス。 |
| GroupSource |グループの収集元となったソース。 <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |グループの収集元となったソースの名前。  Active Directory の場合はドメイン名になります。 |
| ManagementGroupName |SCOM エージェントの管理グループの名前。  その他のエージェントの場合、これは AOI-\<workspace ID\> です。 |
| TimeGenerated |コンピューター グループが作成または更新された日時。 |

## <a name="next-steps"></a>次の手順
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。  

