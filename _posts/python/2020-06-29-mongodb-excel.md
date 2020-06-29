---
layout: post
title: 从mongodb导出数据到excel
category: python
tags: [python,mongodb,excel,数据分析]
---

使用pandas将mongodb中扁平的数据导出到excel。

爬虫爬取到的数据，一般保存在MongoDB数据库中，想要进行数据分析的话，需要把数据加载到pandas或者excel中。比较尴尬的是，MongoDB是非关系型数据库，数据以文档形式保存，文档的格式和字段可以不一致，所以不适合导出到excel这样的关系型表格中，可能也是因为这个原因，并没有提供直接导出excel的方法。不过如果数据确实是扁平的，那么也是可以导出到excel的。

#### 导出csv然后保存为excel
MongoDB可以导出csv，然后csv可以保存为excel，通过下面的命令：
```
$ mongoexport -d lagou -c lagou_jobs_main_city -f _id,jobName,jobId,district,company,companyId,salary,tempt,label -o ./lagou_jobs_main_city.csv
```

- -d: 数据库名
- -c: 集合名
- -f: 需要保存的列名
- -o: 保存的文件路劲

excel直接打开csv会出现乱码，可以按下面的步骤导入：
- 要先新建一个空白的excel文档
- 点击菜单栏中的“数据”->“获取外部数据”->“导入文本文件”
- 选择需要导入的csv文件
- 选择utf-8然后下一步
![AltText](/assets/images/me1.png)

- 选择“逗号”作为分隔符，下一步
![AltText](/assets/images/me2.png)

- 设置某列的数据格式，完成
![AltText](/assets/images/me3.png)

但是这样excel保存的数据格式是这样的，不适合进行数据分析：
![AltText](/assets/images/me4.png)

#### 加载到pandas然后保存到excel
第二种方法是通过pandas保存到excel，直接上代码：

```
# mongodb数据保存到excel
import pymongo
import pandas as pd

# 连接mongodb数据库
client = pymongo.MongoClient('localhost', 27017)
db = client['lagou']
jobs = db['lagou_jobs_main_city']

# 加载数据到pandas
data = pd.DataFrame(list(jobs.find()))

# 删除_id字段
del data['_id']

# 选择要显示的字段
data = data[['jobId', 'jobName', 'district', 'company', 'companyId', 'salary', 'tempt', 'label']]
# print(data)

# Dataframe写入到excel
file_path = r'./lagou_jobs_output.xlsx'
writer = pd.ExcelWriter(file_path)
data.to_excel(writer, columns=['jobId', 'jobName', 'district', 'company', 'companyId', 'salary', 'tempt', 'label'],
              index=False, encoding='utf-8', sheet_name='Sheet')
writer.save()


```

然后就能得到想要的数据格式excel：
![AltText](/assets/images/me5.png)

完毕。
