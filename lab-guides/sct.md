﻿[Back to main guide](../README.md)|[Next](dms-inst.md)

___

# AWS Schema Conversion Tool (SCT)

The following steps provide instructions for converting an Oracle database to an Amazon Aurora PostgreSQL database. In this activity, you perform the following tasks:
- Log in to the Windows EC2 instance (this has the AWS SCT installed)  
- Use SCT to create a database migration project
- Use SCT to convert the Oracle schema to a PostgreSQL schema and analyze schema conversion issues
- Apply the converted schema to the Aurora PostgreSQL database

    ![Connect to EC2 Instance](images/sct.png)
___

## Task 1 - Log in to Windows EC2 instance

1. Go to the [Amazon EC2 console](https://console.aws.amazon.com/ec2/v2/home) and click on Instances in the left column.
2. Select the instance with the name **OracleXE-SCT** and then click the **Connect** button.

    _Note : If you need instructions on connecting to a Windows instance, please see our [documentation](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html?icmpid=docs_ec2_console)._

![Connect to EC2 Instance](images/instance-connect.png)

3. Click on **Download Remote Desktop File** to download the RDP file to connect to the EC2 instance **OracleXE-SCT**. 
4. Connect to the EC2 instance using the following password 
   
    **User Name**: administrator           
    **Password** : GPSreInvent@321

___

## Task 2 - Launch the Schema Conversion Tool
Now that you are connected to the EC2 instance **OracleXE-SCT**, launch the Schema Conversion Tool from the shortcut on the desktop.

1. Launch SCT from the shortcut on the desktop and create a new Database Migration Project using the tool.
![Create New Project](images/new_project.png)
2. Enter the following values and click **OK**.

    Parameter | Value
    ----------|------
    Project Name | AWS Schema Conversion Tool Oracle to Aurora PostgreSQL
    Location | Leave default
    Database type | Transactional Database (OLTP)
    Source database engine | Oracle 
    Target database engine | Amazon Aurora(PostgreSQL compatible)

    ![Create New Project](images/new_project1.png)

3. Click **Connect to Oracle** on the top menu bar. Enter the source database details from the below table, and click **Test Connection**. Once the connection is successfully tested, click **Ok**.

    Parameter | Value
    ----------|------
    Type | SID
    Server Name | localhost
    Server Port | 1521
    Oracle SID | XE 
    User Name | hr
    Password |  hr123 
    Use SSL | Unchecked
    Store Password | Checked
    
    ![Connect to Oracle](images/sct_oracle.png)

4. Click **Connect to Amazon Aurora(PostgreSQL compatible)** on the top menu bar. Enter the target database details from the below table, and click **Test Connection**. Once the connection is successfully tested, click **Ok**.

    Parameter | Value
    ----------|------
    Server Name | Get `AuroraPostgreSQLEndpoint` from [CloudFormation stack output](./lab-setup-verification.md#cloudformation-stack-outputs)
    Server Port | 5432
    Database | AuroraPostgreSQLDB 
    User Name | postgres
    Password |  Aurora321 
    Use SSL | Unchecked
    Store Password | Checked
    
    ![Connect to Amazon Aurora](images/sct_aurora.png)

5. Select and expand the `HR` schema from the left-hand panel to inspect the schema objects.
    
    ![Schema Objects](images/schema_objects.png)
___

## Task 3 - Convert the Schema Using the Schema Conversion Tool
After creating a Database Migration Project, the next step is to convert the source Oracle schema to the target PostgreSQL schema.

1. Right-click on the `HR` schema from Oracle source and select **Convert Schema** to generate the data definition language (DDL) statements for the target database.

_Note: You may be prompted with a dialog box “These objects might already exist in the target database, Replace?” Select **Yes** and conversion will start._

![Convert Schema](images/convert_schema.png)

AWS SCT analyses the schema and creates a database migration assessment report for the conversion to PostgreSQL. Items with a red exclamation mark next to them cannot be directly translated from the source to the target. In this case, it includes the SECURE_DML and ADD_JOB_HISTORY procedures, and the EMP_DETAILS_VIEW view.

2. Click on the **View** button, and choose **Assessment Report view** to view the detailed assessment report.

![Assessment Report view](images/assessment.png)    

3. Review the assessment report and check if there are any suggested actions to address schema conversion issues. 

4. Next, navigate to the **Action Items** tab in the report to see the items that have issues while converting to the new PostgreSQL schema. 

![Action Items](images/action-items.png) 

5. Check each of the issues listed and compare the contents under the source Oracle panel and the target Aurora PostgreSQL panel. SCT has proposed resolutions by generating equivalent PostgreSQL DDL to convert the objects. Additionally, SCT highlights each conversion issue where it cannot automatically generate a conversion, and provide hints on how you can successfully convert the database object.

Notice the issue highlighted in the procedure `ADD_JOB_HISTORY`. You will see that SCT is unable to automatically convert the procedure. Ignore these issues for now. We will take a look at fixing these issues towards the end of the workshop in activity [Resolving SCT action items](https://github.com/aws-samples/amazon-aurora-database-migration-workshop-reinvent2019/blob/master/lab-guides/optional-resolv.md).  

6. To migrate the converted schema to the target database, right click on the `HR` schema in the right-hand panel, and select **Apply to database**.

![Apply to database](images/apply_db.png) 

7. When prompted if you want to apply the schema to the database, click **Yes**.
8. At this point, the schema has been applied to the target database. Expand the `HR` schema to see the tables.

    _Note: If you don’t see the tables, right click on `HR` schema and select **Refresh from Database**._

9. Save the SCT project by clicking on File-> Save project.

### Conclusion

This part of the workshop demonstrated how to convert and migrate the schema from an Oracle database to an Amazon Aurora PostgreSQL database. The AWS Schema Conversion Tool (SCT) automates the schema conversion to a large extent. These same steps can be followed to migrate SQL Server and Oracle workloads to other Amazon RDS engines including Aurora MySQL and MySQL.
___

[Back to main guide](../README.md)|[Next](dms-inst.md)