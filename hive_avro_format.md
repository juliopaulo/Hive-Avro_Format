#First step is import data frrom mysql database 

sqoop import --connect jdbc:mysql://localhost:3306/work_connection --username root --password cloudera  --table social_account --columns "id_social_account,creation_date,update_date,name,lastname,password,email" --fields-terminated-by '\t' --hive-import

#Second step - Create the avro schema 
{
    "namespace":"example.social_account",
    "type":"record",
    "name":"social_account",    
    "fields":[
        {"name":"id_social_account","type":"int"},
        {"name":"creation_date","type":"string"},
        {"name":"update_date","type":"string"},
        {"name":"name","type":"string"},
        {"name":"lastname","type":"string"},
        {"name":"password","type":"string"},
        {"name":"email","type":"string"}
    ]
}

#Third step - create the table avro format in HIVE

CREATE TABLE social_account_avro
    ROW FORMAT SERDE 
    ‘org.apache.hadoop.hive.serde2.avro.AvroSerDe’
    STORED as INPUTFORMAT
    ‘org.apache.hadoop.hive.ql.io.avro.AvroContainerInputFormat’
    OUTPUTFORMAT
    ‘org.apache.hadoop.hive.ql.io.avro.AvroContainerOutputFormat’
    TBLPROPERTIES (
    ‘avro.schema.url’=’file:///home/cloudera/social_account.avsc’
);

#Four step Insert datas in the new table 

INSERT OVERWRITE TABLE SOCIAL_ACCOUNT_AVRO SELECT * FROM SOCIAL_ACCOUNT;

#CREATE TABLE WITHOUT SPECIFY SCHEMA AVRO
CREATE TABLE SOCIAL_ACCOUNT_AVRO_2 (
    id_social_account INT,
creation_date STRING,
       update_date string,
       name string,
       lastname string,
       password string,
       email string
)
STORED AS AVRO;

INSERT OVERWRITE TABLE SOCIAL_ACCOUNT_AVRO SELECT * FROM SOCIAL_ACCOUNT;
