# 0.未优化过的逻辑计划
```
Aggregate [avg(total#32) AS avgScore#33]
+- SubqueryAlias `tmp`
   +- Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 1.org.apache.spark.sql.catalyst.analysis.EliminateSubqueryAliases

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
   +- Filter (cast(age#12 as int) < 35)
      +- Join Inner, (id#10 = studentId#26)
         :- Relation[id#10,name#11,age#12] csv
         +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 2.org.apache.spark.sql.catalyst.optimizer.PushPredicateThroughJoin

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
   +- Join Inner, (id#10 = studentId#26)
      :- Filter (cast(age#12 as int) < 35)
      :  +- Relation[id#10,name#11,age#12] csv
      +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 3.org.apache.spark.sql.catalyst.optimizer.ColumnPruning

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [total#32]
   +- Project [((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
      +- Join Inner, (id#10 = studentId#26)
         :- Project [id#10]
         :  +- Filter (cast(age#12 as int) < 35)
         :     +- Relation[id#10,name#11,age#12] csv
         +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 4.org.apache.spark.sql.catalyst.optimizer.CollapseProject

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
   +- Join Inner, (id#10 = studentId#26)
      :- Project [id#10]
      :  +- Filter (cast(age#12 as int) < 35)
      :     +- Relation[id#10,name#11,age#12] csv
      +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 5.org.apache.spark.sql.catalyst.optimizer.ConstantFolding

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [((180.0 + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
   +- Join Inner, (id#10 = studentId#26)
      :- Project [id#10]
      :  +- Filter (cast(age#12 as int) < 35)
      :     +- Relation[id#10,name#11,age#12] csv
      +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 6.org.apache.spark.sql.catalyst.optimizer.InferFiltersFromConstraints

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [((180.0 + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
   +- Join Inner, (id#10 = studentId#26)
      :- Filter isnotnull(id#10)
      :  +- Project [id#10]
      :     +- Filter (isnotnull(age#12) && (cast(age#12 as int) < 35))
      :        +- Relation[id#10,name#11,age#12] csv
      +- Filter isnotnull(studentId#26)
         +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 7.org.apache.spark.sql.catalyst.optimizer.PushDownPredicate

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [((180.0 + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
   +- Join Inner, (id#10 = studentId#26)
      :- Project [id#10]
      :  +- Filter isnotnull(id#10)
      :     +- Filter (isnotnull(age#12) && (cast(age#12 as int) < 35))
      :        +- Relation[id#10,name#11,age#12] csv
      +- Filter isnotnull(studentId#26)
         +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 8.org.apache.spark.sql.catalyst.optimizer.CombineFilters

```
Aggregate [avg(total#32) AS avgScore#33]
+- Project [((180.0 + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
   +- Join Inner, (id#10 = studentId#26)
      :- Project [id#10]
      :  +- Filter ((isnotnull(age#12) && (cast(age#12 as int) < 35)) && isnotnull(id#10))
      :     +- Relation[id#10,name#11,age#12] csv
      +- Filter isnotnull(studentId#26)
         +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```

