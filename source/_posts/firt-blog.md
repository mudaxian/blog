---
title: firt blog
date: 2022-07-06 11:19:46
tags:
---


## 1.select根据pid查询影像

​		如果要合并，查出肯定是多笔影像

## 2.根据pid修改dicomseries

​		把studyinstanceuid改为一致

```sql
update dicomseries set studyinstanceuid = '1.2.840.113704.9.1000.16.0.20220116091849162' where patientID = '5320220116013';
```

## 3.改为一致后再查询dicomstudies

​		把不需要的一笔的accessionnumber改为（原5502111280007）11280007_bak

```sql
update dicomstudies set accessionnumber = '0116013006_bak' where patientID = '5320220116013' and studyinstanceuid = '2022.1.16.7.56.0.986';
```

## 4.查询message

​		把tryTIme和uploadType改为0 type改为-1



```sql
update message set tryTime = 0, uploadType = 0, type = -1 where patientID = '5320220116013';
```



## 5.打开一个新的服务器

​		tailtom查看上传是否完成，完成后uploadType为1，即表示合并成功

## 6.最后总院检查一下是否上传