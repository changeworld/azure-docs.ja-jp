
<!--
includes/sql-data-warehouse-backup-retention-policies.md

Latest Freshness check:  2016-05-05 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-overview-expectations.md
articles/sql-data-warehouse/sql-data-warehouse-overview-backup-and-restore.md
-->
SQL Data Warehouse は Azure Storage Snapshots を使用して少なくとも 8 時間ごとにすべてのライブ データのスナップショットを取得します。これらのスナップショットは、7 日間保持されます。これにより、最後のスナップショットが取得された時刻から過去 7 日間の少なくとも 21 の時点のデータを復元できます。

SQL Data Warehouse では、データベースが削除される前にデータベース スナップショットを取得し、7 日間保持します。この場合、ライブ データベースからのスナップショットは保持されなくなります。これにより、削除済みのデータベースを削除された時点の状態に復元することができます。

SQL Data Warehouse は、リージョンに障害が発生した場合の回復性を高めるために、地理的に異なる Azure リージョンにスナップショットを非同期にコピーします。Azure リージョンの障害のためにデータベースにアクセスできない場合は、地理冗長化されたスナップショットのいずれかにデータベースを復元できます。

<!---HONumber=AcomDC_0525_2016-->