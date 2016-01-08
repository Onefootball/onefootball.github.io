---
layout: post
title: S3downloader - AWS s3 cli tool
permalink: /s3downloader/
tags: [aws, s3, download, cli, tool]
categories: golang, aws, s3
author_id: smakarski
---

Onefootball app is designed with the goal to deliver the best suitable football content for each individual user. In order to achieve this our backend hourly handles great volumes of data. It's a good practice to keep track of processed data, especially when it comes to football results, where sequence of parsed files is equally important to the content.

__[s3downloader](https://github.com/motain/s3downloader) written in [go](https://golang.org) comes in place when you need to quickly find or download needed file from s3__.

#### Case study

Imagine you have a football match between teams **A** and **B**. Backend updates its result based on the third-party provider feed with the following naming pattern `match-a-b-results.xml`. Let's assume the feed is updated every 30 seconds and we archive every delta update without overwriting the file into a separate sub-folder. In the end of the game our S3 archive would look like this:

```bash
- 2016-01-05/143000/match-a-b-results.xml
- 2016-01-05/143030/match-a-b-results.xml
- 2016-01-05/143100/match-a-b-results.xml
- 2016-01-05/143130/match-a-b-results.xml
- ...
- ...
- ...
- 2016-01-05/171430/match-a-b-results.xml
- 2016-01-05/171500/match-a-b-results.xml
```

#### Getting up and running

In order to run the command you need to have **go** installed on your machine. You can [install](https://golang.org/dl/) **go** either from source or run an installation package.

Once you have installed and configured **go**, run `go get -t -u github.com/motain/s3downloader` - this will fetch the project source and install it on your machine.

In order to make sure you have correctly install the tool run the following cmd from terminal:

```bash
s3downloader -h
```

Expected result is

```bash
Usage of s3downloader:
  -bucket string
        Download bucket
  -dir string
        Target local dir (default "downloads-s3")
  -dry-run
        Find only flag - no download
  -p    Prepend downloaded file name with last-modified timestamp
  -prefix string
        Bucket download path
  -regexp string
        Item name regular expression (default ".*")

```

##### Configuration

Create a config file based on template.

```bash
cd $GOPATH/src/github.com/motain/s3downloader
cp config.json.dist config.json
```

Provide your valid AWS credentials in `config.json`. Consider a valid s3 [region](http://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region).

### Example 1 - find all

Find all archived feeds from `2016-01-05`

```bash
s3downloader -bucket=archive -prefix=2016-01-05 -dry-run
```

would output:

```
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/171500/match-a-b-results.xml
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/143000/
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/143030/
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/143100/match-a-b-results.xml
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/171400/
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/143030/match-a-b-results.xml
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/143130/
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/143130/match-a-b-results.xml
INFO: 2016/01/05 15:13:58 Found: s3://archive/2016-01-05/171400/match-a-b-results.xml
```

### Example 2 - download all

Download all files from 2016-01-05 to a local directory `$HOME/s3test` (will be created if does not exist), prepend downloaded file name with last-modified timestamp. 

```bash
s3downloader -bucket=archive -prefix=2016-01-05 -p -dir=/home/username/s3test
```

you should see similar info messages in terminal:

```bash
INFO: 2016/01/05 15:20:16 Downloading s3://archive/2016-01-05/171400/match-a-b-results.xml to /Users/username/s3test/2016-01-05T14:11:26Z_match-a-b-results.xml...
INFO: 2016/01/05 15:20:16 Downloading s3://archive/2016-01-05/143030/match-a-b-results.xml to /Users/username/s3test/2016-01-05T14:10:33Z_match-a-b-results.xml...
INFO: 2016/01/05 15:20:16 Downloading s3://archive/2016-01-05/143100/match-a-b-results.xml to /Users/username/s3test/2016-01-05T14:10:48Z_match-a-b-results.xml...
INFO: 2016/01/05 15:20:16 Downloading s3://archive/2016-01-05/143130/match-a-b-results.xml to /Users/username/s3test/2016-01-05T14:11:09Z_match-a-b-results.xml...
INFO: 2016/01/05 15:20:16 Downloading s3://archive/2016-01-05/143000/match-a-b-results.xml to /Users/username/s3test/2016-01-05T14:10:18Z_match-a-b-results.xml...
INFO: 2016/01/05 15:20:16 Downloading s3://archive/2016-01-05/171500/match-a-b-results.xml to /Users/username/s3test/2016-01-05T14:12:00Z_match-a-b-results.xml...
``` 

### Example 3 - download or find by regexp

```bash
// Find results for all games
s3downloader -bucket=archive -regexp=^*results\\.xml$ -dry-run

// Download all results after 170000
s3downloader -bucket=archive -p -regexp=^.*17.*\\/.*results\\.xml$
```

#### Summary

We wrote the s3downloader because we were not happy with the performance and complexity the AWS web interface or other clients suggest. Due to go concurrency pattern utilization s3downloader allows to quickly scan even very big s3 buckets. The tool helps us a lot in our everyday work and we hope it can be useful for you as well.
