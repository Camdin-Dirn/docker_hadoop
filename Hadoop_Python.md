# Cloud Hadoop

## This is my solution to fix the word count
```
#!/usr/bin/python3
# -*-coding:utf-8 -*

import sys
import re

word_pattern = re.compile(r'^[a-zA-Z]+$')

for line in sys.stdin:
    line = line.strip()
    words = line.split()
    for word in words:
        if word_pattern.match(word):
            print(f'{word.lower()}\t1')

```
## This the mapred I used

```
mapred streaming \
  -input /user/sandbox/books \
  -output /user/sandbox/words \
  -mapper mapper.py \
  -reducer reducer.py \
  -file scripts/mapper.py \
  -file scripts/reducer.py
```

## I used all the same 3 books that were in the original demo.


## Big error I was getting was:

```
2024-06-13 03:17:21,289 INFO mapreduce.Job: Task Id : attempt_1718247426636_0001_m_000001_1, Status : FAILED
Error: java.lang.RuntimeException: PipeMapRed.waitOutputThreads(): subprocess failed with code 127
        at org.apache.hadoop.streaming.PipeMapRed.waitOutputThreads(PipeMapRed.java:326)
        at org.apache.hadoop.streaming.PipeMapRed.mapRedFinished(PipeMapRed.java:539)
        at org.apache.hadoop.streaming.PipeMapper.close(PipeMapper.java:129)
        at org.apache.hadoop.mapred.MapRunner.run(MapRunner.java:61)
        at org.apache.hadoop.streaming.PipeMapRunner.run(PipeMapRunner.java:34)
        at org.apache.hadoop.mapred.MapTask.runOldMapper(MapTask.java:466)
        at org.apache.hadoop.mapred.MapTask.run(MapTask.java:350)
        at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:178)
        at java.base/java.security.AccessController.doPrivileged(Native Method)
        at java.base/javax.security.auth.Subject.doAs(Subject.java:423)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1953)
        at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:172)
```

## I found out when I was trying to curl in my GitHub mapper it wasn't working so instead I did this and it worked:

```
echo -e '#!/usr/bin/env python3\n# -*- coding: utf-8 -*-\nimport sys\nimport re\n\nword_pattern = re.compile(r"^[a-zA-Z]+$")\n\nfor line in sys.stdin:\n    line = line.strip()\n    words = line.split()\n    for word in words:\n        if word_pattern.match(word):\n            print(f"{word.lower()}\\t1")' > scripts/mapper2.py
```
