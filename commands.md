
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./master/my sqldump --add-drop-table test > dumps/full.dump
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./master/my sqldump --add-drop-table -c test > dumps/full_comp_ins.dump
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./master/my sqldump --no-data test > dumps/ddl.dump
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./master/my sqldump --skip-create-options test > dumps/only_data.dump

emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./m bug < dumps/full.dump 
ERROR 182 (HY000) at line 73: Invalid InnoDB FTS Doc ID
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./m bug < dumps/full_comp_ins.dump 
ERROR 182 (HY000) at line 73: Invalid InnoDB FTS Doc ID
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./m bug < dumps/ddl.dump 
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./m bug < dumps/only_data.dump 
ERROR 182 (HY000) at line 73: Invalid InnoDB FTS Doc ID

emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ master/my sqldump -c -t -e test bookContentByLine > dumps/bcl_onlydata.dump
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./m bug < dumps/bcl_onlydata.dump 
ERROR 182 (HY000) at line 42: Invalid InnoDB FTS Doc ID

master [localhost] {msandbox} (test) > show create table bookContentByLine\G
*************************** 1. row ***************************
       Table: bookContentByLine
Create Table: CREATE TABLE `bookContentByLine` (
  `FTS_DOC_ID` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `bookid` bigint(20) DEFAULT NULL,
  `content` text,
  PRIMARY KEY (`FTS_DOC_ID`),
  KEY `ftsbookid` (`bookid`),
  FULLTEXT KEY `ftscontent` (`content`)
) ENGINE=InnoDB AUTO_INCREMENT=145296 DEFAULT CHARSET=latin1
1 row in set (0,00 sec)

master [localhost] {msandbox} (test) > select count(*) from bookContentByLine;
+----------+
| count(*) |
+----------+
|   100732 |
+----------+
1 row in set (0,05 sec)
master [localhost] {msandbox} (test) > select FTS_DOC_ID, bookid FROM bookContentByLine WHERE FTS_DOC_ID < 3;
+------------+--------+
| FTS_DOC_ID | bookid |
+------------+--------+
|          1 |  12242 |
|          2 |  12242 |
+------------+--------+
2 rows in set (0,00 sec)


emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ master/my sqldump test bookContentByLine > dumps/bcl.dump
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./m bug < dumps/bcl.dump 
ERROR 182 (HY000) at line 41: Invalid InnoDB FTS Doc ID


emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ master/my sqldump --no-data test bookContentByLine | ./m bug
emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ master/my sqldump --single-transaction test bookContentByLine | ./m bug
ERROR 182 (HY000) at line 41: Invalid InnoDB FTS Doc ID
mysqldump: Got errno 32 on write

emanuel@3laptop ~/sandboxes/rsandbox_5_7_9 $ ./m bug

master [localhost] {msandbox} (bug) > INSERT INTO `bookContentByLine` (`FTS_DOC_ID`, `bookid`, `content`) VALUES (1,12242,'\"Project Gutenberg\'s Poems: Three Series, Complete, by Emily Dickinson\"'),(2,12242,'\"This eBook is for the use of anyone anywhere at no cost and with\"'),(3,12242,'\"almost no restrictions whatsoever.  You may copy it, give it away or\"'),(4,12242,'\"re-use it under the terms of the Project Gutenberg License included\"'),(5,12242,'\"with this eBook or online at www.gutenberg.net\"'),(6,12242,'\"Title: Poems: Three Series, Complete\"'),(7,12242,'\"Author: Emily Dickinson\"'),(8,12242,'\"Release Date: May 3, 2004 [EBook #12242]\"'),(9,12242,'\"Language: English\"'),(10,12242,'\"*** START OF THIS PROJECT GUTENBERG EBOOK POEMS: THREE SERIES, COMPLETE ***\"'),(11,12242,'\"Produced by Jim Tinsley <jtinsley@pobox.com>\"'),(12,12242,'\"POEMS\"'),(13,12242,'\"by EMILY DICKINSON\"');
Query OK, 13 rows affected (0,02 sec)
Records: 13  Duplicates: 0  Warnings: 0



