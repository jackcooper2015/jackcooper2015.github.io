title: Joda-Time使用手册
author: jackcooper
date: 2018-03-19 17:14:00
tags:
---
## 简介 
Joda-Time 令时间和日期值变得易于管理、操作和理解。事实上，易于使用是 Joda 的主要设计目标。其他目标包括可扩展性、完整的特性集以及对多种日历系统的支持。并且 Joda 与 JDK 是百分之百可互操作的，因此您无需替换所有 Java 代码，只需要替换执行日期/时间计算的那部分代码。本文主要就JDK date calendar  与 jodatime 对比。

## 一 、Joda-Time使用说明
### 创建DateTime对象

```java
//方法一
 DateTime dateTime = new DateTime();
//方法二（推荐使用此方法）
DateTime dateTime = SystemFactory.getClock().getDateTime();
//方法三
DateTime dateTime = new DateTime(
  2000, //year
  1,    // month
  1,    // day
  0,    // hour (midnight is zero)
  0,    // minute
  0,    // second
  0     // milliseconds
);
```
### 创建LocalDate，LocalTime对象
LocalDate：该类封装了一个年/月/日的组合。当地理位置（即时区）变得不重要时，使用它存储日期将非常方便。例如，某个特定对象的出生日期 可能为 1999 年 4 月 16 日，但是从技术角度来看，在保存所有业务值的同时不会了解有关此日期的任何其他信息（比如这是一周中的星期几，或者这个人出生地所在的时区）。在这种情况下，应当使用 LocalDate。

```java
//方法一
LocalDate localDate = SystemFactory.getClock().getLocalDate();

//方法二
LocalDate localDate = new LocalDate(2009, 9, 6);// September 6, 2009
```

LocalTime：这个类封装一天中的某个时间，当地理位置不重要的情况下，可以使用这个类来只存储一天当中的某个时间。例如，晚上 11:52 可能是一天当中的一个重要时刻（比如，一个 cron 任务将启动，它将备份文件系统的某个部分），但是这个时间并没有特定于某一天，因此我不需要了解有关这一时刻的其他信息。

```java
//方法一
LocalTime localTime = SystemFactory.getClock().getLocalTime();

//方法二
LocalTime localTime = new LocalTime(13, 30, 26, 0);// 1:30:26PM
```

## 二、Joda处理时间
### 日期计算
 ###### 例1：计算上一个月的最后一天?
```java
LocalDate now = SystemFactory.getClock().getLocalDate();
LocalDate lastDayOfPreviousMonth = now.minusMonths(1).dayOfMonth().withMaximumValue();
```
* yearOfCentury
* dayOfYear
* monthOfYear
* dayOfMonth
* dayOfWeek
###### 例2：获得任何一年中的第 11 月的第一个星期二的日期，而这天必须是在这个月的第一个星期一之后？
```java
LocalDate now = SystemFactory.getClock().getLocalDate();
LocalDate electionDate = now.monthOfYear()
 .setCopy(11)        // November
 .dayOfMonth()       // Access Day Of Month Property
 .withMinimumValue() // Get its minimum value
 .plusDays(6)        // Add 6 days
 .dayOfWeek()        // Access Day Of Week Property
 .setCopy("Monday")  // Set to Monday (it will round down)
 .plusDays(1);       // Gives us Tuesday
```
**例2注释帮助您了解代码如何获得结果。.setCopy("Monday") 是整个计算的关键。不管中间 LocalDate 值是多少，将其 dayOfWeek 属性设置为 Monday 总是能够四舍五入，这样的话，在每月的开始再加上 6 天就能够让您得到第一个星期一。再加上一天就得到第一个星期二。Joda 使得执行此类计算变得非常容易。**

###### 例3 ：计算从现在开始经过两个星期之后的日期
```java
DateTime now = SystemFactory.getClock().getDateTime();
DateTime then = now.plusWeeks(2);
```

###### 例4：计算从明天起 90 天以后的日期
```java
DateTime now = SystemFactory.getClock().getDateTime();
DateTime tomorrow = now.plusDays(1);
DateTime then = tomorrow.plusDays(90);
```
###### 例5： 计算从现在起 156 秒之后的时间
```java
DateTime now = SystemFactory.getClock().getDateTime();
DateTime then = now.plusSeconds(156);
```

