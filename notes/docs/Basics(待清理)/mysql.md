service mysql start

net start mysql

drop database if exists "School";

create database "School";

use "School";

drop table if exist "tb_class";

create table "tb_class"
(
"id" int(11) not null AUTO_INCREMENT primary key,
"NAME" varchar(32) not null
);

drop table if exists tb_student;

create table "tb_student"
(
"id" int(11) not null auto_increment primary key,
"name" varchar(32) not null,
"age" int default 0,check("age">0 or "age"<1=00),
"gender" boolean default 0,check("gender"=0 or "gender"=1),
"date" datetime default now()
);

create table "tb_score"
(
"id" int(11) not mull auto_increament primary key,
"course" varchar(32) not null,
"score" float(3,1) not null,
"stuid" int(11) not null,
constraint "FK_stuid" foreign key("stuid") references "tb_student"("id")
);


show databases;
use school;
desc tb_student;

alter table tb_student change "date" "createdate" datetime;
alter table tb_student add "phone"  varchar(15) after "name";
alter table tb_student add classId int(11) not null;
alter table tb_student add constrint "FK_class_student" foreign key("classId") references tb_class("id");


insert into tb_name(id,name,score)values("null","7c","140"),("null","7c","140")

insert into tb_name(name,score)select name,score from tb_name2;

update tb_name set score=189 where id=2;
update tablename set columnname=newvalue[where condition]

delete from tb_name where id=3;

select * from tb_name where id=3;
select * from tb_name group by score having count(*)>2

select * from tab_name where name regexp "^[a-d]"


