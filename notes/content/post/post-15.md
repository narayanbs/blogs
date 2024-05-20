---
title: "Working with date and time"
date: 2022-04-07T11:37:56+05:30
publishdate: 2022-04-07
lastmod: 2022-04-07
draft: true
tags: [General,python]
---
#### How Computers count time
Nearly all computers count time from an instant called the Unix epoch. This occurred on January 1, 1970, at 00:00:00 UTC. UTC stands for Coordinated Universal Time and refers to the time at a longitude of 0°. UTC is often also called Greenwich Mean Time, or GMT. Longitude 0<sup>0</sup> passes through greenwich england. UTC is not adjusted for daylight saving time, so it consistently keeps twenty-four hours in every day. 

By definition, Unix time elapses at the same rate as UTC, so a one-second step in UTC corresponds to a one-second step in Unix time, with the exception of leap seconds. Leap seconds are occasionally added to UTC to account for the slowing of the Earth’s rotation but are not added to Unix time.

Number of Seconds since unix epoch (excluding leap seconds) as a floating point number, also known as unix time, can be displayed using python as shown below
```
>>> import time
>>> time.time()
15923423423.234234
```
As we see, Unix time is nearly impossible for a human to parse. So, Unix time is typically converted to UTC, which can then be converted into a local time using time zone offsets.
The Internet Assigned Numbers Authority (IANA) maintains a database of all of the values of time zone offsets. This database is often included with your operating system.

The database contains a copy of all the designated time zones and how many hours and minutes they’re offset from UTC. The offset for Nepal, for example, is +05:45, from UTC.

Note: The timezones can be obtained on linux using 
```
$ timedatectl list-timezones
# for London
$ timedatectl list-timezones | grep London
```

Note: 
* Longitude 0<sup>0</sup> passes through Greenwich england. 
* The International date line is at longitude 180<sup>0</sup>

#### Standard Date representation
* United States:  January 31, 2020 as 01-31-2020 
* Europe/Worldwide:  31 January, 2020 as 31-01-2020

ISO 8601 standardized the format to `YYYY-MM-DD HH:MM:SS`

----------------------

#### Python date and time

The standard library provides the following modules to handle date and time in python
1. `datetime` supplies classes for manipulating date and time
2. `calendar` outputs calendars and provides functions using an idealized Gregorian Calendar.
3. `time` provides time-related functions where dates are not needed. 

##### datetime module
The module provides the following classes to manipulate date and time. 
1. `date` is an idealized date that assumes the Gregorian Calendar extends infinitely into the future and past. This object stores the year, month, and day as attributes.

2. `time` is an idealized time that assumes there are 86,400 seconds per day with no leap seconds. This object stores the hour, minute, second, microsecond, and tzinfo (time zone information).

3. `datetime` is a combination of a date and a time. It has all the attributes of both classes.

4. `timedelta` represents the duration between two dates or datetimes. 

##### Creating date, time and datetime

```
from datetime import date, time, datetime

# syntax to create a date object
date(year, month, day)

d = date(year=2022, month=4, day=4)    # leading 0 is not permitted, just use 4 not 04
print(d)
date(2022, 4, 4)

# extract attributes
print("Year:", d.year)
print("Month:", d.month)
print("Day:", d.day)

# syntax to create a time object
time(hour, minute, second, microsecond, tzinfo)

t = time(hour=23, minute=14, second=30)
print(t)
time(23, 13, 30)

t = time(7, 10, 45, 400437)

# extract attributes
print("Hours:", t.hour)
print("Minutes:", t.minute)
print("Seconds:", t.second)
print("Microseconds:", t.microsecond)

# syntax to create datetime object
datetime(year, month, day, hour, minute, secod, microsecond, tzinfo)

dt = datetime(year=2021, month=2, day=17, hour=13, minute=47, second=34)
print(dt)
datetime.datetime(2021, 2, 17, 13, 47, 34)

# extract attributes
print("Year:", dt.year)
print("Month:", dt.month)
print("Day:", dt.day)

print("Hours:", dt.hour)
print("Minutes:", dt.minute)
print("Seconds:", dt.second)
print("Microseconds:", dt.microsecond)
```
##### Useful operations

