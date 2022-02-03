## 初期データ

本書のSQLを実行するには、以下のテーブルを作成しデータを入れておく必要があります。

### テーブルの作成

deptテーブルを作成します。

```SQL
CREATE TABLE dept (
  deptno INT,
  dname VARCHAR(14),
  loc VARCHAR(13));

```

empテーブルを作成します。

```SQL
CREATE TABLE emp (
  empno INT,
  ename VARCHAR(10),
  job VARCHAR(9),
  mgr INT,
  hiredate DATE,
  sal DECIMAL(7,2),
  comm DECIMAL(7,2),
  deptno INT);
```

### データの投入

#### deptテーブルへのデータ投入


deptテーブルにデータを投入します。

```SQL
INSERT INTO dept (deptno, dname, loc) VALUES (10, 'ACCOUNTING', 'NEW YORK');
INSERT INTO dept (deptno, dname, loc) VALUES (20, 'RESEARCH', 'DALLAS');
INSERT INTO dept (deptno, dname, loc) VALUES (30, 'SALES', 'CHICAGO');
INSERT INTO dept (deptno, dname, loc) VALUES (40, 'OPERATIONS', 'BOSTON');
```

#### empテーブルへのデータ投入

empテーブルにデータを投入します。

#### SQL Server, Oracle, PostgreSQL

```SQL
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7369, 'SMITH' , 'CLERK'    , 7902, '17-DEC-2005', 800,  NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7499, 'ALLEN' , 'SALESMAN' , 7698, '20-FEB-2006', 1600, 300 , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7521, 'WARD'  , 'SALESMAN' , 7698, '22-FEB-2006', 1250, 500 , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7566, 'JONES' , 'MANAGER'  , 7839, '02-APR-2006', 2975, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7654, 'MARTIN', 'SALESMAN' , 7698, '28-SEP-2006', 1250, 1400, 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7698, 'BLAKE' , 'MANAGER'  , 7839, '01-MAY-2006', 2850, NULL, NULL);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7782, 'CLARK' , 'MANAGER'  , 7839, '09-JUN-2006', 2450, NULL, 10);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7788, 'SCOTT' , 'ANALYST'  , 7566, '09-DEC-2007', 3000, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7839, 'KING'  , 'PRESIDENT', NULL, '17-NOV-2006', 5000, NULL, 10);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7844, 'TURNER', 'SALESMAN' , 7698, '08-SEP-2006', 1500, 0   , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7876, 'ADAMS' , 'CLERK'    , 7788, '12-JAN-2008', 1100, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7900, 'JAMES' , 'CLERK'    , 7698, '03-DEC-2006',  950, NULL, 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7902, 'FORD'  , 'ANALYST'  , 7566, '03-DEC-2006', 3000, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7934, 'MILLER', 'CLERK'    , 7782, '23-JAN-2007', 1300, NULL, 10);
```

#### MySQL

MySQLはdate型のカラムにデータを入れる時に行われる暗黙的型変換のフォー
マットが異なり、前述のSQLをそのまま実行できません。日付のフォーマット
を2005-12-17のような形式にするか、STR_TO_DATE関数を用いてフォーマット
を指定して読み取るなどの処理をする必要があります。今回は前述のSQLをな
るべくそのまま用いたいので、STR_TO_DATE()関数を用いて日付のデータを変
換する方法を用います。

