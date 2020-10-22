---
title: "Solve TimeZone in Azure Web App"
date: "2018-07-15"
description: "This blog will tell you how to handle time zone problem in Azure and Java"
categories:
    - "Azure"
    - "Cloud computing"
    - "Java"
    - "JavaScript"
    - "Intern"
---

# TimeZone's Problem

In my intern project, I need to handle timezone problem. Because in US, there is Daylight time saving and 5 diffferent timezone. My code should provide service for people in different timezones. Therefore, in the front end, I could allow user input time in their own timezone, but I should save date and time in **GMT+0**. Then I could make sure all time in database is saved in the same timezone. So, it is much easier to perform schedule.


## Idea and problem

The idea is quite straight forward.

<img src='https://g.gravizo.com/svg?
digraph G { 
    rankdir=LR;
    A [label="User TimeZone Input"],
    B [label="GMT+0 TimeZone"],
    C [label="User TimeZone Output"],
    A -> B,
    B -> C,
    }
'/>

There is one thing should mentioned. The default timezone of all Azure Virtual machine is `GMT+0`. You could change that in [`APPLICATION_SETTING`](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/app-service/app-service-web-configuration-and-management-faq.md#how-do-i-set-the-server-time-zone-for-my-web-app)

But here, since user could from different timezone, we did not set this value here.

## Java Code implementation

You could find lots of code on StackOverflow telling you how to change timezone, but they all could not use here. The reason is they all assume the input time's timezone is the same as the local machine time. For example, after parsing datetime from user input, it will use system timezone to understand this time and then you could set timezone id to change it. However, in my case, I want my web application to receive time and parse it to UTC. Because when my RESTful server receive that time, it thought it is **GMT+0**. Therefore, when I just set it to GMT+0 timezone, it would not change. 

```java

// WRONG WAY in THIS CASE!
String dateInString = "22-1-2015 10:15:55 AM";
LocalDateTime ldt = LocalDateTime.parse(dateInString, DateTimeFormatter.ofPattern(DATE_FORMAT));

ZoneId singaporeZoneId = ZoneId.of("Asia/Singapore");
System.out.println("TimeZone : " singaporeZoneId)
//LocalDateTime + ZoneId = ZonedDateTime
ZonedDateTime asiaZonedDateTime = ldt.atZo(singaporeZoneId);
System.out.println("Date (Singapore) : " asiaZonedDateTime)
ZoneId newYokZoneId = ZoneId.("America/New_York");
System.out.println("TimeZone : " + newYokZoneId);

```

So the right method is to parse time in custom timezone. Then you could just put that in GMT+0 timezone.

```java

DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/yyyy HH:mm:ss");

ZonedDateTime time = LocalDateTime.parse(end, formatter).atZone(ZoneId.of("America/New_York"));
time.format(DateTimeFormatter.ISO_INSTANT).toString());

```

## JavaScript implementation

For Javascript part, we could use `Moment` [package](https://momentjs.com/). In my case, I need to change timezone from GMT+0 to user local timezone. It is quite easy, because it is only one line.

```javascript
moment(UTCTimeString).format('MM/DD/YYYY HH:mm:ss')
```

## Conclusion

The function seems very easy, but I actually spent lots of time on it. The reason is I did not get that the machine's timezone was "GMT+0" in the beginning, therefore I could not change it.