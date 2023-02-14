---
title: MongoDB 6.0 설치 및 테스트 (mongod, mongosh)
toc: true
categories:
  - 데이터/데이터베이스
tags:
  - MongoDB 6.0
  - mongo
  - mongod
  - MongoDB
  - mongosh
  - updateUser
---

7월 19일 MongoDB 6.0 버전이 release 되었습니다. 기존엔 MongoDB가 설치되면 mongo로 테스트를 했었는데 6.0이 되면서부터 mongo 대신 mongosh를 사용해야해서 여기에 정리합니다.

## **순서**

1. **MongoDB 설치**
2. **계정 추가**
3. **bindIP 및 authorization 설정**
4. **테스트**

<br>

### **MongoDB 6.0 설치**

```bash
$ wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
$ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
$ sudo apt-get update
$ sudo apt-get install -y mongodb-org
$ sudo systemctl enable mongod.service
```

<br>

### **계정 추가**

```bash
$ mongosh
test> use admin
admin> db.updateUser("admin", { pwd: "...", roles: ["root"], passwordDigestor: "server" })
admin> db.updateUser("user", { pwd: "...", roles: ["dbOwner"], passwordDigestor: "server" })
admin> exit
```

<br>

### **bindIP 및 authorization 설정**

```bash
$ vi /etc/mongod.conf
...
net:
  port: 27017
  bindIP: 0.0.0.0
security:
  authorization: enable

$ sudo systemctl restart mongod
```

<br>

### **테스트**

```bash
$ mongosh -u user
Enter password: **********

...

test> show dbs;
admin	180.00 KiB
config	 48.00 KiB
local	 72.00 KiB
test> use test_db;
switched to db test_db
test_db> db.test_col.insertOne({"name": "Mongo Kim", "age": 22})
{
  acknowledged: true,
  insertedId: ObjectId("6369f4b8cf64f2d9e24ddbg5")
}
test_db> db.test_col.find()
[
  {
    _id: ObjectId("6369f4b8cf64f2d9e24ddbg5"),
    name: 'Mongo Kim',
    age: 22
  }
]
```