###### 例6：计算五年后的第二个月的最后一天
```java
DateTime now = SystemFactory.getClock().getDateTime();
DateTime then = now.minusYears(5) // five years ago
               .monthOfYear()     // get monthOfYear property
               .setCopy(2)        // set it to February
               .dayOfMonth()      // get dayOfMonth property
               .withMaximumValue();// the last day of the month
```

---
---

## 三 、Joda 和 JDK 互操作性
##### 从 Joda DateTime 类创建 JDK 类
```java
DateTime dateTime = SystemFactory.getClock().getDateTime();
Calendar calendar = dateTime.toCalendar(Locale.getDefault());
Date date = dateTime.toDate();
DateMidnight dateMidnight = SystemFactory.getClock().getDateMidnight();
date = dateMidnight.toDate();
```

##### 从 Joda LocalDate 类创建 JDK 类
```java
LocalDate localDate = SystemFactory.getClock().getLocalDate();
Date date = localDate.toDateMidnight().toDate();
```
##### JDK 对象转换为Joda对象
```java
java.util.Date jdkDate = obtainDateSomehow();
dateTime = new DateTime(jdkDate);
```

##### 将不同对象传递给 DateTime 的构造函数
```java
// Use a Calendar
java.util.Calendar calendar = obtainCalendarSomehow();
dateTime = new DateTime(calendar);
// Use another Joda DateTime
DateTime anotherDateTime = obtainDateTimeSomehow();
dateTime = new DateTime(anotherDateTime);
// Use a String (must be formatted properly)
String timeString = "2006-01-26T13:30:00-06:00";
dateTime = new DateTime(timeString);
timeString = "2006-01-26";
dateTime = new DateTime(timeString);
```
### 以 Joda 方式格式化时间

##### 使用了 ISODateTimeFormat 的静态方法
```java
DateTime dateTime = SystemFactory.getClock().getDateTime();
dateTime.toString(ISODateTimeFormat.basicDateTime());
dateTime.toString(ISODateTimeFormat.basicDateTimeNoMillis());
dateTime.toString(ISODateTimeFormat.basicOrdinalDateTime());
dateTime.toString(ISODateTimeFormat.basicWeekDateTime());

```

##### 传递 SimpleDateFormat 字符串
```java
DateTime dateTime = SystemFactory.getClock().getDateTime();
dateTime.toString("MM/dd/yyyy hh:mm:ss.SSSa");
dateTime.toString("dd-MM-yyyy HH:mm:ss");
dateTime.toString("EEEE dd MMMM, yyyy HH:mm:ssa");
dateTime.toString("MM/dd/yyyy HH:mm ZZZZ");
dateTime.toString("MM/dd/yyyy HH:mm Z");
 
09/06/2009 02:30:00.000PM
06-Sep-2009 14:30:00
Sunday 06 September, 2009 14:30:00PM
09/06/2009 14:30 America/Chicago
09/06/2009 14:30 -0500
```

---
---

## 四、对比例子
### 1、初始化某个瞬间的对象（除 “现在” 以外）并加上特定天数
* JDK 
```java
Calendar calendar = Calendar.getInstance();
calendar.set(2000, Calendar.JANUARY, 1, 0, 0, 0);
SimpleDateFormat sdf = new SimpleDateFormat("E MM/dd/yyyy HH:mm:ss.SSS");
calendar.add(Calendar.DAY_OF_MONTH, 90);
System.out.println(sdf.format(calendar.getTime()));
```

*Joda
```java
DateTime dateTime = new DateTime(2000, 1, 1, 0, 0, 0, 0);
System.out.println(dateTime.plusDays(90).toString("E MM/dd/yyyy HH:mm:ss.SSS");
```

### 2、距离 2000年1月1日 45 天之后的某天在下一个月的当前周的最后一天的日期

* Joda
```java
DateTime dateTime = new DateTime(2000, 1, 1, 0, 0, 0, 0);
System.out.println(dateTime.plusDays(45).plusMonths(1).dayOfWeek()
  .withMaximumValue().toString("E MM/dd/yyyy HH:mm:ss.SSS");
```
结果
> Sun 03/19/2000 00:00:00.000


----
文章参考： https://www.ibm.com/developerworks/cn/java/j-jodatime.html