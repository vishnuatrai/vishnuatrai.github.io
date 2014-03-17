---
author: admin
comments: true
date: 2010-09-20 14:03:35+00:00
layout: post
slug: indexing-basic-mysql-queries
title: MySql DB Indexing
wordpress_id: 59
categories:
- Technology
tag:
- MySql
---

Working with a online tutorial with a huge amount of data, product owners always used to  raise issues for application performance. After looking in to different scenerios like apache configurations, hardware, network issues, and mysql performance as a backend server, we got to know that there are a lot of opportunities to improve mysql db performance. To see why db performance slow I turned on slow quiry logging in my.cnf:

log-slow-queries
long_query_time = 5 

The slow quesies log file became huge in size in Gigs after only two or three days, and queries was like:

#  Query_time:   5        Lock_time:   0       Rows_sent:   1        Rows_examined:   40508

SELECT quize_id as total_quizes FROM student_quizes WHERE exam_id IN(1,2,3,11)

In abouve example its whowing that “Rows_sent:   1    Rows_examined: 40508”. So, after examining 40508 and returning just 1 row is not a good job done by db server. If there are 200 visitors using the application then multiply this figure with 200, in this case MySQL is examining 8,101,600 rows! Then the Mysql performance is in misurable situation. 

To see why MySQL queries are too slow we can explain them:

    
       mysql> EXPLAIN
           -> SELECT quize_id as total_quizes FROM student_quizes
           -> WHERE exam_id IN (1, 2, 3, 11);
       +-------------------+-------+---------------+-------------------+---------+------+-------+--------------------------+
       | table             | type  | possible_keys | key               | key_len | ref  | rows  | Extra                    |
       +-------------------+-------+---------------+-------------------+---------+------+-------+--------------------------+
       | student_quizes    | index | NULL          | quize_id_exam_id  |       6 | NULL | 40508 | Using where; Using index |
       +-------------------+-------+---------------+-------------------+---------+------+-------+--------------------------+


Here MySQL telling us    there's no possible keys but it's using key quize_id_exam_id? And if it's using a key then why does it suspect it    will have to examine 40508 rows (by performing a full index scan, denoted by "type: index")? It seems there's a    problem with the keys so we must now understand them:

    
    mysql> DESCRIBE student_quizes;
       +-------------+-----------------------+------+-----+---------+-------+
       | Field       | Type                  | Null | Key | Default | Extra |
       +-------------+-----------------------+------+-----+---------+-------+
       | quize_id    | mediumint(8) unsigned |      | MUL | 0       |       |
       | exam_id     | mediumint(8) unsigned |      |     | 0       |       |
       | created_at  | datetime              |      |     | 0       |       |
       | updated_at  | datetime              |      |     | 0       |       |
       +-------------+-----------------------+------+-----+---------+-------+
    
       mysql> SHOW INDEX FROM student_quizes;
       +-------------------+------------+-----------------------+--------------+-------------+-----------+-------------+
       | Table             | Non_unique | Key_name              | Seq_in_index | Column_name | Collation | Cardinality |
       +-------------------+------------+-----------------------+--------------+-------------+-----------+-------------+
       | student_quizes    |          1 | quize_id_exam_id      |            1 | quize_id    | A         |       40508 |
       | student_quizes    |          1 | exam_id_quize_id      |            2 | exam_id     | A         |       40508 |
       +-------------------+------------+-----------------------+--------------+-------------+-----------+-------------+


Understanding indexes is two part: Understanding the structure of the table _then_ understanding the indexes. You can't just slap an index on a table and think everything will be wonderful. In this    example it looks like everything should be wonderful with key quize_id_exam_id. Given that the SELECT statement is    selecting quize_id and exam_id and that's just what this key indexes, so why isn't it working? It is working, just not    how we're intending; it's working for MySQL which is why in EXPLAIN it says "Using index." When MySQL says this    in "Extra" is means "The column information is retrieved from the table using only information in the index tree     without having to do an additional seek to read the actual row." In other words: It finds and returns matching    columns from the index in memory not the table on disk, which is a good thing, unless it's doing this 12 million    times for 1 matching column.

How very annoying: MySQL is using the index but still in effect examing every row of the table. The reason why in    this example deals with how MySQL uses multiple column indexes. From DESCRIBE we see "MUL" for multi-column    index, and from SHOW INDEX we see quize_id_exam_id twice, first for quize_id second for exam_id. A multiple    column index acts like a single column index if the columns were put end-to-end in the order specified by    "Seq_in_index" from SHOW INDEX. In this example if quize_id were 100 and exam_id were 200 this is indexed as    "100 200". Throw this in the mix: MySQL will only use a multi-column index if a value    is specified for the first column in the index. In this example the first column in the index is quize_id and    we're not specifying a value for this column which is why MySQL won't use the index like we want it to. What    MySQL does do, and why it's able to use the index at all,  is use any value for quize_id and the values we gave    it for exam_id. In effect it looks for '*  1', '*  2', '*   3', '*   11'. Since quize_id is unique MySQL really does have to look at every single one, all 40,00+. While doing that if it    comes across one with a matching exam_id lucky for us. I hope you see the obvious and simple solution: Swap    the order of columns in the key, exam_id then quize_id. Later we'll do this but first it's good learning    to examine another possibility.

If exam_id is what we're matching rows on, just index exam_id. The command would be "CREATE INDEX    test_index ON student_quizes (exam_id);". Then EXPLAIN the exact same slow query again:

    
       mysql> EXPLAIN
           -> SELECT quize_id as total_quizes FROM student_quizes
           -> WHERE exam_id IN (1,2,3,11);
       +-------------------+-------+---------------+------------+---------+------+------+-------------+
       | table             | type  | possible_keys | key        | key_len | ref  | rows | Extra       |
       +-------------------+-------+---------------+------------+---------+------+------+-------------+
       | student_quizes    | range | test_index    | test_index |       3 | NULL |    5 | Using where |
       +-------------------+-------+---------------+------------+---------+------+------+-------------+


That output speaks volumes: 5 rows to examine. This is the index that saved the server. I have not seen the server's    load go above 4 and the website is running faster with more users at once. The difference was night and day. But    why stop there? The query is no longer slow but it could be better. Notice how MySQL is not "Using index" anymore.    This is because quize_id is not in the index its using. Whereas it's in the quize_id_exam_id index MySQL has    wisely chosen to examine fewer rows at the cost of doing a few disk seeks. The solution is a multiple column index    on both exam_id and quize_id, with exam_id first.

    
       mysql> CREATE INDEX exam_id_quize_id ON student_quizes (exam_id, quize_id);
       Query OK, 40508 rows affected (0.53 sec)
       Records: 40508  Duplicates: 0  Warnings: 0
    
       mysql> EXPLAIN
           -> SELECT quize_id as total_quizes FROM student_quizes
           -> WHERE exam_id IN (1,2,3,11);
       +-------------------+-------+------------------------------+-------------------+---------+------+------+--------------------------+
       | table             | type  | possible_keys                | key               | key_len | ref  | rows | Extra                    |
       +-------------------+-------+------------------------------+-------------------+---------+------+------+--------------------------+
       | student_quizes    | range | test_index,exam_id_quize_id  | exam_id_quize_id  |       3 | NULL |    5 | Using where; Using index |
       +-------------------+-------+------------------------------+-------------------+---------+------+------+--------------------------+


As we can see MySQL still considers the test_index key but chooses exam_id_quize_id because doing so will allow    it to get matching quize_id from the index instead of the disk. A simple swap of column orders in the index made    all the difference. As the saying goes, it takes one tree to make a thousand matches and one match to burn a    thousand trees down.
