# project url
https://www.pulumi.com/blog/redshift-etl-with-pulumi-and-aws-glue/



# to see the aws IAM that is logged in
aws sts get-caller-identity
# create a new project

mkdir my-data-warehouse && cd my-data-warehouse
pulumi new aws-python
From here you will provide your project name, stack and aws region

# configure your stacks
pulumi config set clusterIdentifier my-redshift-cluster
 pulumi config set pulumi_aws:clusterIdentifier my-redshift-cluster
pulumi config set clusterNodeType ra3.xlplus
pulumi config set clusterDBName dev
pulumi config set clusterDBUsername admin
pulumi config set clusterDBPassword StrongPass1 --secret


# Create a text file
echo '{"id": 1, "name": "An interesting event"}
{"id": 2, "name": "Another interesting event"}
{"id": 3, "name": "An event of monumental importance"}' > events-1.txt


#upload the txt file to s3 using pulumi
aws s3 cp events-1.txt "s3://$(pulumi stack output dataBucketName)"


# SQL code: This is to create table
CREATE TABLE events (
id INT,
name VARCHAR(125)
);
# SQL code This is to copy from s3 to redshift
COPY events
FROM 's3://events-8a3b795/events-1.txt'
IAM_ROLE 'arn:aws:iam::730335624385:role/redshift-role-429cd23'
FORMAT AS JSON 'auto'
REGION AS 'us-east-1';

# SQL code: This is to view itemes in your table
SELECT * 
from events
order by id;

