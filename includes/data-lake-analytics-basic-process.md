**基本的な Data Lake Analytics のプロセス:**
	
![Azure Data Lake Analytics プロセスのフロー図](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)
	
	1. Create a Data Lake Analytics account.
	2. Prepare the source data. Data Lake Analytic jobs can read data from either Azure Data Lake Store accounts or Azure Blob storage accounts.   
	3. Develop a U-SQL script.
	4. Submit a job (U-SQL script) to the Data Lake Analytics account. The job reads from the source data, process the data as instructed in the U-SQL script, and then save the output to either a Data Lake Store account or a Blob storage account.

<!---HONumber=AcomDC_0427_2016-->