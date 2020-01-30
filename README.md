![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Take SQL Database Offline And Disable All Associated SQL Logins
**Post Date: February 16, 2016**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>The following SQL logic takes a database offline, and then disables all subsequent logins of the database that have a name that is associated with the database name. In this case we are using a wild card. In this example we are using an old TRACKIT database. This logic is useful in cases where you are decommissioning old database environments, but don't necessarily want to drop any Databases or their users. A traditional approach is to simply take the database offline, and disable it's users. Then after a period of 30 days you can decommission the database by removing it and the users. I've included a couple quick queries to confirm the database has been taken offline, and the logins have been disabled including a timestamp when the logins were modified.</p>     



## SQL-Logic
```SQL
-- take the database offline
alter database [TRACKIT8] set offline
with rollback immediate
 
-- disable all logins associated with same database name using wildcard
declare @disable_trackit_logins varchar(max)
set     @disable_trackit_logins = ''
select  @disable_trackit_logins = @disable_trackit_logins + 
        'alter login [' + name + '] disable;' + char(10)
from    syslogins where name like 'track%' order by name asc
exec    (@disable_trackit_logins) --for xml path(''), type
 
-- confirm database is offline
select
    'database'          = upper(name)
,   'is_offline'        = state_desc
from
    sys.databases
where
    name like 'trackit%'
 
-- confirm all logins have been disabled
select
    'login'             = upper(name)
,   'is_disabled'       =
                        case is_disabled
                            when 0 then 'Enabled'
                            when 1 then 'Disabled'
                        end
,   'modified_on'       = left(modify_date, 19)
from
    sys.sql_logins
where
    name like 'Trackit%'
```

![Take SQL Database Offline Disable SQL Logins]( https://mikesdatawork.files.wordpress.com/2016/02/image001.jpg?w=829 "take sql database offline and disable logins")
 


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

    
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

