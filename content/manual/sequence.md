+++
date = "2015-02-28T18:48:24-08:00"
title = "Sequence: A New Approach"
+++

<a href="#" class="image fit"><img src="/images/banner2.jpg" alt="" /></a>

A new approach taken by `sequence` breaks the art of extracting meaningful information from log messages into three parts: scanning, analyzing and parsing.

In `sequence`, the first critical function to understanding a log message is to convert it into a sequence of valid tokens, i.e., **meaningful** character strings. This function is called a _scanner_, but it can be called a _lexical analyzer_, _lexer_, or _tokenizer_. The `sequence` _scanner_ goes through log message sequentially while tokentizing each part of the message, without the use of regular expressions.

The _analyzer_ takes all the _sequences_ returned by the _scanner_ and attempt to automatically write the parsing rules. While it's impossible to automatically generate 100% of the rules, the goal is to get to 75% accuracy by leveraging hints provided by analysts.

Finally, the _parser_ will take the rules generated by the analyzer (and updated/corrected by some human) as well as the _sequences_ returned by the _scanner_ for log messages, and attempt to identify the matching rule. Based on the matching rule, the _parser_ will tag all the recognized tokens with a semantic tag, as defined by the user.

The approach taken by the _parser_ is a tree-based parsing approach, which is also used by [liblognorm](http://www.liblognorm.com/). This approach is much higher performance, as it only requires going through the log message only once. And because most, if not all, parsing rules are pre-built into a prefix tree, the engine just needs to walk through the tree once and be able to determine if there's a match.

Keep in mind that `sequence` is NOT a replacement for Splunk or ElasticSearch, or any of the log analytics solutions. It is a library and a tool that can help parse messages much faster. The result of `sequence` can be fed into these log analytics tools for further analysis.

### Other Concepts

The _scanner_ returns a _sequence_, or a list of tokens, which can then be consumed by the _analyzer_ or _parser_. 

A _Token_ is a piece of information extracted from the original log message. It is a struct that contains fields for _TokenType_, _FieldType_, _Value_.

A _TokenType_ indicates whether the token is a literal string (one that does not change), a variable string (one that could have different values), an IPv4 or IPv6 address, a MAC address, an integer, a floating point number, or a timestamp.

A _FieldType_ indicates the semantic meaning of the token. For example, a token could be a source IP address (%srcipv4%), or a user (%srcuser% or %dstuser%), an action (%action%) or a status (%status%).

### Performance 

Most of the tools out there perform at several thousands messages per second (MPS) on a single machine using regex parsers. For `sequence`, we are able to achieve over 100,000 MPS with average message size of 140 bytes, using a single CPU core. `sequence` can also scale almost linearly (with a slight penalty due to communications overhead) as more cores are added.

The following performance benchmarks are run on a single 4-core (2.8Ghz i7) MacBook Pro. The first file is a
bunch of sshd logs, averaging 98 bytes per message. The second is a Cisco ASA log file, averaging 180 bytes per message. Last is a mix of ASA, sshd and sudo logs, averaging 136 bytes per message.

```
  $ ./sequence bench parse -p ../../patterns/sshd.txt -i ../../data/sshd.all
  Parsed 212897 messages in 1.69 secs, ~ 126319.27 msgs/sec

  $ ./sequence bench parse -p ../../patterns/asa.txt -i ../../data/allasa.log
  Parsed 234815 messages in 2.89 secs, ~ 81323.41 msgs/sec

  $ ./sequence bench parse -d ../patterns -i ../data/asasshsudo.log
  Parsed 447745 messages in 4.47 secs, ~ 100159.65 msgs/sec
```

Performance can be improved by adding more cores:

```
  GOMAXPROCS=2 ./sequence bench parse -p ../../patterns/sshd.txt -i ../../data/sshd.all -w 2
  Parsed 212897 messages in 1.00 secs, ~ 212711.83 msgs/sec

  $ GOMAXPROCS=2 ./sequence bench parse -p ../../patterns/asa.txt -i ../../data/allasa.log -w 2
  Parsed 234815 messages in 1.56 secs, ~ 150769.68 msgs/sec

  $ GOMAXPROCS=2 ./sequence bench parse -d ../patterns -i ../data/asasshsudo.log -w 2
  Parsed 447745 messages in 2.52 secs, ~ 177875.94 msgs/sec
```

One may ask, who can POSSIBLY use such performance? Many organizations that I know are generating between 50-100M messages per second (MPS), that's only 1,200 MPS. However, some larger organizations I know are generating 60GB of Bluecoat logs per day, **8 years ago**!! That's a good 3,000 MPS assuming an average of 250 bytes per message. Even if log rate grows at 15%, that's over 10,000 MPS today. This already exceeds the capability of some parsers.

To run through another example, [at EMC, 1.4 billion log messages are generated daily on average, at a rate of one terabyte a day](http://www.covert.io/research-papers/security/Beehive%20-%20Large-Scale%20Log%20Analysis%20for%20Detecting%20Suspicious%20Activity%20in%20Enterprise%20Networks.pdf). That's 16,200 messages per second, and about 714 bytes per message. These EMC numbers are from 2013, so they have most likely increased by now.

The `sequence` parser, with a single CPU core, can process about 270,000 MPS for messages averaging 98 bytes. Assuming the performance is linear compare to the message size (which is pretty close to the truth), we can parse 37,000 MPS for messages averaging 714 bytes. That's twice of what's needed for EMC, using a SINGLE CORE!

Obviously one can throw more hardware at solving the scale problem with the existing tools, but then again, why do that if you don't need to. Just because you have the hardware doesn't mean you should waste the money! Besides, there are much more interesting analytics problems your hardware can be used for than just tokenizing a message.
