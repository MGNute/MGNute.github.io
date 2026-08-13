+++
title = "Head of the Charles Scheduling"
description = "Some of the work I've done over the years for HOCR."
author = "Mike Nute"
date = "2026-08-12"
draft = false
tags = ["python", "R", "spatial statistics", "data vizualization"]
# categories = ["themes", "syntax"]
+++

I've been volunteering for the [Head of the Charles Regatta](http://hocr.org) since 2005. If you're not familiar, HOCR is the largest multi-day rowing event in the world and it takes place on the Charles river in Boston every October. It draws roughly 10,000 athletes annually and has enough sponsors that the event itself employs at least three full time staff at any given time, plus an army of volunteers that converge on race weekend to make the event possible. 

My role since 2009 has been that I make the official schedule every year. Given a few parameters for each event, plus the order of the events on each day, computing the official start times for each event isn't especially difficult. It's just arithmetic. But managing those particular parameters and the collective assumptions that go into timing the events at least requires some prudent attention to the historical data to make sure events don't collide with each other while also making sure the pacing is good and the regatta ends at a reasonable hour. To do enable that, I've also been managing and maintaining a database of historical results data for all HOCR events going back 17+ years, and each year we learn something new about how the event operates.

## Background

In late 2009, after the regatta that year, a group of the regatta staff and volunteers in the starting line team got together to see if the event schedule could possibly be compressed to accomodate more events over the course of the (then) two-day race weekend. They had noticed that some events had long stretches of "dead water" between them, and at the same time there was a general desire to add new events, particuarly for adaptive rowing and to expand access among youth rowers. 

The math of making the schedule isn't especially complicated. You start with a basic assumption: the fastest boat in the event will start first and the slowest boat will start last. The former tends to be more or less true year over year because of how the boats are seeded, but the latter tends to be conservative on average but could easily be true in the worst case (and probably is true at least in one race a year). Next, you assume an average time between boats at the starting gate. The HOCR is a "head" regatta where boats cross the starting gate one at a time and the results are based on net time. Finally, you assume the time for the fastest boat (the "rabbit") and the time for the slowest boat (the "caboose"). Add to that a basic margin between events (typically about 1:30) and, provided you know how many hulls are in the event, the start times fall out arithmetically:

```
Next_evt_start = Curr_evt_start + [Start gate gap (sec)] * (# hulls) + [Curr_evt_caboose_time - Next_evt_rabbit_time] + safety_margin
```

It turns out though that small deviations in those parameters can add up to a lot of extra time throughout the day. Even if you assume that all "next event start" times are rounded *up* instead of rounded either way, that can add 15-20 minutes to the day by itself. If the time-between-starts, typically on the order of 12-17 seconds depending on the boat type and event class, is conservative by an average of 1 second, that can add an unnecessary 30-60 minutes to the day. Then also if the caboose times aren't properly calibrated, that can add another couple minutes per event. So there was actually a lot of room in the schedule to be gained by pinning these numbers down, but also in pinning down their variability to keep risk well managed. 

### Historical Data & Improvements

At the time, all we had was the data from 2007-2009, which was helpful but certainly didn't represent the universe of possible result-sets very well. Still though, we could get a reasonable sense of the variance and what the numbers ought to look like year to year. What we found was that the numbers that were being used were not crazy, but almost across the board they were clearly conservative. Still, with only three years of data we had to keep some of the conservatism in the assumptions, but we could pull it out a little at a time. As noted earlier, it turns out with all the conservatism built in, rounding every start time *up* automatically was quietly adding extra cushion above 1:30. 

Also, it turns out that the time-between-starts for each event could actually be set with some precision because in fact, what matters for the schedule is not the actual boat-by-boat time between starts, but the *average* for the entire event. If boats cross the start like this:

```
       [boat_A]---------------------[boat_B]------[boat_C]-----
```

or like this:

```
       [boat_A]------[boat_B]---------------------[boat_C]-----
```

That has the same impact on the schedule! So because the time between starts like that is actually averaged out over many hulls in an event, *and* because if you look closely there you can see that the time between boats A and B will actually be anti-correlated with the time between boats B and C, the average time between hulls for a specific event is quite stable year to year!

## Gathering data for over a decade

### Rabbit & Caboose Variance

Every year since then, I've pulled the race-result data (using wall-clock times) and gradually compiled a database of results that can tell us quite a bit. Here's one of the graphics we look at every year, for a set of 12 events that this year happened to fall early to mid-day on Sunday:

