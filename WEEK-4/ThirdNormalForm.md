Design a database of a college in or above 3rd normalized form
Main entities should be Students, lecturers, courses and subjects


 For this tasks First a table was created at college1. which had the following attributes and values
```
    =>  desc college1;
=>+-----------------+-------------+------+-----+---------+-------+
| Field           | Type        | Null | Key | Default | Extra |
+-----------------+-------------+------+-----+---------+-------+
| student_id      | int(11)     | NO   | PRI | NULL    |       |
| student_name    | varchar(20) | YES  |     | NULL    |       |
| student_city    | varchar(20) | YES  |     | NULL    |       |
| student_state   | varchar(20) | YES  |     | NULL    |       |
| student_course  | varchar(20) | YES  |     | NULL    |       |
| course_lecturer | varchar(20) | YES  |     | NULL    |       |
| course_id       | int(11)     | YES  |     | NULL    |       |
+-----------------+-------------+------+-----+---------+-------+

=>select * from college2;
=>+------------+--------------+--------------+---------------+----------------+
| student_id | student_name | student_city | student_state | student_course |
+------------+--------------+--------------+---------------+----------------+
|          1 | raju         | bangalore    | karnataka     | computersc     |
|          2 | varshini     | kochi        | kerala        | infosc         |
|          3 | akash        | patna        | bihar         | mech           |
|          4 | sumit        | lucknow      | uttarpradesh  | civil          |
|          5 | puneet       | bangalore    | karnataka     | computersc     |
+------------+--------------+--------------+---------------+----------------+

```

```
=> (The above we see Primary Attribute as (student_id ) )
=> The other attribute 
    => That is  student_name | student_city | student_state | student_course 
               
               (   | raju   | bangalore    | karnataka     | computersc      )
                                 
                                  (and)

               (| puneet   | bangalore    | karnataka     | computersc )           
```

```
=> Raju and Punnet have the same student_city, student_state and student_course 
=> So, it's like Non prime attribute used to determine the Non prime attribute 
 => Suppose => student_city and student_course  ( are same in case of Raju and Puneet)

 => They can be differentiated use the PRIMARY KEY. 1=>Raju     5=>Puneet (Different Primary Key use to differentiate)
 
 ```
 The Third Divided Table is as follows 
 
 ```
 => desc college3;
MariaDB [college]> desc college3;
--------------
desc college3
--------------

+-----------------+-------------+------+-----+---------+-------+
| Field           | Type        | Null | Key | Default | Extra |
+-----------------+-------------+------+-----+---------+-------+
| course_id       | int(11)     | NO   | PRI | NULL    |       |
| course_name     | varchar(20) | YES  |     | NULL    |       |
| duration        | int(11)     | YES  |     | NULL    |       |
| course_lecturer | varchar(20) | YES  |     | NULL    |       |
+-----------------+-------------+------+-----+---------+-------+
4 rows in set (0.000 sec)

MariaDB [college]> select * from college3;
--------------
select * from college3
--------------

+-----------+------------------+----------+-----------------+
| course_id | course_name      | duration | course_lecturer |
+-----------+------------------+----------+-----------------+
|         1 | artificialintell |        2 | nptel           |
|         2 | datascience      |        3 | udemy           |
|         3 | python           |        4 | udacity         |
|         4 | java             |        5 | youtube         |
|         5 | python           |        4 | udacity         |
+-----------+------------------+----------+-----------------+
5 rows in set (0.001 sec)
```
Here we see Conflicting values of course_name and course_lecturer in 3,5 where they have same values.
```

=> where
   =>course_id|course_name|duration|course_lecturer
   => 3 |       python  |        4 | udacity  
   => 5 |       python  |        4 | udacity    


   => The Non prime attributes course_name and course_lecturer
   => Can be  determined the Non prime attribute ( i.e course_name and course_lecturer)
   => Since, This is conflicting we have the Prime Attribute which is used to differentiate between them.

   => That is how the third Normal Form can be Achived.

```

 
