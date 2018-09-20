## [ClockPetals Demo](https://va.tech.purdue.edu/vast2017/mc1 "ClockPetals")

## Introduction
ClockPetals is an online visual analytics system designed for traffic analysis. This project was submitted to [2017 IEEE Visual Analytics for Science and Technology (VAST) Challenge Mini-Challenge One](http://vacommunity.org/VAST+Challenge+2017+MC1).

A bit about the VAST challenge: it's an annual IEEE competition event aiming to advancing visual analytics idea, design, and techniques. It usually kick off in late April and ends by mid July.

Each year VAST challenge provides meticulously synthesized dataset and context story to participants from all over the world and asks them to investigate what the data can tell via visual analytics methods. Typically every challenge consists of 2-3 sub challenges (called "mini-challenge") and, maybe, a grand challenge when the mini challenges are connected or in the same context. The investigation result (solution) typically should answer 3-5 questions the challenge asks. For each question, the solution should presents about 5 visual evidence and explanation for supporting the patterns/anomalies the investigation finds.

In the summer of 2017 (also in 2016 but another story), I worked with two serial-player-and-awardee professors in VAST challenge, along with an undergrad developer, a data scientist PhD (remote),and three visual designers. We competed for two mini challenges in parallel. I was appointed as the team lead to

* **manage the design and development iteration**. Normally we had 1-2 weekly meetings to report the progress and to discuss/critique the design ideas and their implementation. Since the projects were driven by the solution and the design, we started by exploring and massaging the data in the first couple of weeks. Meanwhile we brainstormed a lot of design ideas. When a design plan including features and visualization forms were determined in the meetings, I setup goals and deadlines for both design and development so that we could present the practical-but-mocked-up progress in the next meeting.

  >![Process](https://uploads-ssl.webflow.com/5b43c1ec7ab3d835fb006c5d/5b468b8276d89c85825d1b2b_vast-process.png)
(process flowmap courtesy by one of the designers [Wenjie Wu](https://wenjiewu.com))

* **implement and code the design ideas for mini challenge one** with the undergrad developer. I architected the tech stack, controlled the versions (OMG I really should've used Git but it was complicated...), and suggested the code styles. My resolute choice on JavaScript and D3.js over PHP increased the performance and development efficiency indeed.

* **oversee the progress of mini challenge two** as we had quite a lot of ideation trials and its design iteration did not finalize until the last a couple of weeks.

* **support the team** also by organizing team lunch and activities such as canoeing and barbecues. Most team members were the first time participants. More importantly, they lacked experience of being involved into a serious large project driven by specific outcomes (The Award!). I made rigorous daily schedules while fought with them from the beginning to the end in order to keep everything on the right track without a KPI or corporate rules (As nobody could/would fire anybody while they should in school).

Okay, so much for the background. Now let's begin with the simplified version of the problem...


## Problem Statement
### Context
**The problem**: an ornithologist investigated the human activities' impact to an endangered specie of bird in a fictional natural preserve park. He hypothetically related the downhill change of the number of birds to 1) the air and noise pollution brought by the park traffic and 2) the campers' (even poachers') invasion. Our ornithologist need help to analyze the short and long term patterns and outliers of human life in the park through traffic sensor record in order to obtain the most impactful factors.

**The data**: The synthetic data came from the vehicle sensors installed at major visiting sites, road conjunctions, entrances, and gates in the park. Each vehicle that visited the park would be assigned a unique <em>car ID</em>. A sensor would record the <em>car ID</em> and the <em>timestamp</em> when the vehicle passed by the sensor. So, each vehicle passed by a sensor would generate a row of record in the dataset. The dataset contained 170,000+ entries over 13 months.

**[Detail context](http://vacommunity.org/VAST+Challenge+2017+MC1)**

### Data

* A 200 x 200 bitmap

  ![park map](https://va.tech.purdue.edu/vast2017/mc1/original_data_from_vast/Lekagul%20Roadways.bmp).

  White pixels for the roads and black pixels for the park land. All the sensor locations were tagged by pixel in colors and labeled accordingly. Map description can be found [here](https://va.tech.purdue.edu/vast2017/mc1/original_data_from_vast/Lekagul%20Preserve%20Description.docx).

* Sensor data in CSV format.

  Same vehicle entered the park multiple times maintained the <em>car ID</em>. There were seven types of vehicles in total. Data description can be found [here](https://va.tech.purdue.edu/vast2017/mc1/original_data_from_vast/Data%20Descriptions%20for%20MC1%20v2.docx).

  |timestamp|car-id|car-type|gate-name|
  |---|---|:---:|---|
  |2015-05-01 00:15:13|20151501121513-39|2| entrance4|
  |2015-05-01 00:32:47|20151501121513-39|2| entrance2|
  |2015-05-01 01:12:42|20151201011242-330|5|  entrance0|
  |2015-05-01 01:14:22|20151201011242-330|5|  general-gate1|
  |2015-05-01 01:17:13|20151201011242-330|5|  ranger-stop2|
  |2015-05-01 01:20:36|20151201011242-330|5|  ranger-stop0|
  |2015-05-01 01:24:11|20151201011242-330|5|  general-gate2|
  |2015-05-01 01:46:16|20151201011242-330|5|  entrance2|
  |2015-05-01 01:55:25|20155501015525-264|1|  entrance0|
  |2015-05-01 01:56:53|20155501015525-264|1|  general-gate1|
  |2015-05-01 01:59:27|20155501015525-264|1|  ranger-stop2|

## Data Preprocessing
* A Python module to process the bitmap park map to
  * clean the grey noises;
  * enlarge the map for human reading.
  ![processed map](https://va.tech.purdue.edu/vast2017/mc1/original_data_from_vast/Lekagul%20Roadways%20labeled%20v2.jpg)

* Imported the data to MySQL database and restructured to four tables.
  * A table with all records, adding index, total times of visit, and the temporal order of each visit.

    |id|timestamp|car-id|car-type|gate-name|tdate|ttime|times|sequence|
    |---|---|---|:---:|---|---|---|---|---|
    |1|2015-05-01 00:15:13|20151501121513-39|2|entrance4|2015-05-01|00:15:13|1|1|
    |2|2015-05-01 00:32:47|20151501121513-39|2|entrance2|2015-05-01|00:32:47|1|2|
    |3|2015-05-01 01:12:42|20151201011242-330|5|entrance0|2015-05-01|01:12:42|1|1|
    |4|2015-05-01 01:14:22|20151201011242-330|5|general-gate1|2015-05-01|01:14:22|1|2|
    |5|2015-05-01 01:17:13|20151201011242-330|5|ranger-stop2|2015-05-01|01:17:13|1|3|
    |6|2015-05-01 01:20:36|20151201011242-330|5|ranger-stop0|2015-05-01|01:20:36|1|4|
    |7|2015-05-01 01:24:11|20151201011242-330|5|general-gate2|2015-05-01|01:24:11|1|5|
    |8|2015-05-01 01:46:16|20151201011242-330|5|entrance2|2015-05-01|01:46:16|1|6|

  * A table compressed the visits from the same vehicle, showing total number of records, earliest and latest record time, and total times of visit.

    |car-id|car-type|counts|minTime|maxTime|times|
    |---|:---:|:---:|---|---|---|
    |20151501121513-39|2|2|2015-05-01 00:15:13|2015-05-01 00:32:47|1|
    |20151201011242-330|5|6|2015-05-01 01:12:42|2015-05-01 01:46:16|1|

  * A table with rows showing each visiting segment based on temporal order.

    |id|carid|stoptart|stopend|seconds|starttime|endtime|sequence|times|cartype|
    |---|---|---|---|:---:|---|---|:---:|:---:|:---:|
    |1|20150001010009-284|entrance3|general-gate1|1244|2015-07-01 13:00:09|2015-07-01 13:20:53|1|1|3|
    |2|20150001010009-284|general-gate1|ranger-stop2|159|2015-07-01 13:20:53|2015-07-01 13:23:32|2|1|3|
    |3|20150001010009-284|ranger-stop2|ranger-stop0|184|2015-07-01 13:23:32|2015-07-01 13:26:36|3|1|3|


  * A table specifically with rows indicating the duration of vehicles staying on camping sites and ranger stations.

    |carid|cartype|tdate|gatename|hours|
    |---|:---:|---|---|---|
    |20150002070024-376|2|2015-06-02|camping0|07:00:00|
    |20150003080058-668|3|2015-07-03|camping0|09:00:00|
    |20150011070002-311|3|2015-07-11|camping0|07:00:00|

## Features
The design process including ideations and iterations can be found [here](https://va.tech.purdue.edu/vast2017/presentation/Purdue-Zhou-Tang-Wu-Multi-final.pptx) or [here](https://vimeo.com/242499465). Below is some highlights.

* Map
  * Ideation

    Given a map and time-sequential records, it's a typical spatio-temporal visualization problem. However, the map has nothing to do with the real world, which means no map API is available. The provided map looks utterly unpleasant (believe me or not, there was team made visualization out of that black map image). Moreover, we did further investigation on the map and the data and found that some road information seemed redundant. For example, we don't care much about if the vehicle took east bound or west bound to travel from location A to B. We also found major road conjunctions in the park, where the vehicles must pass by when their trip are across the park area. We came up an idea to revamp the map to make it more visually pleasant withholding necessary information. One of the visual designers did this in Adobe Illustrator so that we can have the pair (x, y) of the sensor of each site/gate.

    Expressing a road that connects two sites is the next task. We wanted to distinguish the traffic direction (A to B or B to A) while maintaining the clarity. Google Map enables the traffic layer on each bounds of the road but one really needs to zoom in to tell the difference. We came up with an idea so please see iteration section.

  * Iteration

    ![map iteration](https://va.tech.purdue.edu/vast2017/mc1/design/map.png)
    1. First, we attempted forced-directed graph without force as the nodes' location should be fixed. The links between nodes represent two directions of the road by the arrows and their shades tell the traffic amount. The problem is the arrows overlapping with the node. A closer look,
    ![directed network](https://va.tech.purdue.edu/vast2017/mc1/design/directed-network.jpg)

    2. Then we updated the directed graph to geographic based map using curvature to indicate the traffic direction (assuming driving by the right). The position of the nodes, which are park sites, strictly follows the provided map. Color of the nodes indicates its type. The links, a.k.a the roads, are double coded by width and colors. However, the use of hues for amount are confusing and the links width are hard to differentiate.
    ![geo map](https://va.tech.purdue.edu/vast2017/mc1/design/geomap.png)

    3. Finally we finalized the design with the map below. We redistributed the positions of the gates for aesthetics. After all the exact location of each site is compromisable. We carefully designed the color of the nodes and used only one color for the links with various brightness to tell the traffic. The meaning of the width of the links change dynamically based on the maximum local value in the current view.
    ![final map](https://va.tech.purdue.edu/vast2017/mc1/design/finalmap.png)

* Node (a.k.a sites/gates)
  * Ideation

    We wanted to show



###

### [Finding](http://www.cs.umd.edu/hcil/varepository/VAST%20Challenge%202017/challenges/Mini-Challenge%201/entries/Purdue%20University/)

## License
This document is licensed under the [Creative Commons Attribution Share Alike 4.0 International](https://choosealicense.com/licenses/cc-by-sa-4.0/), and the underlying source code used to create this document is licensed under the [MIT license](LICENSE.md).
