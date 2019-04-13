---
id: 27
title: 'Prhymer: How It Came To Be'
date: 2017-03-15T20:30:11+00:00
author: Thomas Lisankie
layout: post
guid: http://tomlisankie.com/blog/?p=27
permalink: /2017/03/15/prhymer-how-it-came-to-be/
categories:
  - Uncategorized
tags:
  - algorithm
  - prhymer
  - pronunciation similarity
---
### _If you&#8217;re not interested in the history of why I came to build this, go to the post on [How It Works](http://tomlisankie.com/blog/2017/03/15/prhymer-how-it-works/)._

Sometime towards the end of high school in early 2015, I became quite interested in the idea of creating a program that could write rap lyrics. In the process of exploring what it might take to build such a thing, I inevitably came to the problem of finding rhyming words. Finding rhyming words would involve creating some sort of way of finding how well any two given words rhymed with one another.

Out of all the tasks that would need to be accomplished to build such a thing, this problem seemed to be the simplest so I decided to tackle it first. &#8220;What could be so complicated, it&#8217;s just a matter of finding alignments between strings?&#8221; This, of course, ended up being fairly naive of me. English&#8217;s orthography-to-phonology translation happens to be fairly awful so often you&#8217;ll have two words that clearly rhyme but do not have similar sequences of characters that would lead you to believe that they rhyme.

Tackling the fact that English has fairly awful orthography-to-phonology means you&#8217;re gonna have to pursue one of two options:

  1. Build some sort of translator that would take substrings of a word that are known to typically create a certain sound and translate them to symbols that represent that sound (what I would later come to find out are called_ __phones_).
  2. Find some sort of database that already has every English word&#8217;s spelling along with their symbolized pronunciations.

Not really even thinking that someone might have already done it and I could save myself the work, I set out to build number 1. This ended up being both a fairly straightforward process however with comparatively lousy results. Find common substrings that seem to usually create certain sounds and then write them down in an if-else cluster in a loop where the phonetic translation will be generated. Then you can move on to comparing the sequences of phones you just generated. There are a few problems that arise when building such a translator for English though:

  * Many characters in the spelling of a word will have their phonetic output changed by the characters or sounds that came before it (sometimes even after). Finding all these cases is fairly troublesome and cases are encountered where the same environment produces two different sounds in different words. This means that the phonetic output of a word cannot be a function of the way it&#8217;s spelled.
  * English orthography-to-phonology is horrible for the aforementioned reason.

Eventually realizing this, I took to Google to see if someone had already made an orthographic-to-phonological English dictionary. And I ended up finding out that [someone(s) had](http://www.speech.cs.cmu.edu/cgi-bin/cmudict). This was a fairly bittersweet moment. Bitter because I had already invested some time here and there for the past few weeks in order to try and make this translator and I didn&#8217;t want to see all my hard work being rendered essentially useless. Sweet because I no longer had to worry about all the cases where the phonetic transcription would not be correct and thus the measure of how two words rhyme could be completely off.

Now that I had an accurate English orthography-to-phonology dictionary, it was time to addressing the real meat of the problem: **how do you compare two sequences of sound to see how well they rhyme?**
  
Looking back, I should have just googled &#8220;sequence comparison algorithms&#8221; in order to draw some inspiration for solving this problem (or even better, being able to just directly adopt one to solve the problem). But I did not and ended up taking a fairly non-straightforward process to just end up creating a new sequence comparison algorithm. The most recent version (and the one that is presented in this post) only came about in the last few months in some spare time.

Even with all this switching of _how_ I would do this, there was at least one common problem that arose in all versions: how are two elements (phones) considered as being a similar-sounding match and how much weight will each of these matches or non-matches hold? Upon some observation and inspection, I was finding that rhyming words were much more likely to sound like they rhyme if they have similar sequences of the same vowel sounds. Thus, it seemed to make more sense to put a higher value on having similar vowel sounds than similar consonant sounds.

There was also the problem of having two long sequences being compared usually yielding scores that would be impossible for any two shorter sequences to have. For example, comparing two sequences of sizes 2 and 3 will never yield as high of a score as two sequences of sizes 27 and 33. Thus it&#8217;s important to find how well two words rhyme based on a fraction/percentage rather than a raw score. Say the comparison of the former sequences yield a score of 2 while that comparison of the latter sequences yields a score of 22. Just because 22 > 2 doesn&#8217;t mean the second pair rhymes better. In fact, the two comparisons share the same _fraction_ of a score (2/3 and 22/33 are both approximatively equal to 0.67). Thus they both have about 67% commonality.

<div>
</div>