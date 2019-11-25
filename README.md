![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 获取所有数据库的数据库大小趋势
#### Get Database Size Trending On All Databases
**发布-日期: 2015年07月27日 (评论)**

![#](images/get-database-size-trending-on-all-databases-a.png?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
这里有一些SQL逻辑，它将在备份压缩之前和之后使用数据库大小生成一个简单的报告。这个逻辑最初是在Erin Stellato的SQLSkills中创建的。原始帖子可以在这里找到：http：//www.sqlskills.com/blogs/erin/trending-database-growth-from-backups/她指出如何通过excel轻松地对这些数据进行趋势分析。
我只是修改了逻辑以便可以包含所有数据库，并检查数据库是否仍然存在。



## English
Here’s some SQL logic that will use the database size before and after backup compression and produce a simple report. This logic was originally created at SQLSkills from Erin Stellato. The original post can be found here: http://www.sqlskills.com/blogs/erin/trending-database-growth-from-backups/ She points out how you can easily trend this data via excel.
I simply modified the logic to include all databases, and to check to see if the database still exists. 


---
## Logic
```SQL
use master;
 set nocount on
 declare @get_dbsize_history varchar(max)
 declare @first_day_of_year varchar(25)
 set @first_day_of_year = (select dateadd(year, datediff(year, 0, getdate()), 0))
 set @get_dbsize_history = ''
 select @get_dbsize_history = @get_dbsize_history +
 
'
 select
 ''database'' = cast([database_name] as varchar(20))
 , ''month'' = convert(varchar(7),[backup_start_date],120)
 , ''backup size gb'' = str(avg([backup_size]/1024/1024/1024),5,2)
 , ''compressed bu size'' = str(avg([compressed_backup_size]/1024/1024/1024),5,2)
 , ''compression ratio'' = str(avg([backup_size]/[compressed_backup_size]),5,2)
 
from
 msdb.dbo.backupset
 
where
 [database_name] = ''' + name + '''
 and [type] = ''d''
 and backup_finish_date > ''' + @first_day_of_year + '''
 and database_name in (select name from master.sys.databases)
 group by
 [database_name]
 , convert(varchar(7),[backup_start_date],120)
 
order by
 [database_name],convert(varchar(7),[backup_start_date],120);
 ' + char(10) + char(10) 
from
 	sys.databases
where
 	database_id > 4
 	exec (@get_dbsize_history)


```

This information can then be trended using excel. For example; this is one of the Sharepoint Databases.

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

