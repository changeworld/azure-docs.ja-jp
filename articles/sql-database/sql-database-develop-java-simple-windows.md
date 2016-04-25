<properties
	pageTitle="Windows 上で JDBC を含む Java を使用して、SQL Database に接続する"
	description="Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。サンプルは JDBC を使用し、Windows クライアント コンピューター上で実行されます。"
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="andrela"/>


# Windows 上で JDBC を含む Java を使用して、SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。Java サンプルは、Java Development Kit (JDK) バージョン 1.8 に依存します。サンプルは、JDBC ドライバーを使用して、Azure SQL Database に接続されます。

## 手順 1: 開発環境を設定する

ドライバーとライブラリのインストール:

- [Microsoft JDBC 4.2 driver for SQL Server](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774)
- [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) を実行する任意のオペレーティング システム プラットフォーム

## 手順 2: SQL Database を作成する

「[作業の開始](sql-database-get-started.md)」ページで、サンプル データベースを作成する方法についてご確認ください。ガイドに従って、**AdventureWorks データベースのテンプレート**を作成することが重要です。以下に示す例は、**AdventureWorks スキーマ** とのみ動作します。

## 手順 3: 接続文字列を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] JTDS JDBC ドライバーを使用している場合は、接続文字列の URL に "ssl=require" を追加し、JVM の次のオプションを設定する必要があります。"-Djsse.enableCBCProtection=false"。この JVM オプションはセキュリティの脆弱性を修正するプログラムを無効にするため、このオプションを設定する前に、どのようなリスクがあるかを必ず理解しておいてください。

## 手順 4: 接続する

接続クラスを使用して、SQL Database に接続します。

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## 手順 5: クエリを実行する
この例では、Azure SQL Database に接続し、SELECT ステートメントを実行して、選択した行を返します。

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute a SELECT SQL statement.
					String selectSql = "SELECT TOP 10 Title, FirstName, LastName from SalesLT.Customer";
					statement = connection.createStatement();
					resultSet = statement.executeQuery(selectSql);
	
					// Print results from select statement
					while (resultSet.next()) 
					{
						System.out.println(resultSet.getString(2) + " "
							+ resultSet.getString(3));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## 手順 6: 行を挿入する
この例では、INSERT ステートメントを実行し、パラメーターを渡して、自動生成されたプライマリ キーの値を取得します。

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
				PreparedStatement prepsInsertProduct = null;
				
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute an INSERT SQL prepared statement.
					String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES "
						+ "('NewBike', 'BikeNew', 'Blue', 50, 120, '2016-01-01');";
	
					prepsInsertProduct = connection.prepareStatement(
						insertSql,
						Statement.RETURN_GENERATED_KEYS);
					prepsInsertProduct.execute();
					
					// Retrieve the generated key from the insert.
					resultSet = prepsInsertProduct.getGeneratedKeys();
					
					// Print the ID of the inserted row.
					while (resultSet.next()) {
						System.out.println("Generated: " + resultSet.getString(1));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (prepsInsertProduct != null) try { prepsInsertProduct.close(); } catch(Exception e) {}
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}


## 次のステップ

詳細については、[Java デベロッパー センター](/develop/java/)を参照してください。

<!---HONumber=AcomDC_0413_2016-->