# Challenge

## 1A. Oracle to Postgres dms task challenge
1. Make sure you have taken care of prerequisites for the RDS oracle to use as CDC source with Oracle LogMiner https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.Oracle.html
>hint: Source is RDS oracle and not an EC2 or On-prem Oracle instance, thus prerequisites will vary.
2. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
3. Open **Database migration tasks**.
4. Click on **Create task** from right top corner.
5. **Task Identifier**: *ORA2AUPGcdc*
6. **Replication instance** Select the replication instance.
7. **SourceDatabaseEndpoint** Select Oracle endpoint *oraclesource*.
8. **TargetDatabaseEndpoint** Select Aurora Postgres endpoint *aurorapgtarget*.
9. Set the Migration type to *Migrate existing data and replicate ongoing changes*
10. Under **Task settings** check the logging by selecting **Enable CloudWatch logs**.
11. Under **Table mappings** select table **NEWTABLE** from **MYUSER** schema.
12. Add a **transformation rule**
    1. to rename schema from MYUSER to public.
    2. convert the table NEWTABLE to lowercase.
    3. convert all column names (%) of table NEWTABLE to lowercase
13. Lastly **Create task**
14. Once the task is running in state **ongoing replication**, verify the Aurora Postgres Table newtable has required full load data:
```SQL
select * from newtable;
```
15. Run sample inserts/update statements to the source table
```SQL
INSERT INTO newtable (id,fname,lname,region) VALUES (3,'Arthur','Coleman','NORTH');
INSERT INTO newtable (id, fname,lname,region) VALUES (4, 'Nigel','Newman','SOUTH');
UPDATE newtable set region='SOUTH' where id=2;
```
16. Verify the data is getting migrating correctly.
17. If not, troubleshoot and fix the same.
18. Finally, do upload the task logs,source and target table (newtable) select outputs.
19. Zip above files and name it as per your full name.
Lastly upload it to https://tinyurl.com/y3xv4r6u


## 1B. SQLServer to Aurora Postgres dms task challenge

1. Make sure you have taken care of prerequisites for the EC2 SQLServer to use as CDC source with ongoing replication task https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.SQLServer.html

2.
Script to run on target Aurora Writer PSQL connection:
```SQL
CREATE TABLE finaltb(
microid BIGINT NOT NULL GENERATED ALWAYS AS IDENTITY( START WITH 1 INCREMENT BY 1),
description VARCHAR(255),
orderno BIGINT NOT NULL
)
        WITH (
        OIDS=FALSE
        );

CREATE TABLE ordertb(
orderid BIGINT NOT NULL GENERATED ALWAYS AS IDENTITY( START WITH 1 INCREMENT BY 1),
fname VARCHAR(255),
lname VARCHAR(255)
)
        WITH (
        OIDS=FALSE
        );

ALTER TABLE finaltb
ADD CONSTRAINT pk_finaltb_933578364 PRIMARY KEY (microid);

ALTER TABLE ordertb
ADD CONSTRAINT pk__ordertb__0809337dd08f972e PRIMARY KEY (orderid);

ALTER TABLE finaltb
ADD CONSTRAINT fk_finaltb_finaltb_965578478 FOREIGN KEY (orderno)
REFERENCES ordertb (orderid)
ON UPDATE NO ACTION
ON DELETE NO ACTION;
```
3. For source EC2 SQL Server rdp (remote desktop) using login **\administrator** and password **pAsswOrd12**
4. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
5. Open **Database migration tasks**.
6. Click on **Create task** from right top corner.
7. **Task Identifier**: *SQL2AUPGcdc*
8. **Replication instance** Select the replication instance.
9. **SourceDatabaseEndpoint** Select SQLserver endpoint *mssqlsource*.
10. **TargetDatabaseEndpoint** Select Aurora Postgres endpoint *aurorapgtarget*.
11. Set the Migration type to *Migrate existing data and replicate ongoing changes*
12. Under **Task settings** make sure that
    1. The **Target table preparation mode** is set to **Truncate**.
    2. Check the logging by selecting **Enable CloudWatch logs**.
13. Under **Table mappings** migrate table **finaltb** from **dbo** schema.
14. Add a **transformation rule**
    1. to rename schema from dbo to public.
15. Lastly **Create task**
16. Once the task is running in state **ongoing replication**, verify the Aurora Postgres Table newtable has required full load data:
```
select * from finaltb;
```
17. Verify the data is getting migrating correctly.
18. If not, troubleshoot and fix the same.
19. Finally, do upload the task logs,source and target table (finaltb) select outputs.
20. Zip above files and name it as per your full name.
Lastly upload it to  https://tinyurl.com/y3xv4r6u

### Once done, please *don't forget* to delete your stack from Cloudformation:
https://console.aws.amazon.com/cloudformation/home?region=us-east-1

Click **Delete Stack** 
