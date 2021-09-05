# **数据库表设计案例:**

有一份员工信息表如下所示。其中ssn是独一无二的值。

| ssn  | surname | address        | language                | comp_skills      | salary | department  |
| ---- | ------- | -------------- | ----------------------- | ---------------- | ------ | ----------- |
| 1    | kevin   | 22 St. Vincent | {Chinese,Greek,English} | {Java, database} | 52000  | Computer    |
| 2    | Bob     | 2 Hyndland Rd  | {Italian, English}      | {java, database} | 57000  | engineering |
| 3    | Tom     | 9 Keith Street | {English, Dutch}        | database         | 49000  | Computer    |

根据上述的表，我们发现该表设计不符合第一范式，因为出现多值情况。因此修改如下:

Employee:

| ssn  | surname | address        | language | Comp_skills | salary | department  |
| ---- | ------- | -------------- | -------- | ----------- | ------ | ----------- |
| 1    | kevin   | 22 St. Vincent | Chinese  | java        | 52000  | Computing   |
| 1    | kevin   | 22 St. Vincent | Greek    | java        | 52000  | Computing   |
| 1    | kevin   | 22 St. Vincent | English  | java        | 52000  | Computing   |
| 1    | kevin   | 22 St. Vincent | Chinese  | database    | 52000  | Computing   |
| 1    | kevin   | 22 St. Vincent | Greek    | database    | 52000  | Computing   |
| 1    | kevin   | 22 St. Vincent | English  | database    | 52000  | Computing   |
| 2    | Bob     | 2 Hyndland Rd  | Italian  | java        | 57000  | engineering |
| 2    | Bob     | 2 Hyndland Rd  | English  | java        | 57000  | engineering |
| 2    | Bob     | 2 Hyndland Rd  | Italian  | database    | 57000  | engineering |
| 2    | Bob     | 2 Hyndland Rd  | English  | database    | 57000  | engineering |
| 3    | Tom     | 9 Keith Street | English  | database    | 49000  | computing   |
| 3    | Tom     | 9 Keith Street | Dutch    | database    | 49000  | Computing   |

分析Employee表: 我们发现surname, address, salary 以及department依赖于ssn, 但是language, comp_skills不依赖于ssn，因为一个人可以说多种语言以及会多个技能。因此我们需要将Employee降解成第二范式。将{ssn,surname,address,salary,department}形成新的Employee表。其中ssn为主键。将{ssn,language,comp_skills}形成另一张Employee_skills表。其中{ssn,language,comp_skills}为联合主键。为了使两表建立联系，设置Employee_skills表中的ssn为外键和Employee表中的ssn(主键)建立联系。具体如下所示

Employee:

| <u>ssn</u> | surname | address        | salary | department  |
| ---------- | ------- | -------------- | ------ | ----------- |
| 1          | kevin   | 22 St. Vincent | 52000  | Computer    |
| 2          | Bob     | 2 Hyndland Rd  | 57000  | Engineering |
| 3          | Tom     | 9 Keith Street | 49000  | Computing   |

Employee_skills:

| <u>ssn</u> * | <u>language</u> | <u>Comp_skills</u> |
| ------------ | --------------- | ------------------ |
| 1            | Chinese         | java               |
| 1            | Greek           | java               |
| 1            | English         | java               |
| 1            | Chinese         | database           |
| 1            | Greek           | database           |
| 1            | English         | database           |
| 2            | Italian         | java               |
| 2            | English         | java               |
| 2            | Italian         | database           |
| 2            | English         | database           |
| 3            | English         | database           |
| 3            | Dutch           | Database           |

**分析Employee表:**

没有出现传递函数依赖，所有非主属性都直接依赖于主属性，因此Employee表属于第三范式。并且Employee表属于BCNF范式，因为都是由primay key 决定非主属性。

**分析Employee_skills:**

没有出现传递函数依赖，所有非主属性都直接依赖于主属性，因此Employee_skills表属于第三范式。并且Employee_skills表属于BCNF范式，因为都是由primay key 决定非主属性。

并且我们发现Employee_skills存在多值依赖的关系。即1个ssn可以对应多种语言，1个ssn可以对应多种技能。这种情况就会造成Employee_skills数据内容冗余。Employee_skills存在非平凡多值依赖，因此需要分解成第四范式。

解决方案:

emp_languages：其中{ssn,language}为主键，{ssn}为外键和Employee表中的ssn(主键)建立联系

| ssn  | Language |
| ---- | -------- |
| 1    | Chinese  |
| 1    | Greek    |
| 1    | English  |
| 2    | Italian  |
| 2    | English  |
| 3    | Engilish |
| 3    | Dutch    |

emp_comp_skills: {ssn,comp_skills}为主键，{ssn}为外键和Employee表中的ssn(主键)建立联系

| ssn  | comp_skills |
| ---- | ----------- |
| 1    | java        |
| 1    | database    |
| 2    | java        |
| 2    | database    |
| 3    | database    |

