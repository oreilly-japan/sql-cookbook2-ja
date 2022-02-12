---
layout: home
---

## <a href="https://www.oreilly.co.jp/books/9784873119779/">SQLクックブック 第2版<br />― データベースエキスパート、データサイエンティストのための実践レシピ集</a>

このサイトはSQLクックブック 第2版のサポートページです。

<img src="./picture_large978-4-87311-977-9.jpeg" width="500px">

本書のSQLを実行するには、以下のテーブルを作成しデータを入れておく必要があります。

### 環境の作成

本書ではデータベースを扱っていますが、書籍内ではデータベースサーバーの構築方法は解説していません。
各自必要な環境を作成すればよいですが、ここでは一例としてDockerを用いてlocalでの環境の構築方法について解説します。
なおDocker自体は既にインストールされていることを前提とします。
また検証用にローカルで起動することを前提とするため使用するパスワードなどが脆弱な状態になっています。
決して外部への公開をしないようにご注意下さい。

#### MySQL

Docker ImageをPullします。

```
docker pull mysql:8.0.23
```

コンテナを起動します。

```
docker run -it --rm \
       --privileged=true \
       --publish="127.0.0.1:3306:3306" \
       --expose="3306" \
       --volume "mysql-server-8-data:/var/lib/mysql" \
       --name="mysqld" \
       --env 'MYSQL_ALLOW_EMPTY_PASSWORD=1' \
       mysql:8.0.23
```

別のターミナルエミュレーターを開きコンテナ内でデータベースに接続します。

```
docker exec -it mysqld mysql
```

#### PostgreSQL

Docker ImageをPullします。

```
docker pull postgres:12-alpine
```

コンテナを起動します。

```
docker run -it --rm \
       --privileged=true \
       --publish="127.0.0.1:5432:5432" \
       --expose="5432" \
       --volume "postgres-12-data:/var/lib/postgresql/data" \
       --name="postgres" \
       --env='POSTGRES_PASSWORD=postgres' \
       postgres:12-alpine
```

別のターミナルエミュレーターを開きコンテナ内でデータベースに接続します。

```
docker exec -it postgres psql -U postgres
```

#### Microsoft SQL Server

Docker ImageをPullします。

```
docker pull mcr.microsoft.com/mssql/server:2017-latest
```

コンテナを起動します。

```
docker run -it --rm \
       --privileged=true \
       --publish="127.0.0.1:1443:1443" \
       --expose="1443" \
       --name="mssql" \
       --env='ACCEPT_EULA=Y' \
       --env='SA_PASSWORD=Testing1234!' \
       mcr.microsoft.com/mssql/server:2017-latest
```

別のターミナルエミュレーターを開きコンテナ内でデータベースに接続します。

```
docker exec -ti mssql bash -c '/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "Testing1234!"'
```

#### Db2

Docker ImageをPullします。

```
docker pull ibmcom/db2/11.5.0.0
```

コンテナを起動します。

```
docker run -it --rm \
       --privileged=true \
       --publish="127.0.0.1:50000:50000" \
       --env='DB2INST1_PASSWORD=Testing1234!' \
       --env='LICENSE=accept' \
       --env='SAMPLEDB=true' \
       --volume="db2-data:/database" \
       --name='db2-server' \
       'ibmcom/db2:11.5.5.0'

```

別のターミナルエミュレーターを開きコンテナ内でデータベースに接続します。

```
docker exec -ti db2-server bash -c "su - db2inst1 -c 'db2 -t'"
```

#### Oracle Database

Oracle DatabaseについてはDocker Imaggが提供されていません。
しかしソースコードとDocker Imageを作成するためのDockerfileやビルドスクリプトは提供されています。今回はそれらを用いてOracle DatabaseのDocker Imageをビルドします。


ソースコードはOracleからダウンロードします。