```
# Getting today's date
today = date.today()

# Getting current date and time 
now = datetime.now()

# Getting current date
current_date = now.date()

# Getting current time
current_time = now.time()

# Getting current timestamp -- Number of seconds from epoch
current_timestamp = now.timestamp()

# create datetime by combining date and time
dt = datetime.combine(current_date, current_time)

# Getting datetime from timestamp
ts = 1617295943.17321
dt = datetime.fromtimestamp(ts)

# Getting date from timestamp
ts = 1617295943.17321
d = date.fromtimestamp(ts)

# Getting date and time from strings
# string representing date and time in iso 8601 format
dt = date.fromisoformat("2022-04-12")
print(dt)
 -- datetime.date(2022, 4, 12)

t =  time.fromisoformat("14:02:33")
print(t)
 --  datetime.time(14, 2, 33)

# Getting datetime from string not in iso format
dt = datetime.strptime("01-31-2020 14:45:37", "%m-%d-%Y %H:%M:%S")
print(dt)
 -- datetime.datetime(2020, 1, 31, 14, 45, 37)
```
##### timedelta
timedelta represents the duration between two dates or datetimes.
```
# Getting timedelta in days i.e number of days between dates
start_date = date(2006,12,01)
today = date.today()
print((today - start_date).days)

# we can also do the same for datetime instances
start_dt = datetime(2020,11,2,12,45,0)
today_dt = datetime.now()
print((today_dt - start_dt).days)

# Getting timedelta in Hours, minutes and seconds
start_time = datetime.strptime("4:25:40", "%H:%M:%S")
end_time = datetime.strptime("11:40:10", "%H:%M:%S")

difference = end_time - start_time

# timedelta in seconds, minutes and hours
seconds = difference.total_seconds()
minutes = seconds / 60
hours = seconds / (60 * 60)

# More examples
from datetime import datetime, timedelta

current_dt = datetime.now()
tomorrow_dt = current_dt + timedelta(days=+1)
yesterday_dt = current_dt + timedelta(days=-1)
future_dt_2yrs = current_dt + timedelta(days=+730)
future_dt_2days = current_dt + timedelta(days=+2)

now = datetime.now()
print(now)
 -- datetime.datetime(2020, 1, 26, 9, 37, 46, 300905)

delta = timedelta(days=+3, hours=-4)
after = now + delta
print(after)
 -- datetime.datetime(2020, 1, 29, 5, 37, 46, 380905)

```
timedelta is very useful in this way, but it’s somewhat limited because it cannot add or subtract intervals larger than a day,
such as a month or a year. 
Fortunately we can use a package that is a more powerful replacement and is also useful for us when working with time zones, 
as shown later.Infact the python documentation suggests using this for timezones. 

```
$ pip install python-dateutil
    or
$ poetry install python-dateutil
```
dateutil provides relativedelta that is more flexible that timedelta

```
>>> from dateutil.relativedelta import relativedelta
>>> tomorrow = relativedelta(days=+1)
>>> now + tomorrow
datetime.datetime(2020, 1, 27, 9, 37, 46, 380905)

>>> delta = relativedelta(years=+5, months=+1, days=+3, hours=-4, minutes=-30)
>>> now + delta
datetime.datetime(2025, 3, 1, 5, 7, 46, 380905)
```
Notice in this example that the date ends up as March 1, 2025. This is because adding three days to now would be January 29,
and adding one month to that would be February 29, which only exists in a leap year. Since 2025 is not a leap year, the date 
rolls over to the next month.

Using relativedelta to calculate the difference between two datetime instances.

```
>>> now
datetime.datetime(2020, 1, 26, 9, 37, 46, 380905)
>>> tomorrow = datetime(2020, 1, 27, 9, 37, 46, 380905)
>>> relativedelta(now, tomorrow)
relativedelta(days=-1)
```

##### Working with Time Zones

