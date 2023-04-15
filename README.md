# SwissRe_task
Repository with coding task for interview.

## Solution information
1. The solution is implemented using Databricks.
2. Project structure is described in configs -> config.yaml.

### Transformation and Test Code 
1. Code is implemented and described in transformations -> create transaction.
2. Class MakeTransactions and TestMakeTransactions by default is using provided **paths to input and output files**, which could be changed by executing the class and providing your paths. **Paths has been implemented in this way because of different paths in Databricks workspace File Store.**.
3. **Only for task purposes** input and output files are stored in dbfs:FileStore but regarding overall architecture concepts, flexibility and security should be stored in **ADLSv2** which then should be mounted with Databricks.
4. API is provided as string **only for task purposes**, but with best praticices it **should be coded as databricks-secret in secret scope (connecting Azure key vault)**

### Execution 
1. Execute **databricks_configuration notebook** to configure environment **specifically** when executing from Databricks Repos.
2. Execution of configuration file will create a nesscesary directory in your **dbfs:/FileStore/mkrupski_coding_task/** and move input/test files.
3. Execute test will show you output of tests.
4. Execute transactions will write output to **dbfs:/FileStore/mkrupski_coding_task/TRANSACTIONS/** in Delta Format.

### Output
1. Output format has been chosen as **DELTA**.
2. Based on the requirements and the data provided, it is better to store data in Delta tables rather than Parquet. Delta tables provide ACID transactions, which ensure data consistency and reliability, while Parquet only provides a columnar storage format without transactional capabilities. In this use case, the data is transformed from two different sources, and the output is a new table that adheres to an internal data model.
3. Output format could depends on business requirements if we have big financial data that requires frequent updates and strong consistency guarantees, Delta would be a good choice. If you need to query and process large amounts of data efficiently, Parquet would be a good choice.
4. Output was also provided as CSV in repository **only for task purposes** to fast overview data.

### Conceptual Questions - new batches
1. To handle new batches of input data without overriding the existing transactions created in previous batches, we can make use of unique identifiers for each batch. This can be achieved by introducing a new column in the output schema of the transactional system, such as "BATCH_ID". The batch ID will be unique for each new batch of input data and can be generated based on the current date and time.
2. In the MakeTransactions class, we can introduce a new method called "generate_batch_id" which will generate a unique batch ID based on the current date and time. We can call this method before processing each batch of input data and add the batch ID to the output schema.
3. To ensure that the existing transactions are not overridden, we can store the output data in a separate table or file for each batch, using the batch ID as the identifier. This will allow us to maintain the history of all transactions generated by each batch of input data.
4. Additionally, we can create a lookup table that maps each batch ID to the corresponding input data batch. This table can be used to track the source of each transaction and to identify the input data batch that was used to generate a specific transaction.
5. By implementing this approach, we can handle new batches of input data without overriding the existing transactions created in previous batches, while maintaining a history of all transactions generated by each batch of input data.

### Conceptual Questions - architecture solution
1. To expand architecture of solution (process) we could extend code and use ADFv2 or Databricks Jobs to create automated tasks with new batches. 
2. First example: Create trigger which will process data when are uploaded with ADF.
3. Second example: Create a Job which will be scheduled on specific part of time.
4. For both examples, to save costs on computation, we could extend code to check file changes/upload date.