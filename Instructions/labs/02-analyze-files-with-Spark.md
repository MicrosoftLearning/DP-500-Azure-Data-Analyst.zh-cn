---
lab:
  title: 使用 Spark 分析 Data Lake 中的数据
  module: 'Model, query, and explore data in Azure Synapse'
---

# <a name="analyze-data-in-a-data-lake-with-spark"></a>使用 Spark 分析 Data Lake 中的数据

Apache Spark is an open source engine for distributed data processing, and is widely used to explore, process, and analyze huge volumes of data in data lake storage. Spark is available as a processing option in many data platform products, including Azure HDInsight, Azure Databricks, and Azure Synapse Analytics on the Microsoft Azure cloud platform. One of the benefits of Spark is support for a wide range of programming languages, including Java, Scala, Python, and SQL; making Spark a very flexible solution for data processing workloads including data cleansing and manipulation, statistical analysis and machine learning, and data analytics and visualization.

完成本实验室大约需要 45 分钟。

## <a name="before-you-start"></a>准备工作

需要一个你在其中具有管理级权限的 [Azure 订阅](https://azure.microsoft.com/free)。

## <a name="provision-an-azure-synapse-analytics-workspace"></a>预配 Azure Synapse Analytics 工作区

需要一个 Azure Synapse Analytics 工作区，该工作区可以访问 Data Lake Storage 和 Apache Spark 池（可用于查询和处理 Data Lake 中的文件）。

在本练习中，你将组合使用 PowerShell 脚本和 ARM 模板来预配 Azure Synapse Analytics 工作区。

1. 登录到 Azure 门户，地址为 [](https://portal.azure.com)。
2. Use the <bpt id="p1">**</bpt>[<ph id="ph1">\&gt;</ph>_]<ept id="p1">**</ept> button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a <bpt id="p2">***</bpt>PowerShell<ept id="p2">***</ept> environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![具有 Cloud Shell 窗格的 Azure 门户](../images/cloud-shell.png)

    > 注意：如果以前创建了使用 Bash 环境的 Cloud shell，请使用 Cloud Shell 窗格左上角的下拉菜单将其更改为 PowerShell。

3. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the <bpt id="p1">**</bpt>&amp;#8212;<ept id="p1">**</ept>, <bpt id="p2">**</bpt>&amp;#9723;<ept id="p2">**</ept>, and <bpt id="p3">**</bpt>X<ept id="p3">**</ept> icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the <bpt id="p1">[</bpt>Azure Cloud Shell documentation<ept id="p1">](https://docs.microsoft.com/azure/cloud-shell/overview)</ept>.

4. 在 PowerShell 窗格中，输入以下命令以克隆此存储库：

    ```
    rm -r dp500 -f
    git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst dp500
    ```

5. 克隆存储库后，输入以下命令以更改为此实验室的文件夹，然后运行其中包含的 setup.ps1 脚本：

    ```
    cd dp500/Allfiles/02
    ./setup.ps1
    ```

6. 如果出现提示，请选择要使用的订阅（仅当有权访问多个 Azure 订阅时才会发生这种情况）。
7. 出现提示时，输入要为 Azure Synapse SQL 池设置的合适密码。

    > 注意：请务必记住此密码！

8. Apache Spark 是用于分布式数据处理的开放源代码引擎，广泛用于探索、处理和分析 Data Lake Storage 中的大量数据。

## <a name="query-data-in-files"></a>查询文件中的数据

该脚本预配 Azure Synapse Analytics 工作区和 Azure 存储帐户来托管 Data Lake，然后将一些数据文件上传到 Data Lake。

### <a name="view-files-in-the-data-lake"></a>查看 Data Lake 中的文件

1. 脚本完成后，在 Azure 门户中转到创建的 dp500-*xxxxxxx* 资源组，然后选择 Synapse 工作区。
2. 在 Synapse 工作区“概述”页的“打开 Synapse Studio”卡中，选择“打开”，以在新浏览器标签页中打开 Synapse Studio；如果出现提示，请进行登录  。
3. 在 Synapse Studio 左侧，使用 &rsaquo;&rsaquo; 图标展开菜单，这将显示 Synapse Studio 中用于管理资源和执行数据分析任务的不同页面。
4. Spark 在许多数据平台产品中作为处理选项提供，包括 Azure HDInsight、Azure Databricks 和 Microsoft Azure 云平台上的 Azure Synapse Analytics。
5. 在“数据”页上，查看“已链接”选项卡并验证工作区是否包含 Azure Data Lake Storage Gen2 存储帐户的链接，该帐户的名称应类似于 synapsexxxxxxx* (Primary - datalake xxxxxxx*) ** 。
6. 展开存储帐户，验证它是否包含名为 files 的文件系统容器。
7. Spark 的优点之一是支持各种编程语言，包括 Java、Scala、Python 和 SQL；这让 Spark 一种非常灵活的数据处理工作负载（包括数据清理和操作、统计分析和机器学习以及数据分析和可视化）解决方案。
8. 打开 sales 文件夹及其包含的 orders 文件夹，并观察 orders 文件夹中包含具有三年销售数据的 .csv 文件  。
9. Right-click any of the files and select <bpt id="p1">**</bpt>Preview<ept id="p1">**</ept> to see the data it contains. Note that the files do not contain a header row, so you can unselect the option to display column headers.

### <a name="use-spark-to-explore-data"></a>使用 Spark 浏览数据

1. Select any of the files in the <bpt id="p1">**</bpt>orders<ept id="p1">**</ept> folder, and then in the <bpt id="p2">**</bpt>New notebook<ept id="p2">**</ept> list on the toolbar, select <bpt id="p3">**</bpt>Load to DataFrame<ept id="p3">**</ept>. A dataframe is a structure in Spark that represents a tabular dataset.
2. In the new <bpt id="p1">**</bpt>Notebook 1<ept id="p1">**</ept> tab that opens, in the <bpt id="p2">**</bpt>Attach to<ept id="p2">**</ept> list, select your Spark pool (*<bpt id="p3">*</bpt>spark<ept id="p3">*</ept>xxxxxxx***). Then use the <bpt id="p1">**</bpt>&amp;#9655; Run all<ept id="p1">**</ept> button to run all of the cells in the notebook (there's currently only one!).

    Since this is the first time you've run any Spark code in this session, the Spark pool must be started. This means that the first run in the session can take a few minutes. Subsequent runs will be quicker.

3. 在等待 Spark 会话初始化时，请查看生成的代码；如下所示：

    ```Python
    %%pyspark
    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/sales/orders/2019.csv', format='csv'
    ## If header exists uncomment line below
    ##, header=True
    )
    display(df.limit(10))
    ```

4. When the code has finished running, review the output beneath the cell in the notebook. It shows the first ten rows in the file you selected, with automatic column names in the form <bpt id="p1">**</bpt>_c0<ept id="p1">**</ept>, <bpt id="p2">**</bpt>_c1<ept id="p2">**</ept>, <bpt id="p3">**</bpt>_c2<ept id="p3">**</ept>, and so on.
5. Modify the code so that the <bpt id="p1">**</bpt>spark.read.load<ept id="p1">**</ept> function reads data from <bpt id="p2">&lt;u&gt;</bpt>all<ept id="p2">&lt;/u&gt;</ept> of the CSV files in the folder, and the <bpt id="p3">**</bpt>display<ept id="p3">**</ept> function shows the first 100 rows. Your code should look like this (with <bpt id="p1">*</bpt>datalakexxxxxxx<ept id="p1">*</ept> matching the name of your data lake store):

    ```Python
    %%pyspark
    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/sales/orders/*.csv', format='csv'
    )
    display(df.limit(100))
    ```

6. 使用代码单元格左侧的“&#9655;”按钮仅运行该单元格，然后查看结果。

    The dataframe now includes data from all of the files, but the column names are not useful. Spark uses a "schema-on-read" approach to try to determine appropriate data types for the columns based on the data they contain, and if a header row is present in a text file it can be used to identify the column names (by specifying a <bpt id="p1">**</bpt>header=True<ept id="p1">**</ept> parameter in the <bpt id="p2">**</bpt>load<ept id="p2">**</ept> function). Alternatively, you can define an explicit schema for the dataframe.

7. Modify the code as follows (replacing <bpt id="p1">*</bpt>datalakexxxxxxx<ept id="p1">*</ept>), to define an explicit schema for the dataframe that includes the column names and data types. Rerun the code in the cell.

    ```Python
    %%pyspark
    from pyspark.sql.types import *
    from pyspark.sql.functions import *

    orderSchema = StructType([
        StructField("SalesOrderNumber", StringType()),
        StructField("SalesOrderLineNumber", IntegerType()),
        StructField("OrderDate", DateType()),
        StructField("CustomerName", StringType()),
        StructField("Email", StringType()),
        StructField("Item", StringType()),
        StructField("Quantity", IntegerType()),
        StructField("UnitPrice", FloatType()),
        StructField("Tax", FloatType())
        ])

    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/sales/orders/*.csv', format='csv', schema=orderSchema)
    display(df.limit(100))
    ```

8. 使用页面顶部搜索栏右侧的 [\>_] 按钮在 Azure 门户中创建新的 Cloud Shell，在出现提示时选择“PowerShell”环境并创建存储。

    ```Python
    df.printSchema()
    ```

9. Cloud Shell 在 Azure 门户底部的窗格中提供命令行界面，如下所示：

## <a name="analyze-data-in-a-dataframe"></a>分析数据帧中的数据

Spark 中的 dataframe 对象类似于 Python 中的 Pandas 数据帧，它包含各种可用于操作、筛选、分组或以其他方式分析其所含数据的函数。

### <a name="filter-a-dataframe"></a>筛选数据帧

1. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```Python
    customers = df['CustomerName', 'Email']
    print(customers.count())
    print(customers.distinct().count())
    display(customers.distinct())
    ```

2. Run the new code cell, and review the results. Observe the following details:
    - 对数据帧执行操作时，结果是一个新数据帧（在本例中，是一个新客户数据帧，它是通过从 df 数据帧中选择特定的列子集来创建的） 
    - 数据帧提供 count 和 distinct 等函数，可用于汇总和筛选它们包含的数据 。
    - The <ph id="ph1">`dataframe['Field1', 'Field2', ...]`</ph> syntax is a shorthand way of defining a subset of column. You can also use <bpt id="p1">**</bpt>select<ept id="p1">**</ept> method, so the first line of the code above could be written as <ph id="ph1">`customers = df.select("CustomerName", "Email")`</ph>

3. 按如下所示修改代码：

    ```Python
    customers = df.select("CustomerName", "Email").where(df['Item']=='Road-250 Red, 52')
    print(customers.count())
    print(customers.distinct().count())
    display(customers.distinct())
    ```

4. Run the modified code to view the customers who have purchased the <bpt id="p1">*</bpt>Road-250 Red, 52<ept id="p1">*</ept> product. Note that you can "chain" multiple functions together so that the output of one function becomes the input for the next - in this case, the dataframe created by the <bpt id="p1">**</bpt>select<ept id="p1">**</ept> method is the source dataframe for the <bpt id="p2">**</bpt>where<ept id="p2">**</ept> method that is used to apply filtering criteria.

### <a name="aggregate-and-group-data-in-a-dataframe"></a>在数据帧中对数据进行聚合和分组

1. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```Python
    productSales = df.select("Item", "Quantity").groupBy("Item").sum()
    display(productSales)
    ```

2. 请注意，可以通过拖动窗格顶部的分隔条或使用窗格右上角的 &#8212;、&#9723; 或 X 图标来调整 Cloud Shell 的大小，以最小化、最大化和关闭窗格  。

3. 在笔记本中再次新增一个代码单元格，并在其中输入以下代码：

    ```Python
    yearlySales = df.select(year("OrderDate").alias("Year")).groupBy("Year").count().orderBy("Year")
    display(yearlySales)
    ```

4. 有关如何使用 Azure Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 文档](https://docs.microsoft.com/azure/cloud-shell/overview)。

## <a name="query-data-using-spark-sql"></a>使用 Spark SQL 查询数据

As you've seen, the native methods of the dataframe object enable you to query and analyze data quite effectively. However, many data analysts are more comfortable working with SQL syntax. Spark SQL is a SQL language API in Spark that you can use to run SQL statements, or even persist data in relational tables.

### <a name="use-spark-sql-in-pyspark-code"></a>在 PySpark 代码中使用 Spark SQL

The default language in Azure Synapse Studio notebooks is PySpark, which is a Spark-based Python runtime. Within this runtime, you can use the <bpt id="p1">**</bpt>spark.sql<ept id="p1">**</ept> library to embed Spark SQL syntax within your Python code, and work with SQL constructs such as tables and views.

1. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```Python
    df.createOrReplaceTempView("salesorders")

    spark_df = spark.sql("SELECT * FROM salesorders")
    display(spark_df)
    ```

2. Run the cell and review the results. Observe that:
    - The code persists the data in the <bpt id="p1">**</bpt>df<ept id="p1">**</ept> dataframe as a temporary view named <bpt id="p2">**</bpt>salesorders<ept id="p2">**</ept>. Spark SQL supports the use of temporary views or persisted tables as sources for SQL queries.
    - 然后，使用 spark.sql 方法对 salesorders 视图运行 SQL 查询 。
    - 查询结果存储在数据帧中。

### <a name="run-sql-code-in-a-cell"></a>在单元格中运行 SQL

虽然能够将 SQL 语句嵌入到包含 PySpark 代码的单元格中非常有用，但数据分析师通常只想直接使用 SQL。

1. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```sql
    %%sql
    SELECT YEAR(OrderDate) AS OrderYear,
           SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue
    FROM salesorders
    GROUP BY YEAR(OrderDate)
    ORDER BY OrderYear;
    ```

2. Run the cell and review the results. Observe that:
    - 单元格开头的 `%%sql` 行（称为 magic）指示应使用 Spark SQL 语言运行时来运行此单元格中的代码，而不是 PySpark。
    - SQL 代码引用以前使用 PySpark 创建的 salesorder 视图。
    - SQL 查询的输出将自动显示为单元格下的结果。

> 注意：有关 Spark SQL 和数据帧的详细信息，请参阅 [Spark SQL 文档](https://spark.apache.org/docs/2.2.0/sql-programming-guide.html)。

## <a name="visualize-data-with-spark"></a>使用 Spark 直观呈现数据

A picture is proverbially worth a thousand words, and a chart is often better than a thousand rows of data. While notebooks in Azure Synapse Analytics include a built in chart view for data that is displayed from a dataframe or Spark SQL query, it is not designed for comprehensive charting. However, you can use Python graphics libraries like <bpt id="p1">**</bpt>matplotlib<ept id="p1">**</ept> and <bpt id="p2">**</bpt>seaborn<ept id="p2">**</ept> to create charts from data in dataframes.

### <a name="view-results-as-a-chart"></a>以图表形式查看结果

1. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```sql
    %%sql
    SELECT * FROM salesorders
    ```

2. 运行代码并观察它是否从之前创建的 salesorders 视图返回数据。
3. 在单元格下方的结果部分中，将“视图”选项从“表格”更改为“图表”  。
4. 等待脚本完成 - 此过程通常需要大约 10 分钟；但在某些情况下可能需要更长的时间。
    - **图表类型**：条形图
    - **键**：项
    - **值**：数量
    - **序列组**：留空
    - **聚合**：Sum
    - **堆积**：未选中

5. 验证图表是否如下所示：

    ![按订单总数排列的产品条形图](../images/notebook-chart.png)

### <a name="get-started-with-matplotlib"></a>matplotlib 入门

1. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```Python
    sqlQuery = "SELECT CAST(YEAR(OrderDate) AS CHAR(4)) AS OrderYear, \
                    SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue \
                FROM salesorders \
                GROUP BY CAST(YEAR(OrderDate) AS CHAR(4)) \
                ORDER BY OrderYear"
    df_spark = spark.sql(sqlQuery)
    df_spark.show()
    ```

2. 运行代码并观察它是否返回包含年收入的 Spark 数据帧。

    等待时，请查看 Azure Synapse Analytics 文档中的 [Azure Synapse Analytics 中的 Apache Spark](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-overview) 一文。

3. 在笔记本中新增一个代码单元格，并在其中添加以下代码：

    ```Python
    from matplotlib import pyplot as plt

    # matplotlib requires a Pandas dataframe, not a Spark one
    df_sales = df_spark.toPandas()

    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'])

    # Display the plot
    plt.show()
    ```

4. Run the cell and review the results, which consist of a column chart with the total gross revenue for each year. Note the following features of the code used to produce this chart:
    - matplotlib 库需要 Pandas 数据帧，因此需要将 Spark SQL 查询返回的 Spark 数据帧转换为此格式 。
    - At the core of the <bpt id="p1">**</bpt>matplotlib<ept id="p1">**</ept> library is the <bpt id="p2">**</bpt>pyplot<ept id="p2">**</ept> object. This is the foundation for most plotting functionality.
    - 默认设置会生成一个可用图表，但它有很大的自定义空间

5. 修改代码以绘制图表，如下所示：

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

    # Customize the chart
    plt.title('Revenue by Year')
    plt.xlabel('Year')
    plt.ylabel('Revenue')
    plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
    plt.xticks(rotation=45)

    # Show the figure
    plt.show()
    ```

6. Re-run the code cell and view the results. The chart now includes a little more information.

    A plot is technically contained with a <bpt id="p1">**</bpt>Figure<ept id="p1">**</ept>. In the previous examples, the figure was created implicitly for you; but you can create it explicitly.

7. 修改代码以绘制图表，如下所示：

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a Figure
    fig = plt.figure(figsize=(8,3))

    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

    # Customize the chart
    plt.title('Revenue by Year')
    plt.xlabel('Year')
    plt.ylabel('Revenue')
    plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
    plt.xticks(rotation=45)

    # Show the figure
    plt.show()
    ```

8. Re-run the code cell and view the results. The figure determines the shape and size of the plot.

    图可以包含多个子图，每个子图都其自己的轴上。

9. 修改代码以绘制图表，如下所示：

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a figure for 2 subplots (1 row, 2 columns)
    fig, ax = plt.subplots(1, 2, figsize = (10,4))

    # Create a bar plot of revenue by year on the first axis
    ax[0].bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
    ax[0].set_title('Revenue by Year')

    # Create a pie chart of yearly order counts on the second axis
    yearly_counts = df_sales['OrderYear'].value_counts()
    ax[1].pie(yearly_counts)
    ax[1].set_title('Orders per Year')
    ax[1].legend(yearly_counts.keys().tolist())

    # Add a title to the Figure
    fig.suptitle('Sales Data')

    # Show the figure
    plt.show()
    ```

10. Re-run the code cell and view the results. The figure contains the subplots specified in the code.

> 注意：若要详细了解如何使用 matplotlib 绘图，请参阅 [matplotlib 文档](https://matplotlib.org/)。

### <a name="use-the-seaborn-library"></a>使用 seaborn 库

While <bpt id="p1">**</bpt>matplotlib<ept id="p1">**</ept> enables you to create complex charts of multiple types, it can require some complex code to achieve the best results. For this reason, over the years, many new libraries have been built on the base of matplotlib to abstract its complexity and enhance its capabilities. One such library is <bpt id="p1">**</bpt>seaborn<ept id="p1">**</ept>.

1. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```Python
    import seaborn as sns

    # Clear the plot area
    plt.clf()

    # Create a bar chart
    ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```

2. 运行代码并观察它是否使用 seaborn 库显示条形图。
3. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```Python
    # Clear the plot area
    plt.clf()

    # Set the visual theme for seaborn
    sns.set_theme(style="whitegrid")

    # Create a bar chart
    ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```

4. 运行代码，注意 seaborn 能够让你为绘图设置一致的颜色主题。

5. 在笔记本中新增一个代码单元格，并在其中输入以下代码：

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a bar chart
    ax = sns.lineplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```

6. 运行代码，以折线图的形式查看年收入。

> 注意：若要详细了解如何使用 seaborn 绘图，请参阅 [seaborn 文档](https://seaborn.pydata.org/index.html)。

## <a name="delete-azure-resources"></a>删除 Azure 资源

你已完成对 Azure Synapse Analytics 的探索，现在应删除已创建的资源，以避免产生不必要的 Azure 成本。

1. 关闭 Synapse Studio 浏览器选项卡并返回到 Azure 门户。
2. 在 Azure 门户的“主页”上，选择“资源组”。
3. 选择 Synapse Analytics 工作区的 dp500-*xxxxxxx* 资源组（不是受管理资源组），并确认它包含 Synapse 工作区、存储帐户和工作区的 Spark 池。
4. 在资源组的“概述”页的顶部，选择“删除资源组”。
5. 输入 dp500-*xxxxxxx* 资源组名称以确认要删除该资源组，然后选择“删除” 。

    几分钟后，将删除 Azure Synapse 工作区资源组及其关联的托管工作区资源组。
