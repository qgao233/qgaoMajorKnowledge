# sql练习

```
-- 学生表  
  
create table Student(SId varchar(10),Sname varchar(10),Sage datetime,Ssex varchar(10));  
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');  
insert into Student values('02' , '钱电' , '1990-12-21' , '男');  
insert into Student values('03' , '孙风' , '1990-12-20' , '男');  
insert into Student values('04' , '李云' , '1990-12-06' , '男');  
insert into Student values('05' , '周梅' , '1991-12-01' , '女');  
insert into Student values('06' , '吴兰' , '1992-01-01' , '女');  
insert into Student values('07' , '郑竹' , '1989-01-01' , '女');  
insert into Student values('09' , '张三' , '2017-12-20' , '女');  
insert into Student values('10' , '李四' , '2017-12-25' , '女');  
insert into Student values('11' , '李四' , '2012-06-06' , '女');  
insert into Student values('12' , '赵六' , '2013-06-13' , '女');  
insert into Student values('13' , '孙七' , '2014-06-01' , '女');  
  
-- 科目表  
  
create table Course(CId varchar(10),Cname nvarchar(10),TId varchar(10));  
insert into Course values('01' , '语文' , '02');  
insert into Course values('02' , '数学' , '01');  
insert into Course values('03' , '英语' , '03');  
  
-- 教师表  
  
create table Teacher(TId varchar(10),Tname varchar(10));  
insert into Teacher values('01' , '张三');  
insert into Teacher values('02' , '李四');  
insert into Teacher values('03' , '王五');  
  
-- 成绩表  
  
create table SC(SId varchar(10),CId varchar(10),score decimal(18,1));  
insert into SC values('01' , '01' , 80);  
insert into SC values('01' , '02' , 90);  
insert into SC values('01' , '03' , 99);  
insert into SC values('02' , '01' , 70);  
insert into SC values('02' , '02' , 60);  
insert into SC values('02' , '03' , 80);  
insert into SC values('03' , '01' , 80);  
insert into SC values('03' , '02' , 80);  
insert into SC values('03' , '03' , 80);  
insert into SC values('04' , '01' , 50);  
insert into SC values('04' , '02' , 30);  
insert into SC values('04' , '03' , 20);  
insert into SC values('05' , '01' , 76);  
insert into SC values('05' , '02' , 87);  
insert into SC values('06' , '01' , 31);  
insert into SC values('06' , '03' , 34);  
insert into SC values('07' , '02' , 89);  
insert into SC values('07' , '03' , 98);  
  
  
-- 1.   查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数  
  
/* select * from student s right JOIN  
(select t1.SId, class1, class2 from   
(select SId, score as class1 from sc where CId = '01') as t1,  
(select SId, score as class2 from sc where CId = '02') as t2  
where t1.SId = t2.SId and t1.class1>t2.class2) t  
on s.SId=t.SId */  
  
-- 1.1  查询同时存在" 01 "课程和" 02 "课程的情况  
  
/* select t1.SId, class1, class2 from   
(select SId, score as class1 from sc where CId = '01') as t1,  
(select SId, score as class2 from sc where CId = '02') as t2  
where t1.SId = t2.SId  */  
  
-- 1.2  查询存在" 01 "课程但可能不存在" 02 "课程的情况(不存在时显示为 null )  
  
/* select t1.SId, class1, class2 from   
(select SId, score as class1 from sc where CId = '01') as t1 left JOIN  
(select SId, score as class2 from sc where CId = '02') as t2  
on t1.SId = t2.SId  */  
  
-- 1.3  查询不存在" 01 "课程但存在" 02 "课程的情况  
  
/* select * from sc  
where sc.SId not in (  
    select SId from sc   
    where sc.CId = '01'  
)   
AND sc.CId= '02'; */  
  
-- 2.   查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩  
  
/* select s.SId,Sname,t.avgscore from student s INNER JOIN  
(select SId,avg(score) as avgscore from sc GROUP BY SId HAVING AVG(score)>= 60) t  
on s.SId=t.SId */  
  
-- 3.   查询在 SC 表存在成绩的学生信息  
  
/* select distinct s.* from student s , sc t  
where s.SId=t.SId */  
  
-- 4.   查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )  
  
/* select s.SId, Sname, counts, sums from student s LEFT JOIN  
(select SId, count(score) as counts, sum(score) as sums from sc GROUP BY SId) t  
on s.SId=t.SId */  
  
-- 4.1  查有成绩的学生信息  
  
/* select * from student s where exists(SELECT 1 from sc where s.SId=sc.SId) */  
  
-- 5.   查询「李」姓老师的数量  
  
/* select count(TId) from teacher where Tname LIKE '李%' */  
  
-- 6.   查询学过「张三」老师授课的同学的信息  
  
/* select student.* from student,teacher,course,sc  
where   
    student.sid = sc.sid   
    and course.cid=sc.cid   
    and course.tid = teacher.tid   
    and tname = '张三'; */  
  
-- 7.   查询没有学全所有课程的同学的信息  
  
/* SELECT s.* from student s where s.SId not in  
(select SId from sc GROUP BY SId   
HAVING count(CId)=(select count(CId) from course)) */  
  
-- 8.   查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息  
  
/* select s.* from student s,  
(select DISTINCT SId from sc where CId in (select CId from sc where SId = '01') and SId != '01') t  
where s.SId=t.SId */  
  
-- 9.   查询和" 01 "号的同学学习的课程 完全相同的其他同学的信息  
  
/* select s.* from student s,  
(select SId from sc GROUP BY SId having GROUP_CONCAT(CId ORDER BY CId)=(select GROUP_CONCAT(CId ORDER BY CId) from sc where SId = '01' ) and SId != '01') t  
where s.SId=t.SId */  
  
-- 10.  查询没学过"张三"老师讲授的任一门课程的学生姓名  
  
/* select * from student   
where SId not in  
(select SId from sc   
where CId in  
(select CId from course,teacher  
where course.TId=teacher.TId and Tname='张三')) */  
  
-- 11.  查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩  
  
/* select t.SId,Sname,avgs from student,(  
select SId,avg(score) as avgs from sc where score<60 GROUP BY SId having count(CId)>=2) t  
where student.SId=t.SId */  
  
-- 12.  检索" 01 "课程分数小于 60，按分数降序排列的学生信息  
  
select student.* from student,(  
select SId from sc where CId='01' and score<60 ORDER BY score DESC) t  
where student.SId=t.SId  
  
-- 13.  按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩  
/*   
select *  from sc   
left join (  
    select sid,avg(score) as avscore from sc   
    group by sid  
    )r   
on sc.sid = r.sid  
order by avscore desc; */  
  
-- 14.  查询各科成绩最高分、最低分和平均分：  
  
-- 以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率  
  
-- 及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90  
  
-- 要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列  
  
/* select sc.CId,count(SId) as counts,Cname,max(score),min(score),avg(score),  
sum(case when score >= 60 then 1 else 0 end)/count(SId) '及格率',  
sum(case when score >= 70 and score <= 80 then 1 else 0 end)/count(SId) '中等率',  
sum(case when score >= 80 and score <= 90 then 1 else 0 end)/count(SId) '优良率',  
sum(case when score >= 90 then 1 else 0 end)/count(SId) '优秀率'  
 from sc   
RIGHT JOIN course c on c.CId=sc.CId  
group by sc.CId order by counts desc, sc.CId asc */  
  
-- 15.  按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺  
  
/* select a.CId,a.SId,a.score,count(b.score)+1 as rank from sc a left JOIN sc b  
on a.CId = b.CId and a.score<b.score  
GROUP BY a.CId, a.SId  
ORDER BY a.CId, rank asc */  
  
-- 15.1 按各科成绩进行排序，并显示排名， Score 重复时合并名次  
  
/* 有问题  
select a.CId,GROUP_CONCAT(DISTINCT a.SId ),a.score,count(b.score)+1 as rank from sc a left JOIN sc b  
on a.CId = b.CId and a.score<b.score  
GROUP BY a.CId,a.score  
ORDER BY a.CId, a.score desc */  
  
-- 16.  查询学生的总成绩，并进行排名，总分重复时保留名次空缺【不会】  
-- 16.1 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺  
  
/* set @crank=0;  
select q.sid, total, @crank := @crank +1 as rank from(  
select sc.sid, sum(sc.score) as total from sc  
group by sc.sid  
order by total desc)q; */  
  
-- 17.  统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比  
  
/* select sc.CId, Cname,  
sum(case when score < 60 then 1 else 0 end) '[60-0]',  
sum(case when score < 60 then 1 else 0 end)/count(score) '[60-0]%',  
sum(case when score >=60 and score < 70 then 1 else 0 end) '[70-60]',  
sum(case when score >=60 and score < 70 then 1 else 0 end)/count(score) '[70-60]%',  
sum(case when score >=70 and score < 85 then 1 else 0 end) '[85-70]',  
sum(case when score >=70 and score < 85 then 1 else 0 end)/count(score) '[85-70]%',  
sum(case when score >=85 and score <= 100 then 1 else 0 end) '[100-85]',  
sum(case when score >=85 and score <= 100 then 1 else 0 end)/count(score) '[100-85]%'  
from sc,course  
where sc.CId=course.CId  
GROUP BY sc.CId */  
  
-- 18.  查询各科成绩前三名的记录  
  
/* 有问题  
select a.CId,a.SId, a.score score1 from sc a  
left join sc b  
on a.CId=b.CId and a.score < b.score  
GROUP BY a.CId, a.SId  
having count(b.score) < 3  
ORDER BY a.CId, score1 desc */  
  
-- 19.  查询每门课程被选修的学生数  
  
/* select CId, count(SId) '学生数' from sc group by CId */  
  
-- 20.  查询出只选修两门课程的学生学号和姓名  
  
/* select s.SId, Sname from student s right JOIN (  
select SId from sc group by SId having count(CId) = 2) t  
on s.SId=t.SId */  
  
-- 21.  查询男生、女生人数  
  
/* select ssex, count(*) from student  
group by ssex; */  
  
-- 22.  查询名字中含有「风」字的学生信息  
  
/* select *  
from student   
where student.Sname like '%风%' */  
  
-- 23.  查询同名同性学生名单，并统计同名人数  
  
/* select sname, count(*),ssex from student  
group by sname,Ssex  
having count(*)>1; */  
  
-- 24.  查询 1990 年出生的学生名单  
  
/* select *  
from student  
where YEAR(student.Sage)=1990; */  
  
-- 25.  查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列  
  
/* select sc.cid, course.cname, AVG(SC.SCORE) as average from sc, course  
where sc.cid = course.cid  
group by sc.cid   
order by average desc,cid asc; */  
  
-- 26.  查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩  
  
/* select student.sid, student.sname, AVG(sc.score) as aver from student, sc  
where student.sid = sc.sid  
group by sc.sid  
having aver > 85; */  
  
-- 27.  查询课程名称为「数学」，且分数低于 60 的学生姓名和分数  
  
/* select student.sname, sc.score from student, sc, course  
where student.sid = sc.sid  
and course.cid = sc.cid  
and course.cname = "数学"  
and sc.score < 60; */  
  
-- 28.  查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）  
  
/* select student.sname, cid, score from student  
left join sc  
on student.sid = sc.sid; */  
  
-- 29.  查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数  
  
/* select s.sid,c.cname,t.score from student s, (  
select distinct SId,CId,score from sc where score >= 70) t, course c  
where s.SId = t.SId and c.CId=t.cid */  
  
-- 30.  查询不及格的课程  
  
/* select DISTINCT sc.CId  
from sc  
where sc.score <60; */  
  
-- 31.  查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名  
  
/* select student.sid,student.sname   
from student,sc  
where cid="01"  
and score>=80  
and student.sid = sc.sid; */  
  
-- 32.  求每门课程的学生人数  
  
/* select sc.CId,count(*) as 学生人数  
from sc  
GROUP BY sc.CId; */  
  
-- 33.  成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩  
  
/* select student.*, sc.score, sc.cid from student, teacher, course,sc   
where teacher.tid = course.tid  
and sc.sid = student.sid  
and sc.cid = course.cid  
and teacher.tname = "张三"  
order by score desc  
limit 1; */  
  
-- 34.  成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩  
  
/* UPDATE sc SET score=90  
where sid = "07"  
and cid ="02";  
  
select student.*, sc.score, sc.cid from student, teacher, course,sc   
where teacher.tid = course.tid  
and sc.sid = student.sid  
and sc.cid = course.cid  
and teacher.tname = "张三"  
and sc.score = (  
    select Max(sc.score)   
    from sc,student, teacher, course  
    where teacher.tid = course.tid  
    and sc.sid = student.sid  
    and sc.cid = course.cid  
    and teacher.tname = "张三"  
); */  
  
-- 35.  查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩  
  
/* select  a.cid, a.sid,  a.score from sc as a  
inner join   
sc as b  
on a.sid = b.sid  
and a.cid != b.cid  
and a.score = b.score  
group by cid */  
  
-- 36.  查询每门课程成绩最好的前两名  
  
/* select a.sid,a.cid,a.score from sc as a   
left join sc as b   
on a.cid = b.cid and a.score<b.score  
group by a.cid, a.sid  
having count(b.cid)<2  
order by a.cid; */  
  
-- 37.  统计每门课程的学生选修人数（超过 5 人的课程才统计）。  
  
/* select sc.cid, count(sid) as cc from sc  
group by cid  
having cc >5; */  
  
-- 38.  检索至少选修两门课程的学生学号  
  
/* select sid, count(cid) as cc from sc  
group by sid  
having cc>=2; */  
  
-- 39.  查询选修了全部课程的学生信息  
  
/* select student.*  
from sc ,student   
where sc.SId=student.SId  
GROUP BY sc.SId  
HAVING count(*) = (select DISTINCT count(*) from course ) */  
  
-- 40.  查询各学生的年龄，只按年份来算【看第41题】  
  
-- 41.  按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一  
  
/* select student.SId as 学生编号,student.Sname  as  学生姓名,  
TIMESTAMPDIFF(YEAR,student.Sage,CURDATE()) as 学生年龄  
from student */  
  
-- 42.  查询本周过生日的学生  
  
/* select *  
from student   
where WEEKOFYEAR(student.Sage)=WEEKOFYEAR(CURDATE()); */  
  
-- 43.  查询下周过生日的学生  
  
/* select *  
from student   
where WEEKOFYEAR(student.Sage)=WEEKOFYEAR(CURDATE())+1; */  
  
-- 44.  查询本月过生日的学生  
  
/* select *  
from student   
where MONTH(student.Sage)=MONTH(CURDATE()); */  
  
-- 45.  查询下月过生日的学生  
  
/* select *  
from student   
where MONTH(student.Sage)=MONTH(CURDATE())+1; */ 

```