# Cloud Hadoop

## This is my solution to fix the word count
```
import sys
import re

word_pattern = re.compile(r'[a-zA-Z]+')

for line in sys.stdin:
    line = line.strip()
    words = word_pattern.findall(line)
    for word in words:
        print(f'{word}\t1')
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
