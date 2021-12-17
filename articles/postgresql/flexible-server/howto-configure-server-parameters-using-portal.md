---
title: サーバー パラメーターを構成する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーの Postgres パラメーターを構成する方法について説明します。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 1ca1501f790f914412fa61fce5ceed5ba45130b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736389"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーでサーバー パラメーターを構成する 
Azure Portal を使用して、Azure Database for PostgreSQL サーバーの構成パラメーターを一覧表示、表示、更新できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL - フレキシブル サーバー](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>パラメーターの編集と表示
1. [Azure Portal](https://portal.azure.com)を開きます。

2. Azure Database for PostgreSQL サーバーを選択します。

3. **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。 ページにパラメーター、その値、説明の一覧が表示されます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="パラメーターの [概要] ページ":::

4. **ドロップ ダウン** ボタンをクリックして、client_min_messages のような列挙型のパラメーターに指定できる値を表示します。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="列挙ドロップ ダウン":::

5. **[i]** (情報) ボタンを選択するかマウス ポインターを置いて、cpu_index_tuple_cost のような数値パラメーターに指定できる値の範囲を表示します。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="情報ボタン":::

6. 必要に応じて、**検索ボックス** を使用して、特定のパラメーターを絞り込みます。 検索は、パラメーターの名前と説明に対して実行されます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="検索結果":::

7. 調整するパラメーターの値を変更します。 セッションで行ったすべての変更が紫色で強調表示されます。 値を変更したら、 **[保存]** を選択できます。 または変更を **[破棄]** することができます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="変更の保存または破棄":::

8. パラメーターの新しい値を保存した場合は、 **[すべて既定値にリセット]** を選択していつでもすべてを既定値に戻すことができます。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="すべて既定値にリセット":::

## <a name="working-with-time-zone-parameters"></a>タイム ゾーン パラメーターを使用する
PostgreSQL で日付と時刻のデータを使用する場合は、その場所の適切なタイム ゾーンが設定されていることを確認する必要があります。 タイム ゾーン対応のすべての日付と時刻は、Postgres 内に UTC で格納されます。 それらは、クライアントに表示される前に、**TimeZone** サーバー パラメーターで指定されているゾーンのローカル時刻に変換されます。  このパラメーターは、上で説明したように、 **[サーバー パラメーター]** ページで編集できます。 PostgreSQL では、3 つの異なる形式でタイム ゾーンを指定できます。
1. 完全なタイム ゾーン名 (例: America/New_York)。 認識されるタイム ゾーン名は、[**pg_timezone_names**](https://www.postgresql.org/docs/9.2/view-pg-timezone-names.html) ビューに一覧表示されます。  
   psql でこのビューのクエリを実行し、タイム ゾーン名の一覧を取得する例:
   <pre>select name FROM pg_timezone_names LIMIT 20;</pre>

   次のような結果が表示されます。

   <pre>
            name
        -----------------------
        GMT0
        Iceland
        Factory
        NZ-CHAT
        America/Panama
        America/Fort_Nelson
        America/Pangnirtung
        America/Belem
        America/Coral_Harbour
        America/Guayaquil
        America/Marigot
        America/Barbados
        America/Porto_Velho
        America/Bogota
        America/Menominee
        America/Martinique
        America/Asuncion
        America/Toronto
        America/Tortola
        America/Managua
        (20 rows)
    </pre>
   
2. タイム ゾーンの省略形 (例: PST)。 このような指定では、単に UTC からの特定のオフセットが定義されるだけであるのに対し、、完全なタイム ゾーン名の場合は、夏時間の移行日ルールのセットも表すことができます。 認識される省略形は、[**pg_timezone_abbrevs**](https://www.postgresql.org/docs/9.4/view-pg-timezone-abbrevs.html) ビューに一覧表示されます。psql でこのビューのクエリを実行し、タイム ゾーンの省略形の一覧を取得する例:

   <pre> select abbrev from pg_timezone_abbrevs limit 20;</pre>

    次のような結果が表示されます。

     <pre>
        abbrev|
        ------+
        ACDT  |
        ACSST |
        ACST  |
        ACT   |
        ACWST |
        ADT   |
        AEDT  |
        AESST |
        AEST  |
        AFT   |
        AKDT  |
        AKST  |
        ALMST |
        ALMT  |
        AMST  |
        AMT   |
        ANAST |
        ANAT  |
        ARST  |
        ART   |
    </pre>

3. PostgreSQL では、タイム ゾーンの名前と省略形に加えて、STDoffset または STDoffsetDST という形式の POSIX スタイルのタイム ゾーン指定が受け入れられます。ここで、STD はゾーンの省略形、offset は UTC から西回りでのオフセット時間数の値、DST はオプションの夏時間ゾーンの省略形で、指定されたオフセットより 1 時間進むものと想定されます。 
   

## <a name="next-steps"></a>次のステップ
内容は次のとおりです。
- [Azure Database for PostgreSQL のサーバー パラメーターの概要](concepts-server-parameters.md)
- [CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバーのパラメーターを構成する](howto-configure-server-parameters-using-cli.md)
  