---
title: SQL Data Warehouse の列レベルのセキュリティとは
description: 列レベルのセキュリティでは、ユーザーの実行コンテキストまたはグループ メンバーシップに基づいてデータベース テーブルの列へのアクセスを制御し、アプリケーションのセキュリティの設計とコーディングを簡略化し、列に制限を実装することができます。
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 344701989a753e17d8a026f6bb771a6030bdb71f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513050"
---
# <a name="column-level-security"></a>列レベルのセキュリティ

列レベルのセキュリティでは、ユーザーの実行コンテキストまたはグループ メンバーシップに基づいて、テーブルの列へのアクセスを制御できます。


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
このビデオが投稿された後に、[行レベルのセキュリティ](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017)が SQL Data Warehouse で使用可能になりました。 

列レベルのセキュリティにより、アプリケーションのセキュリティの設計とコーディングが簡略化されるため、列のアクセスを制限して機密データを保護することができます。 たとえば、特定のユーザーが、所属する部門に関連するテーブルの一定の列にのみアクセスできるようにします。 アクセスの制限のロジックは、別のアプリケーション層のデータから離れてではなく、データベース層にあります。 任意の階層からデータ アクセスが試行されるたびに、データベースによってアクセス制限が適用されます。 この制限により、セキュリティ全体の外部からのアクセスが減り、そのシステムの信頼性と堅牢性が向上します。 さらに、列レベルのセキュリティは、列を除外してユーザーにアクセス制限を課すためのビューの導入も不要にします。

列レベルのセキュリティは [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL ステートメントで実装できます。 このメカニズムでは、SQL と Azure Active Directory (AAD) 認証がどちらもサポートされます。

![CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>構文

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>例
次の例では、`TestUser` が `Membership` テーブルの `SSN` 列にアクセスするのを制限する方法を示します。

社会保障番号を格納するために使用される SSN 列が含まれた `Membership` テーブルを作成します。

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

機密データを含む SSN 列以外のすべての列へのアクセスを `TestUser` に許可します。

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

`TestUser` として実行されたクエリは、SSN 列が含まれている場合に失敗します。

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>例

現在、列レベルのセキュリティを使用する方法の例を次に示します。

- 金融サービス会社は、アカウント マネージャーだけが、顧客の社会保障番号 (SSN) や電話番号などの個人を特定できる情報 (PII) にアクセスできるようにします。
- 医療機関は、医師と看護師だけが機密の医療記録にアクセスできるようにする一方で、請求担当部門のメンバーがそのようなデータを表示できないようにします。