[[https://www.oracle.com/jp/database/technologies/oracle-database-software-downloads.html#19c]]

ダウンロードする際、Oracleのアカウントが必要なので適宜作ります。


Oracle DatabaseのDocker関連のファイルはGithubでホスティングされています。
Gitコマンドを使って取得します。

```
git clone https://github.com/oracle/docker-images oracle-docker-images
```

ダウンロードしたソースコードのzipファイルを =oracle-docker-images/OracleDatabase/SingleInstance/dockerfiles/19.3.0= 配下に配置します。

```
mv LINUX.X64_193000_db_home.zip oracle-docker-images/OracleDatabase/SingleInstance/dockerfiles/19.3.0
```

`oracle-docker-images/OracleDatabase/SingleInstance/dockerfiles/` にカレントディレクトリを移動して `buildContainerImage.sh` を用いてイメージをビルドします。

```
cd oracle-docker-images/OracleDatabase/SingleInstance/dockerfiles
./buildContainerImage.sh -v 19.3.0 -e -i
```

ビルドには時間がかかります。

ビルドが完了すると6.53GBの大きなDocker Imageができます。

コンテナを起動します。

```
docker run -it --rm \
     --privileged=true \
     --publish="127.0.0.1:1521:1521" \
     --publish="127.0.0.1:5500:5500" \
     --expose="1521" \
     --expose="5500" \
     --name="oracle" \
     --env="ORACLE_PWD=testing1234" \
     oracle/database:19.3.0-ee

```

初回起動には時間がかかります。

別のターミナルエミュレーターを開きコンテナ内でデータベースに接続します。

```
docker exec -ti oracle bash -c "sqlplus sys/testing1234@localhost:1521/ORCLCDB as sysdba"
```

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

MySQLはdate型のカラムにデータを入れる時に行われる暗黙的型変換のフォーマットが異なり、
前述のSQLをそのまま実行できません。
日付のフォーマットを2005-12-17のような形式にするか、
STR_TO_DATE関数を用いてフォーマットを指定して読み取るなどの処理をする必要があります。今回は前述のSQLをなるべくそのまま用いたいので、
STR_TO_DATE()関数を用いて日付のデータを変換する方法を用います。

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

#### DB2

DB2もMySQL同様、前述のSQLをそのまま実行できません。日付のフォーマットの文字列をTO_DATEを用いて変換します。

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


以下のデータが投入されています。

###### SQL Server, Oracle, PostgreSQL

```
INSERT INTO emp_bonus(empno, received, type) VALUES (7369, '14-MAR-2015', 1);
INSERT INTO emp_bonus(empno, received, type) VALUES (7900, '14-MAR-2015', 2);
INSERT INTO emp_bonus(empno, received, type) VALUES (7788, '14-MAR-2015', 3);
```

###### MySQL

```
INSERT INTO emp_bonus(empno, received, type) VALUES (7369, STR_TO_DATE('14-MAR-2015', '%d-%b-%Y'), 1);
INSERT INTO emp_bonus(empno, received, type) VALUES (7900, STR_TO_DATE('14-MAR-2015', '%d-%b-%Y'), 2);
INSERT INTO emp_bonus(empno, received, type) VALUES (7788, STR_TO_DATE('14-MAR-2015', '%d-%b-%Y'), 3);
```

###### DB2

```
INSERT INTO emp_bonus(empno, received, type) VALUES (7369, TO_DATE('14-MAR-2015', 'DD-MON-YYYY'), 1);
INSERT INTO emp_bonus(empno, received, type) VALUES (7900, TO_DATE('14-MAR-2015', 'DD-MON-YYYY'), 2);
INSERT INTO emp_bonus(empno, received, type) VALUES (7788, TO_DATE('14-MAR-2015', 'DD-MON-YYYY'), 3);
```

## 正誤表

本書の正誤情報は[こちら](https://www.oreilly.co.jp/books/9784873119779/#errata0)です。

誤植や間違いなどを見つけた方は、[japan@oreilly.co.jp](<mailto:japan@oreilly.co.jp>)までお知らせください。