```SQL
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7369, 'SMITH' , 'CLERK'    , 7902, STR_TO_DATE('17-DEC-2005', '%d-%b-%Y'), 800,  NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7499, 'ALLEN' , 'SALESMAN' , 7698, STR_TO_DATE('20-FEB-2006', '%d-%b-%Y'), 1600, 300 , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7521, 'WARD'  , 'SALESMAN' , 7698, STR_TO_DATE('22-FEB-2006', '%d-%b-%Y'), 1250, 500 , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7566, 'JONES' , 'MANAGER'  , 7839, STR_TO_DATE('02-APR-2006', '%d-%b-%Y'), 2975, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7654, 'MARTIN', 'SALESMAN' , 7698, STR_TO_DATE('28-SEP-2006', '%d-%b-%Y'), 1250, 1400, 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7698, 'BLAKE' , 'MANAGER'  , 7839, STR_TO_DATE('01-MAY-2006', '%d-%b-%Y'), 2850, NULL, NULL);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7782, 'CLARK' , 'MANAGER'  , 7839, STR_TO_DATE('09-JUN-2006', '%d-%b-%Y'), 2450, NULL, 10);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7788, 'SCOTT' , 'ANALYST'  , 7566, STR_TO_DATE('09-DEC-2007', '%d-%b-%Y'), 3000, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7839, 'KING'  , 'PRESIDENT', NULL, STR_TO_DATE('17-NOV-2006', '%d-%b-%Y'), 5000, NULL, 10);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7844, 'TURNER', 'SALESMAN' , 7698, STR_TO_DATE('08-SEP-2006', '%d-%b-%Y'), 1500, 0   , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7876, 'ADAMS' , 'CLERK'    , 7788, STR_TO_DATE('12-JAN-2008', '%d-%b-%Y'), 1100, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7900, 'JAMES' , 'CLERK'    , 7698, STR_TO_DATE('03-DEC-2006', '%d-%b-%Y'),  950, NULL, 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7902, 'FORD'  , 'ANALYST'  , 7566, STR_TO_DATE('03-DEC-2006', '%d-%b-%Y'), 3000, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7934, 'MILLER', 'CLERK'    , 7782, STR_TO_DATE('23-JAN-2007', '%d-%b-%Y'), 1300, NULL, 10);
```
```

#### DB2

DB2もMySQL同様、前述のSQLをそのまま実行できません。日付のフォーマット
の文字列をTO_DATEを用いて変換します。

```SQL
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7369, 'SMITH' , 'CLERK'    , 7902, TO_DATE('17-DEC-2005', 'DD-MON-YYYY'), 800,  NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7499, 'ALLEN' , 'SALESMAN' , 7698, TO_DATE('20-FEB-2006', 'DD-MON-YYYY'), 1600, 300 , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7521, 'WARD'  , 'SALESMAN' , 7698, TO_DATE('22-FEB-2006', 'DD-MON-YYYY'), 1250, 500 , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7566, 'JONES' , 'MANAGER'  , 7839, TO_DATE('02-APR-2006', 'DD-MON-YYYY'), 2975, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7654, 'MARTIN', 'SALESMAN' , 7698, TO_DATE('28-SEP-2006', 'DD-MON-YYYY'), 1250, 1400, 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7698, 'BLAKE' , 'MANAGER'  , 7839, TO_DATE('01-MAY-2006', 'DD-MON-YYYY'), 2850, NULL, NULL);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7782, 'CLARK' , 'MANAGER'  , 7839, TO_DATE('09-JUN-2006', 'DD-MON-YYYY'), 2450, NULL, 10);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7788, 'SCOTT' , 'ANALYST'  , 7566, TO_DATE('09-DEC-2007', 'DD-MON-YYYY'), 3000, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7839, 'KING'  , 'PRESIDENT', NULL, TO_DATE('17-NOV-2006', 'DD-MON-YYYY'), 5000, NULL, 10);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7844, 'TURNER', 'SALESMAN' , 7698, TO_DATE('08-SEP-2006', 'DD-MON-YYYY'), 1500, 0   , 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7876, 'ADAMS' , 'CLERK'    , 7788, TO_DATE('12-JAN-2008', 'DD-MON-YYYY'), 1100, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7900, 'JAMES' , 'CLERK'    , 7698, TO_DATE('03-DEC-2006', 'DD-MON-YYYY'),  950, NULL, 30);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7902, 'FORD'  , 'ANALYST'  , 7566, TO_DATE('03-DEC-2006', 'DD-MON-YYYY'), 3000, NULL, 20);
INSERT INTO emp (empno, ename, job, mgr, hiredate, sal, comm, deptno) VALUES (7934, 'MILLER', 'CLERK'    , 7782, TO_DATE('23-JAN-2007', 'DD-MON-YYYY'), 1300, NULL, 10);
```

### 各章で用いられているデータ

#### ３章

##### emp_bonus

従業員のボーナスに関わるテーブルを用いています。

```
CREATE TABLE emp_bonus (empno INT, received DATE, type INT);
```


以下のデータが投入されてます。

###### SQL Server, Oracle, PostgreSQL

```
INSERT INTO emp_bonus(empno, received, type) VALUES (7369, '14-MAR-2005', 1);
INSERT INTO emp_bonus(empno, received, type) VALUES (7900, '14-MAR-2005', 2);
INSERT INTO emp_bonus(empno, received, type) VALUES (7788, '14-MAR-2005', 3);
```

###### MySQL

```
INSERT INTO emp_bonus(empno, received, type) VALUES (7369, STR_TO_DATE('14-MAR-2005', '%d-%b-%Y'), 1);
INSERT INTO emp_bonus(empno, received, type) VALUES (7900, STR_TO_DATE('14-MAR-2005', '%d-%b-%Y'), 2);
INSERT INTO emp_bonus(empno, received, type) VALUES (7788, STR_TO_DATE('14-MAR-2005', '%d-%b-%Y'), 3);
```

###### DB2

```
INSERT INTO emp_bonus(empno, received, type) VALUES (7369, TO_DATE('14-MAR-2005', 'DD-MON-YYYY'), 1);
INSERT INTO emp_bonus(empno, received, type) VALUES (7900, TO_DATE('14-MAR-2005', 'DD-MON-YYYY'), 2);
INSERT INTO emp_bonus(empno, received, type) VALUES (7788, TO_DATE('14-MAR-2005', 'DD-MON-YYYY'), 3);
```

## 正誤表

本書の正誤情報は現在準備中です。

誤植や間違いなどを見つけた方は、[:email: japan@oreilly.co.jp](<mailto:japan@oreilly.co.jp>)までお知らせください。
