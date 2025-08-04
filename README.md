# Note:
1. The system used in this paper is a local virtual machine, not an Internet public target
2. Due to Shanghai Lingdang Information Technology Co., Ltd only releasing the latest version of CRM to the public, the vulnerable version has been uploaded to the same directory as this article. So that you can reproduce and use it in your environment. It's named CRMV8.6.5.2-SP23_20250708.zip.


# 1、Open the target source code and determine the current version

<img width="1451" height="763" alt="image" src="https://github.com/user-attachments/assets/708089cb-07af-4908-927e-361ef3c764d9" />


# 2、Locate the vulnerability file =>  crm/WeiXinApp/yunzhijia/event.php

<img width="1934" height="847" alt="image" src="https://github.com/user-attachments/assets/ba069c67-5159-486c-b1ae-1aa4d06d0459" />


# 3、According to the code, we can analyze that the execution process of the PHP file is as follows
## a、Instantiate a class object , event_model


<img width="1397" height="822" alt="image" src="https://github.com/user-attachments/assets/468f8faf-7d21-4836-b88a-b7bef1a77884" />



## b、Get request parameters  $function = $_REQUEST["action"]; Obviously, the action parameter here needs to be "get_userid" in order to enter the case branch


<img width="1923" height="1018" alt="image" src="https://github.com/user-attachments/assets/ef6b8f7e-426a-4043-8793-232ffefed89b" />



## c、Then enter the get_userid function of the event_madel object and pass the parameters wbuserid, openid, and applicationType into the function

<img width="1831" height="927" alt="image" src="https://github.com/user-attachments/assets/d74f974f-4529-4a2d-86d3-bb8a3edc9cce" />

## d、By analyzing the get_userid function, it can be seen that as long as the number of select query results is less than 1, it will enter the else branch. In the else branch, the wbuserid and openid passed in are directly concatenated into the SQL statement, resulting in an SQL injection vulnerability

<img width="2328" height="838" alt="image" src="https://github.com/user-attachments/assets/afdc97c8-6669-48d4-a1d8-1f289b483a53" />



# 4.Vulnerability verification
## The following is the target website access page

<img width="2530" height="1157" alt="image" src="https://github.com/user-attachments/assets/1e134bad-b874-4354-8744-9d05084b6531" />


## Use the sqlmap tool to detect

python sqlmap.py  -u "http://192.168.8.27:85/crm/WeiXinApp/yunzhijia/event.php?action=get_userid&applicationType=market&openid=1*"  --level 5    --dbms mysql


<img width="2210" height="1156" alt="image" src="https://github.com/user-attachments/assets/230787ff-af4b-49e3-b5bd-52fe5b293447" />

<img width="2322" height="1074" alt="image" src="https://github.com/user-attachments/assets/c6230216-907b-42d3-8aa4-605a6cb539b8" />

<img width="2446" height="963" alt="image" src="https://github.com/user-attachments/assets/9cf3cb38-dcff-4283-93d8-22b08b794e27" />


# The above is the complete content of this vulnerability. Thank you for your review and time!