Python datetime instances support two types of operation, naive and aware. The basic difference between them is that 
naive instances don’t contain time zone information, whereas aware instances do.
An aware datetime instance can compare itself unambiguously to other aware datetime instances and will always return 
the correct time interval when used in arithmetic operations, Naive datetime instances, on the other hand, may be ambiguous. 
One example of this ambiguity relates to daylight saving time. Areas that practice daylight saving time turn the clocks forward 
one hour in the spring and backward one hour in the fall.
Practically, what happens is that the offset from UTC in these time zones changes throughout the year. IANA tracks these changes 
and catalogs them in the different database files that your computer has installed. Using a library like dateutil, which uses the 
IANA database under the hood, is a great way to make sure that your code properly handles arithmetic with time.

Storing the time zone in which a date occurs is an important aspect of ensuring your code is correct. 
Python datetime provides tzinfo, which is an abstract base class that allows datetime.datetime and datetime.time to include 
time zone information, including an idea of daylight saving time.

Prior to python3.9, datetime did not provide a direct way to interact with IANA timezone database. The python datetime.tzinfo documentation 
recommended using a third party package such as dateutil. So prior to 3.9 we could only specify utc as the timezone, out of the box. 
for ex:
datetime.datetime(2020, 9, 18, 13, 2, 47, 24553, tzinfo=datetime.timezone.utc)

For everything else we had to use a package like dateutil to specify other timezones.

python 3.9 introduced the zoneinfo module. The ZoneInfo class provides us access to the IANA database in our computer.
Now we can now specify other timezones besides utc. 

```
# Prior to 3.9
from datetime import datetime, timezone

now = datetime.now()
print(now)
 -- datetime.datetime(2020, 9, 18, 13, 2, 47, 24553) # returns naive datetime

now = datetime.now(tz=timezone.utc)
print(now)
 -- datetime.datetime(2020, 9, 18, 13, 2, 47, 24553, tzinfo=datetime.timezone.utc)  # returns aware datetime

# From 3.9 onwards
from zoneinfo import ZoneInfo
zi = ZoneInfo("America/Vancouver")
print(zi)
 -- ZoneInfo(key="America/Vancouver")

# datetime in oslo
print(datetime.now(tz=ZoneInfo("Europe/Oslo")))
 -- datetime.datetime(2020, 9, 23, 19, 27, 30, 544399, tzinfo=zoneinfo.ZoneInfo(key="Europe/Oslo"))

# To convert datetime b/w timezones
release_date = datetime(2020, 10, 5, 3, 9, tzinfo=ZoneInfo("America/Vancouver"))  
rd = release_date.astimezone(ZoneInfo("Europe/Oslo"))
print(rd)
 -- datetime.datetime(2020, 10, 5, 12, 9, tzinfo=zoneinfo.ZoneInfo(key="Europe/Oslo"))

# All available timezones
print(len(zoneinfo.available_timezones()))
 -- 595
```
Let's look at an example
```
from datetime import datetime, time, data, 
from zoneinfo import ZoneInfo 
# Date of pycon in New York
PYCON_DATE = datetime.strptime("May 12, 2024 8:00 AM", "%B %d, %Y %H:%M %p")
print(PYCON_DATE)
 -- datetime.datetime(2022, 5, 12, 8, 0)
PYCON_DATE = PYCON_DATE.astimezone(ZoneInfo("America/New_York"))
# naive datetime
now = datetime.now()
# timezone aware datetime
now = now.astimezone()

countdown = PYCON_DATE - now.astimezone()
print(f"Countdown to Pycon 2024: {countdown}")
-- 71 days, 15:30:46.114604
```
Relativedelta with strftime
```
from datetime import datetime
from zoneinfo import ZoneInfo

from dateutil.relativedelta import relativedelta

PYCON_DATE = datetime.strptime("May 12, 2024 8:00 AM", "%B %d, %Y %H:%M %p")
PYCON_DATE = PYCON_DATE.astimezone(ZoneInfo("America/New_York"))

def time_amount(time_unit, countdown):
    t = getattr(countdown, time_unit)
    return f"{t} {time_unit}" if t != 0 else ""

def main():
    now = datetime.now().astimezone()
    countdown = relativedelta(PYCON_DATE, now)
    time_units = ["years", "months", "days", "hours", "minutes", "seconds"]
    output = (t for tu in time_units if (t := time_amount(tu, countdown)))
    pycon_date_str = PYCON_DATE.strftime("%A, %B %d, %Y at %H:%M %p %Z")
    print(f"Pycon us 2024 will start on:", pycon_date_str)
    print("Countdown to Pycon us 2024:", " ".join(output))


if __name__ == "__main__":
    main()
```

