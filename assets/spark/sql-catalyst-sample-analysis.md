```sql
SELECT avg(total) AS avgScore
FROM (
    SELECT
        student.id,
        ( 100 + 80 + score.mathScore + score.englishScore ) AS total
    FROM student
    JOIN score ON (student.id = score.studentId)
    WHERE student.age < 35
) tmp
```



# 0.Parsed Logical Plan

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project ['student.id, (((100 + 80) + 'score.mathScore) + 'score.englishScore) AS total#32]
      +- 'Filter ('student.age < 35)
         +- 'Join Inner, ('student.id = 'score.studentId)
            :- 'UnresolvedRelation `student`
            +- 'UnresolvedRelation `score`
```



# 1.ResolveRelations

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project ['student.id, (((100 + 80) + 'score.mathScore) + 'score.englishScore) AS total#32]
      +- 'Filter ('student.age < 35)
         +- 'Join Inner, ('student.id = 'score.studentId)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 2.ResolveReferences

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project ['student.id, (((100 + 80) + 'score.mathScore) + 'score.englishScore) AS total#32]
      +- 'Filter (age#12 < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 3.TypeCoercion.PromoteStrings

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project ['student.id, (((100 + 80) + 'score.mathScore) + 'score.englishScore) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv

```



# 4.ResolveReferences

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project [id#10, (((100 + 80) + mathScore#27) + englishScore#28) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 5.ResolveTimeZone

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project [id#10, (((100 + 80) + mathScore#27) + englishScore#28) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 6.TypeCoercion.PromoteStrings

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project [id#10, (((100 + 80) + cast(mathScore#27 as double)) + englishScore#28) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 7.TypeCoercion.ImplicitTypeCasts

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + englishScore#28) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 8.ResolveTimeZone

```
'Project ['avg('total) AS avgScore#33]
+- 'SubqueryAlias `tmp`
   +- 'Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + englishScore#28) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 9.TypeCoercion.PromoteStrings

```
'Project ['avg('total) AS avgScore#33]
+- SubqueryAlias `tmp`
   +- Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 10.ResolveReferences

```
'Project ['avg(total#32) AS avgScore#33]
+- SubqueryAlias `tmp`
   +- Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 11.ResolveFunctions

```
'Project [avg(total#32) AS avgScore#33]
+- SubqueryAlias `tmp`
   +- Project [id#10, ((cast((100 + 80) as double) + cast(mathScore#27 as double)) + cast(englishScore#28 as double)) AS total#32]
      +- Filter (cast(age#12 as int) < 35)
         +- Join Inner, (id#10 = studentId#26)
            :- SubqueryAlias `student`
            :  +- Relation[id#10,name#11,age#12] csv
            +- SubqueryAlias `score`
               +- Relation[studentId#26,mathScore#27,englishScore#28] csv
```



# 12.GlobalAggregates

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



# 13.ResolveTimeZone

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



# 14.CleanupAliases

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

