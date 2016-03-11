- regex intro
- love
- more than love, i.e. beloved
- just love (i.e. not beloved)
- love, alone (i.e. not loved, loves)
- every word that starts with an 'l'
- every word that ends with an e
- every word that doesn't end in an e
- every word that doesn't have an e
  \b[^e\W]+\b
- every word at the end of a sentence
- every word at least 10 characters long
- all characters in which something is doubled

# Data cleaning
- Obama, Barack, Jr. Ted Cruz
- (555)-929-9233
- 1-()
- cleaning up raw text

# Extraction





# Turning data into visualization





# moving to the command-line



# Moving on to Trump tweets
- Show spreadsheet
- Show CSV headers
- csvcut -3 
- his favorite hash tag
- his most frequently tweeted people
- what links he tweeted
- every wrod he's tweeted that's not a hashtag, not a user, not 
- csvcut -c 'Text' realDonaldTrump--2016-01-05.csv | ack  -o '\S+' | ack -v '@|#|http|[0-9]' | ag -o '\w+'

- every tweet that Donald manually retweets
    - csvcut -c 'Text' realDonaldTrump--2016-01-05.csv | ack '"@\w+'
- every tweet that's straight from the donald
   - csvcut -c 'Text' realDonaldTrump--2016-01-05.csv | ack -v '"@\w+'
   - csvgrep -c 4 -ir '"@\w+|RT'  realDonaldTrump--2016-01-05.csv 


- every tweet in which the nyt was mentioned


## Data viz

Get year
Get year month
get year month day
  csvcut -c 2 ~/data/twitter-data/realDonaldTrump--2016-01-05.csv | ack -o '^\d{4}-\d+-\d+' | sort | uniq -c | ack  '^ *(\d+) (.+)' --output '$2,$1'
get the hour
  csvcut -c 2 ~/data/twitter-data/realDonaldTrump--2016-01-05.csv | ack -o '\s\d+(?=:)' | sort | uniq -c | ack  '^ *(\d+) +(.+)' --output '$2,$1'
------------------------




Regexes can't do simple data things, all it can do is count

Sometimes you just need filtering

Excel can't open this

ack 'GOLDMAN SACHS' > file.csv

Getting exact things

ack 'GOLDMAN...S' > file.csv

Data can be messy. Regex is just to explore

Sometimes I don't even know how to spell something

NASHTUCKET

How many f-bombs are in this database?

------------------------

# Sometimes counting is all you need

## Which trump committee


------

# Basic multiplication is good



-----


# Moving onto PDF land
# Sometimes you don't even need to count

### pdfstotext





# Simple data gathering text mining


- download a webpage, print it out as text
- curl the page
- ack for all lines with ".pdf"
- be a little more specific
- then pipe it into
- replace each line with "curl -o (whatever the line of text is)"


- List every number from 2000 to 2016
- for every 4 digit number (i.e.) year, 
    put the text curl and put a URL in front of it
- and then pipe it into the shell, i.e. run every command


- Look for JUSTICE THOMAS:
- Look for all JUSTICES
- Count up their mentions
- Look for all the times a JUSTICE made us laugh


------------------------

Clinton

- 'r u?'
- '?!?!'
- From: ...
- "From" happening once
- RELEASE IN PART
- ack '[?!]{2,}' data/docs/text/
- ack 'Subject:.+?[?!]{2,}' data/docs/text/
- day of week:
  ack -h -m1 'Sent. *\w+' data/docs/text/ 

- busy day of the week
  ack -h -m1 'Sent. *(\w+)' --output '$1' data/docs/text/  | sort | uniq -c | sort -rn


Sent:                             Friday, September 23, 2011 8:52 AM Classified by DAS, A/GIS, DoS on 12/31/2015 —????


# all emails with "CONFIDENTIAL"
ack -A2 '^ *CONFIDENTIAL *$' data/docs/text 


# all classes
  ack -h 'Class: (\w+)' data/docs/text --output '$1' | sort | uniq -c


Who is sending secret emails?
What confidential emails don't have SECRET or CONFIDENTIAL?
ack -il  'Classified by' data/docs/text | xargs ack -iL CONFIDENTIAL | wc -l
  21


ack -l  'Declassify' data/docs/text  | xargs ack -L 'CONFIDENTIAL'  | wc -l
  22


Be careful with capitalization, and word boundaries:

ack -l  '\bSECRET\b' | wc -l



# Medicare stuff

### All doctors who make 100+ operations and 100+

~~~sh
csvgrep -t \
     -c 'PROVIDER_TYPE' -r 'I' puf-cy2012.txt |
   csvgrep -c 'AVERAGE_MEDICARE_PAYMENT_AMT' -r '\d{3,}\.' |     
   csvgrep -c 'LINE_SRVC_CNT' -r '\d{3,}\.' \
   > temp/high-ops.csv
~~~


### All independent providers who receive $1M+

~~~sh
cat agg-puf-cy2012.csv |
  csvgrep -c 'Total Medicare Payment Amount' -r '\d{7,}\.'  | 
  csvgrep -c 'NPPES Entity Code' -m 'I' \
   > temp/hightotals.csv
~~~


### The break down of their specialties

~~~sh
csvcut -c 'NPPES Provider State' temp/hightotals.csv \
    | sort | uniq -c | sort -n
~~~


~~~sh
csvcut -c 'Provider Type' temp/hightotals.csv \
    | sort | uniq -c | sort -n
~~~

(focus on drug doctors with few patients)

~~~sh
cat agg-puf-cy2012.csv |
  csvgrep -c 'Total Drug Medicare Payment Amount' -r '\d{7,}\.'  | 
  csvgrep -c 'NPPES Entity Code' -m 'I' \
   > temp/drugtotals.csv
~~~



# Legislators

Setup:

~~~sh
csvgrep -c 'in_office' -m '1' legislators.csv 
  > current-legislators.csv
~~~



~~~sh
csvcut -c birthdate current-legislators.csv | 
    ack -o '\d{4}' | sort | uniq -c | 
    ack '(\d+) (\d+)' --output '$2,$1'
~~~


~~~sh
csvcut -c gender current-legislators.csv |
  sort | uniq -c
~~~



# astrazeneca

ack -o '\b[A-Z]{2}(?= +\$)' speaker-compensation-report-Full2010.txt | sort | uniq -c | sort -n | wc -l
