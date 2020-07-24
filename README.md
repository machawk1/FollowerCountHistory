# Twitter Follower Count History via Web Archives
This is a Python module that collects Twitter follower count from the web archives using [MemGator](https://github.com/oduwsdl/MemGator) for a given Twitter handle. The module parses the follower count by identifying various CSS Selectors that match the follower count element on the historical Twitter pages for almost every major overhaul their page layout has gone through. The program collects all of the memento data points by default.

## Installation and Usage
### Dependencies
* Python 3
* R* (to create graph)
* bs4
* warcio
* requests

*optional

### Usage
```shell
$ git clone https://github.com/oduwsdl/FollowerCountHistory.git
$ cd FollowerCountHistory
$ pip install -r requirements.txt
$  fch [-h] [--st] [--et] [--freq] [--out | --debug] <twitter-username-without-@>
```

This will create a new folder with the name: <twitter-username-without-@> and create three files in this folder.
* A .csv file that contains the data collected with associated timestamps
* A .csv file that contains data for mementos follower count was not extracted from
* A .png file that contains a line chart of the data collected with X axis representing time


To just create the graph from a csv file
```shell
$ git clone https://github.com/oduwsdl/FollowerCountHistory.git
$ cd FollowerCountHistory
$ Rscript --vanilla follower_count_linechart.R <twitter-username-without-@>
```

### Docker

We have published a docker image at [oduwsdl/fch](https://hub.docker.com/r/oduwsdl/fch), which can be used to run this tool as following:

```
$ docker container run --rm -it -v </OUTPUT/DIR>:/app/<TWITTER_HANDLE> oduwsdl/fch [OPTIONS] <TWITTER_HANDLE>
```

### Options
```
Follower Count History (fch)

positional arguments:
  thandle     Enter a Twitter handle

optional arguments:
  -h, --help  show this help message and exit
  --st        Memento start datetime format (in RFC 1123 datetime format)
  --et        Memento end datetime (in RFC 1123 datetime format)
  --freq      Sampling frequency of mementos(in seconds)
  --out       Path for follower count output in CSV format
  --debug     Debug Mode
```
## Output

The program can generate output in two formats: firstly, JSON Object to stdout and secondly, CSV files and follower graphs in the output folder.

### Output to stdout 

Command to return JSON object as output 
```shell
$ ./fch --st=20200101000000 --et=20200331000000  --freq=2592000 joebiden
```
JSON Output
```json
[{
	"MementoTimestamp": "20200101001959",
	"URI-M": "https://web.archive.org/web/20200101001959/https://twitter.com/JoeBiden",
	"FollowerCount": 4048208
}, {
	"MementoTimestamp": "20200131120028",
	"URI-M": "https://web.archive.org/web/20200131120028/https://twitter.com/joebiden",
	"FollowerCount": 4142510
}, {
	"MementoTimestamp": "20200301001210",
	"URI-M": "https://web.archive.org/web/20200301001210/https://twitter.com/JoeBiden/",
	"FollowerCount": 4202148
}]
```
### Output to files

Command to return csv files as output

```shell
$ ./fch --st=20200101000000 --et=20200331000000  --freq=2592000 --out joebiden
```

* `<TwitterHandle>.csv`: CSV file with fields, MementoTimestamp (dateTime of the memento), URI-M (link to the memento), FollowerCount(follower count from URI-M), and DateTime (memento datetime in [RFC 1123](http://www.csgnetwork.com/timerfc1123calc.html) format).
* `<TwitterHandle>_analysis.csv`: CSV file with fields, MementoTimestamp (datetime of the memento), URI-M (link to the memento), FollowerCount(follower count from the URI-M), DateTime (memento datetime in [RFC 1123](http://www.csgnetwork.com/timerfc1123calc.html) format), AbsRelative (follower count increase/decrease w.r.t the first memento), AbsPrevRelative (follower Count increase/decrease w.r.t the previous memento), PerRelative (pecentage increase/decrease in follower count w.r.t the first memento), PerPrevRelative (pecentage increase/decrease in follower count w.r.t the previous memento), RateRelative (daily Twitter follower growth rate w.r.t the first memento), and RatePrevRelative (daily Twitter follower growth rate w.r.t the previous memento). 

Command to create graphs for each handle

```r
$ Rscript --vanilla twitterFollowerCount.R <twitter-username-without-@>
```

(List of Graphs for each Twitter handle):

File Name| Description
---------|------------
`<Twitterhandle>`-follower-count.jpg|                shows Twitter follower growth over time
`<Twitterhandle>`-follower-growth-relative.jpg|      shows Twitter follower growth w.r.t. previous memento
`<Twitterhandle>`-follower-growth.jpg|               shows absolute number and pecentage Twitter follower growth w.r.t to first memento
`<Twitterhandle>`-follower-perc-growth-relative.jpg| shows Twitter follower growth over time w.r.t. previous memento in percentage
`<Twitterhandle>`-follower-rate-relative.jpg|        shows new followers added per day w.r.t. previous memento
`<Twitterhandle>`-follower-rate.jpg|                 shows new followers added per day w.r.t. first memento
