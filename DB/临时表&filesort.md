### 背景
    MySQL 执行查询语句， 对于order by谓词，可能会使用filesort或者temporary。比如explain一条语句的时候，会看到Extra字段中可能会出现，using filesort和using temporary。

### 解释
    1.using filesort
    filesort主要用于查询数据结果集的排序操作，首先Mysql会使用sort_buffer_size大小的内存进行排序。
    sort_buffer_size大小，会把这一个排序后的chunk转移到file上，最后使用多路归并排序完成所有数据的排序操作。
    2. using temporary
    MySQL使用临时表保存临时的结构，以用于后续的处理，MySQL首先创建heap引擎的临时表，如果临时的数据过多，超过max_heap_table_size的大小，会自动把临时表转换成MyISAM引擎的表来使用。
    从上面的解释上来看，filesort和temporary的使用场景的区别并不是很明显，不过，有以下的原则:
    filesort只能应用在单个表上，如果有多个表的数据需要排序，那么MySQL会先使用using temporary保存临时数据，然后再在临时表上使用filesort进行排序，最后输出结果。
    