![Time on Course Distribution by Event-Year](/hocr_coursetime_boxplots_50_61.png#center)

Each panel here is one event and the graph shows a box-and-whisker plot of the times it took all the boats in that event, that year, to complete the course. The BW points are the 5/25/50/75/95 percentiles, with outliers plotted inidividually and the number of hulls by year labelled below. 

To me, these plots tell us a lot about the nature of boats in a rowing race. For one thing, anybody who has rowed is aware that the wind direction has a major effect on how it goes out there, and we can tell just by looking at this plot which years were headwind and tailwind years in the past. In fact, one thing that changes year-to-year is frequently the order of events, and sometimes events that were on separate days will be combined, so this panel ordering reflects the order of events in 2025 but not necessarily in prior years. *However*, the youth events have always historically been at least on the same day, though not in the same sequence. So looking at the Youth events, two years in particular stand out as being the harshest conditions: 2018 and 2016. As it happens, after 2016 we assumed that would represent close to the worst conditions we might see in a given year but we were sharply corrected two years later.

But we can see a couple important things from those years. First of all, the delta between a max-headwind and max-tailwind year is probably about 20-30% in time on course, on average, although that varies strongly depending on the *weight* of the hull; i.e. that effect is more pronounced for doubles than eights, and more for women than men, and more for lightweight than openweight boats. That makes quite a bit of sense of course because wind affects lighter objects much more dramatically than heavier ones regardless of context. In fact, in 2018 we ran into one of the only times that the starting line failed to get boats across the starting gate on schedule because in the women's lightweight four event, the boats were being blown around so badly many of them had trouble getting to the start line under control.

Another point that we can see though is that a headwind introduces far more variance to the slower end of the distribution than it does to the faster end. That is, in 2017 (a tailwind year), the cabooses all finished well below the red dashed line, whereas the rabbits all finished only a little below the green line. But in 2018, the rabbits finished a little above the green line, but the cabooses finished *way* above the green line. So on balance headwinds introduce risk to the assumptions in the scheduling where tailwinds do not. 

### Age Effects

Here's another plot that I'm fond of. With 15+ years of data we can get a keen sense of how race time varies with the age of the competitor. This is especially true when we look at only single scullers because we have an exact age data point for each competitor:

![Time on Course by Age (1x)](/hocr_coursetime_vs_age_singles.png#center)

This paints a picture that anyone over 40 is familiar with although I would argue is still somewhat surprising in its detail. This is the same box and whisker plot for all years combined (with outliers removed), grouped by competitor age. Of couse it's no surprise that we slow down as we get older, but a couple things are still noteworthy.

First, the shape of the curve here closely resembles the actuarial one-year mortality rate curve (image credit to a blog called [Zelophehad's Daughters](https://zelophehadsdaughters.com/2015/05/11/predicting-who-will-be-church-president-fit-of-actuarial-mortality-table-to-historical-data/)):

![Mortality by Age Curve](/one-year-mortality-rate-by-age-for-q15-members-1960-2015.png#center)

In both cases, unfortunately the effect of aging an additional year accelerates as we get older, and this shows up in the HOCR results data in the same way it shows up in all-cause-mortality data. If you are one of those idiosyncratic billionaires trying to find a cure for aging, just know that you have a lot of biology working against you, unfortunately. 

There's also a little funny effect in this graph in that evidently you get better as you cross from 39 to 40 years old. That's likely an artifact of the way rowing classes are assigned: at 40, rowers are eligible for the Masters category and that race becomes much more competitive for a singles rower than the Club category where a 39 year old sculler is likely near the bottom of the seeding. Another though is that within the Masters class, from age 40 to 49, there is very little statistical drop off in the finish times. So I, at age 43, have no reason to complain about my age according to the data. 

### Iterating each year

So each year from 2010 to 2020 we would make small tweaks to the schedule and most of the time that meant removing small elements of conservatism. That's not to say there haven't been some hiccups in that time (see that 2018 thing, for example), but on the whole we have managed the risk well and have kept the event smooth and safe for all athletes. We even got a write-up in Row2k about it [back in 2017](https://www.row2k.com/hocr/features/2017/2881/HOCR-2017--Behind-the-Head-of-the-Charles-Regatta-schedule/). 

One thing that we'd come across every year were concerns that would arise ultimately based on anecdotes that had been passed along. As a statistician, you never want to fall too in love with your model because you might simply be missing something. So one year I developed a set of visualizations to actually show what each boat across the entire weekend had doen while on the course. These were a custom version of a [Marey diagram](https://www.jointjs.com/demos/marey-chart), also known as a [time-and-distance plot](https://en.wikipedia.org/wiki/Charles_Ibry). In our case, we show each boat as a line segment connecting the wall-clock-time when it starts, passes each of the 3 split points on the course, and finishes (bottom to top, below):

![Event Replay Graph - 2024, Sunday PM](/hocr_2024_races_sun_2.png#center)

In this case, the pink area is the time that we *plan* for a given event to be on the course based on the scheduling assumptions. The individual segments are the actual boats as they were recorded on the course. This had the nice benefit of letting us take any individual anecdote and match it to an actual data point in the graph, where we could make sense of whether it was a pattern or not. Most of the time it wasn't, but when it was we all learned something. Here is what we saw in 2018 (that nasty headwind year) on Sunday afternoon:

![Event Replay Graph - 2018, Sunday PM](/hocr_2018_races_sun_2.png#center)

It may not look like much in the graph, but if you look at the Champ & Lightweight 4+ Women's event, starting around 1:45, you can see that big string of boats starting well past the scheduled times. In 20 years of working with the starting line that's the only time I've ever seen so many boats in an event start so late, and in that case you can see it bled into the next race where the lead boat in the next event had to overtake the event before it. That is the kind of incident we try to avoid, so it was a lesson well learned. You'll also notice that it happened for the women's lightweight 8+ event later in the day as well, although not to the same degree and fortunately there was enough conservatism to avoid an event collision again. 

## What it all means

Thats a lot to say about one little database, but the real reason that I'm especially proud of it is because of this graphic right here:

![Athletes by Year](/hocr_hulls_athletes_by_year.png#center)

Until 2021, when the race went to three days instead of two, we were able to manage the scheduling risk and keep everybody safe in a way that allowed us to increase the participant count by roughly 30% in that time. Now of course that means more revenue to support the event and provide a good experience, but what I consider more important is that it means more athletes who get to experience the competition, especially youth, college and adaptive athletes. Having coached those rowers, I know how special they considered it so I've always been ready to find a way to get a few more of them in. 