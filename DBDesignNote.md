# 数据库基本设计笔记

## 基本概念介绍:

在关系型数据库中，**关系**代表一张表，表示由多个行和多个列组成。表中的每行就是**元组**。每一列的头表示**属性**。如下表：

员工关系表，每一行代表一个元组。id, name, salary, department_id为属性。

**Relation**:Employee

| id   | name  | Salary | Department_id |
| ---- | ----- | ------ | ------------- |
| 100  | kevin | 5000   | 1             |
| 101  | Bob   | 5500   | 2             |
| 102  | Tom   | 5500   | 3             |

**SupkerKey(SK)超键:**在一张关系表中的一组属性当中，存在至少一个属性能够让每一个元组独一无二。例如在Employee(id, name,salary,Department_id)中，id可以是超键，因为id不会重复，那么id加上任何属性也都可以超键，因为能够使每个元组独一无二。但是如果是name，因为有概率不同的人会有相同的名字，因此name不能当做超键。同理 Salary 和Department_id也不能单独做超键。



**CandidateKey 候选键:** 最小的超键集合。也就是说给定一个超键集合K，如果从K集合中随便移除其中一个属性从而获得K', 如果K' 不再是超键，那么K就是候选键。

举例:

{id,name}\\{name} = {id}。 由于{id} 还是超键，因此{id,name} 不是候选键。

{id}\\{id} =空集。 由于空集不是超键，因此{id}就是候选键。

**PrimayKey主键**: 由于一张关系中可能会出现多个候选键。因此随机从候选键列表中选出一个就可以称之为主键。

注: **主键不可以为null**

## 数据库基本设计

一个良好的关系型数据库设计应该遵守以下几点:

1.在关系表中，属性应该和关系表有意义才行。不同的关系表如果想产生关联，应该使用主键+外键产生联系。

2.在关系表中不要出现重复的元组，浪费存储空间。并且如果关系表设计的不好，可能会产生删除，添加异常等。

| ssn  | surname | address        | language | comp_skills | salary | department |
| ---- | ------- | -------------- | -------- | ----------- | ------ | ---------- |
| 1    | kevin   | 30 St. Vincent | Dutch    | Programming | 51000  | Computing  |
| 1    | Kevin   | 30 St.Veincent | English  | Programming | 51000  | Computing  |
| 2    | Bob     | 2 Hyndland Rd  | English  | Programming | 51000  | Computing  |
| 2    | Bob     | 2 Hyndland Rd  | Greek    | Programming | 51000  | Computing  |

例如:当要求需改kevin的住址时，如果仅仅修改一个元组，那么会出现信息不对称的情况，我们需要更新2个元组，才能保持信息一致。

当要求删除kevin的programming信息时，会出现删除异常，因为删除是一行一行删除的，会把其他信息也一并删除了，这并不是我们所期望的。因此目前来看，该表在设计上存在问题。

3.关系表中尽可能少出现null值。

4.关系表在拆分有重组后不要出现虚拟元组。

**FunctionalDependency(FD) 函数依赖:** 在一张关系表中，如果X属性集能独一无二决定Y属性集，那么Y依赖于X。或者X->Y. 或者说，在给定一张关系表中，如果2个元组有相同的X属性集，他们的Y属性集也相同，那么x->Y.

if t1[x] = t2[x] then t1[y] = t2[y]

**在给定一张关系表中，如果K是候选键，那么k可以决定所有的属性。**

**如果 X->Y , Y->Z，那么X->Z  transitive rule.**

## 数据库范式

数据库范式核心就是把一张设计不好的表通过分解成多个子表。

**第一范式**

表中的每一列都是不可分割的，并且不能出现多值情况。

Department

| Dname    | Dnumber | DMGSSN | Dlocation                 |
| -------- | ------- | ------ | ------------------------- |
| Research | 100     | 123456 | {Glasgow,London,Edinburg} |

我们发现Dlocation列出现多值情况，因此Department表不符合第一范式，修改如下:

| Dname    | Dnumber | DMGSSN | Dlocation |
| -------- | ------- | ------ | --------- |
| Research | 100     | 123456 | Glasgow   |
| Research | 100     | 123456 | London    |
| Research | 100     | 123456 | Edinburg  |

**第二范式:**

非主属性(non-prime attributes) 完全依赖于主属性(prime attributes)

在EMP_PROJ(ssn,pnumber,hours, ename,pname,plocation)中，{ssn,pnumber}为主属性，我们发现 {ssn,pnumber}->{hours}属于完全依赖, {ssn}->{ename}, {pnumber}->{pname,plocation}属于部分依赖。因此EMP_PROJ不属于第二范式。

在第一范式的基础上，如果想升级为第二范式需要做一下步骤:

1.列出所有部分依赖。

2.将这些部分依赖从原表中抽取出来形成行的表。

EMP_PROJ

| ssn  | pnumber | hours | ename | pname | plocation |
| ---- | ------- | ----- | ----- | ----- | --------- |
|      |         |       |       |       |           |

将EMP_PROJ表拆分 

| ssn  | pnumber | Hours |
| ---- | ------- | ----- |
|      |         |       |



emp1

| ssn  | Name |
| ---- | ---- |
|      |      |



emp2

| Pnumber | pname | Plocation |
| ------- | ----- | --------- |
|         |       |           |

**第三范式**

**传递函数依赖(transive FD): 一张关系表中，X为主键，Z 和 Y为非主属性，如果X->Z, Z->Y,那么X->Y. 因此Y是通过Z过渡依赖于X。**

**那么再第三范式当中，不允许出现传递函数依赖。也就是说所有非主属性应当直接依赖于主属性。**

举例:

EMP_DEPT(ssn,ename,bdate,address,dnumber,dname,dmgr_ssn). 

出现了传递函数依赖: ssn->dnumber. Dnumber->dname,dmgr_ssn. 其中Dnumber属于non-prime transitive attribute. 因此不属于第三范式。属于第二范式。那么再第二范式基础上分解成第三范式的步骤:

1.找到non-prime transitive attribute

2.通过non-prime transitive attribute 将表分成2个表，并且在新表中，non-prime transitive attribute为主键。

**EMP_DEPT(ssn,ename,bdate,address,dnumber,dname,dmgr_ssn)**分解如下:

**ED1{ssn,ename,bdate,address,dnumber}**

**ED2(dnumber,dname,dmgr_ssn)**

在X->Z, Z->Y, 然后X->Y中，如果Z是候选键，那么该表就是第三范式。举例:

EMPLOYEE(ssn, passport_no,salary) 其中ssn为主键

虽然ssn->passport_no, passport_no -> salary, 所以 ssn->salary, 但是此表就是在第三范式，因为passport_no是候选键。

**BCNF范式:**

一张关系表是在BCNF范式，当且仅当X->Y, 其中x必须是主键。如果X不是主键，那么就违反了BCNF范式。

举例:

TEACH(student_name, course,instructor)其中{student_name, course} 是主键。那么我们可以得出{student_name} ->{instructor}. 但是{instructor}->{course} 违反了BCNF范式，因为instructor不是主键。

那么将分解成BCNF范式步骤如下:

假设给定一个关系R， X->A 违反了BCNF。

**1.将关系R中的A移除形成R1**

**2.将X∪A 形成R2**

如果R1 R2 还是违反了BCNF 那么重新做上述步骤。

**R1(student_name,instructor) 其中{student_name, instructor}为主键**

**R2(instructor,course) 其中{instructor}为主键**