Let's look at the effects of daylight savings time. 
```
tz = ZoneInfo("America/Vancover")
print(tz.tzname(datetime(2024, 10, 5)))
 -- 'PDT'

print(tz.tzname(datetime(2024, 12, 5)))
 -- 'PST'

```


Best Practices with Time Zones
* Meetings, train times, concerts should have a time zone 
* Timestamps should be naive and based on UTC. Timestamps are normally used in server logs or other logging.
* Timestamps should always be monotonically increasing. things like daylight savings shouldn't affect them.
-----------------
#### Working with time module
```
# Number of seonds since epoch
$ time.time()

# converts time in seconds since epoch to struct time representing UTC or GMT
# if secs is None, then current time as returned by time.time() is used
# fractions in secs are ignored
$ time.gmtime([secs]) 
time.struct_time(tm_year=2022, tm_mon=4, tm_mday=7, tm_hour=6, tm_min=58, tm_sec=4, tm_wday=3, tm_yday=97, tm_isdst=0)

# similar to above, except it converts time in seconds since epoch to 
# struct time representing local time
$ time.localtime([secs]) 
time.struct_time(tm_year=2022, tm_mon=4, tm_mday=7, tm_hour=12, tm_min=28, tm_sec=36, tm_wday=3, tm_yday=97, tm_isdst=0)

# Inverse of localtime, it converts struct time representing local time 
# into seconds since epoch
$ time.mktime(time.localtime()) 
19729384982.03

# Inverse of gmtime(), is provided in the calendar module
$ calendar.timegm(time.gmtime())

# converting seconds since epoch into string
$ time.ctime(time.time()) 
'Sat Jun 06 16:26:11 1998'

# converting struct time into string
$ time.asctime(time.gmtime()) or time.asctime(time.localtime()) 
'Sat Jun 06 16:26:11 1998'

# We can get the unix epoch, if the number of seconds since epoch is 0
$ time.asctime(time.gmtime(0)) 
'Thu Jan 1 00:00:00 1970'
$ time.asctime(time.localtime(0)) 
'Thu Jan 1 05:30:00 1970'
```
##### Date and time format specifiers
```
%a	Abbreviated weekday name		Sun
%A	Full weekday name			Sunday
%b	Abbreviated month name			Mar
%B	Full month name	March
%c	Date and time representation		Sun Aug 19 02:56:02 2012
%d	Day of the month (01-31)		19
%H	Hour in 24h format (00-23)		14
%I	Hour in 12h format (01-12)		05
%j	Day of the year (001-366)		231
%m	Month as a decimal number (01-12)	08
%M	Minute (00-59)				55
%p	AM or PM designation			PM
%S	Second (00-61)				02
%U	Week number with the first Sunday as the first day of week one (00-53)	33
%w	Weekday as a decimal number with Sunday as 0 (0-6)	4
%W	Week number with the first Monday as the first day of week one (00-53)	34
%x	Date representation			08/19/12
%X	Time representation			02:50:06
%y	Year, last two digits (00-99)		01
%Y	Year					2012
%Z	Timezone name or abbreviation		CDT
%%	A % sign				%
```
A monotonic clock cannot go backward, it always goes forward. It is used to calculate elapsed time. (perf_counter should be preferred).
```
time.monotonic() -- returns time as float value that represents the number of monotonic clock in fractional seconds
time.monotonic_ns() -- returns time as integer that represents nanoseconds
```
Performance counter is a clock with the highest available resolution to measure a short duration. It can be used to calculated elapsed time (e.g: start - end) and is preferred. 
```
time.perf_counter() -- returns time as float value that represents fractional seconds
time.perf_counter_ns() -- returns time as integer value that represents nanoseconds.
```
Process time is the sum of the system and user cpu time of the current process in fractional seconds (float value)
```
time.process_time() -- returns time as float value that represents fractional seconds
time.process_time_ns() -- returns time as integer that represents nanoseconds
```

