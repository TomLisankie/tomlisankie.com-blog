---
id: 64
title: 'Prhymer: How It Works'
date: 2017-03-15T20:48:10+00:00
author: Thomas Lisankie
layout: post
guid: http://tomlisankie.com/blog/?p=64
permalink: /2017/03/15/prhymer-how-it-works/
categories:
  - Uncategorized
---
Prhymer is an algorithm I originally made for comparing two words (defined in this case to simply be sequences of phonemes) in order to see how well they rhyme i.e. how similar they are in pronunciation. (Links to my implementations can be found [here](http://tomlisankie.com/projects/prhymer/)). However, it can be generalized to be a [global sequence alignment algorithm](https://en.wikipedia.org/wiki/Sequence_alignment#Global_and_local_alignments). The steps for this algorithm are as follows:

  1. Two sequences of elements are taken as input. We will label the shorter sequence as  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />and the longer sequence as <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35099fe3acb43f598fa0fec9d55e0844_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#76;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />
  
    (if the sequences are of the same length it is arbitrary which sequence is labeled as  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />and which is labeled as <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35099fe3acb43f598fa0fec9d55e0844_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#76;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />). Let  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-95d052bbdff5939edc0f8d73be3abe3f_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#109;" title="Rendered by QuickLaTeX.com" height="8" width="15" style="vertical-align: 0px;" />be the number of elements in <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" /> and let <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-c972962b2b6b29aedf521d48a070fff3_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#110;" title="Rendered by QuickLaTeX.com" height="8" width="11" style="vertical-align: 0px;" /> be the number of elements in <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35099fe3acb43f598fa0fec9d55e0844_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#76;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />.
  2. Find the Cartesian product of <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" /> and  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35099fe3acb43f598fa0fec9d55e0844_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#76;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />and let&#8217;s call it <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-3be05a969a6ec808074a0ddcde01e03c_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#80;" title="Rendered by QuickLaTeX.com" height="12" width="14" style="vertical-align: 0px;" />: <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-c38132ec0d08c897af0d888bd8176f49_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#80;&#32;&#61;&#32;&#92;&#108;&#101;&#102;&#116;&#32;&#92;&#123;&#32;&#83;&#32;&#92;&#116;&#105;&#109;&#101;&#115;&#32;&#76;&#32;&#61;&#32;&#40;&#115;&#44;&#32;&#108;&#41;&#92;&#58;&#32;&#124;&#92;&#58;&#32;&#115;&#32;&#92;&#105;&#110;&#32;&#83;&#44;&#32;&#92;&#58;&#32;&#108;&#32;&#92;&#105;&#110;&#32;&#76;&#32;&#92;&#114;&#105;&#103;&#104;&#116;&#32;&#92;&#125;" title="Rendered by QuickLaTeX.com" height="19" width="264" style="vertical-align: -5px;" />
  3. Now let&#8217;s define a matrix <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-f200273c73dab75f221b7a72aa083e3b_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#77;" title="Rendered by QuickLaTeX.com" height="12" width="19" style="vertical-align: 0px;" /> on the product <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-3be05a969a6ec808074a0ddcde01e03c_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#80;" title="Rendered by QuickLaTeX.com" height="12" width="14" style="vertical-align: 0px;" /> found in step 2 such that <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-c463c62981eec4e55a85d4ad2d315016_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#77;&#32;&#61;&#32;&#92;&#98;&#101;&#103;&#105;&#110;&#123;&#98;&#109;&#97;&#116;&#114;&#105;&#120;&#125;&#32;&#38;&#32;&#40;&#115;&#95;&#123;&#48;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#48;&#125;&#41;&#32;&#92;&#59;&#92;&#59;&#32;&#92;&#99;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#32;&#40;&#115;&#95;&#123;&#48;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#110;&#45;&#49;&#125;&#41;&#32;&#38;&#32;&#92;&#92;&#32;&#38;&#32;&#92;&#118;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#32;&#92;&#100;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#32;&#92;&#118;&#100;&#111;&#116;&#115;&#32;&#38;&#32;&#92;&#92;&#32;&#38;&#32;&#40;&#115;&#95;&#123;&#109;&#45;&#49;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#48;&#125;&#41;&#32;&#92;&#59;&#92;&#59;&#32;&#92;&#99;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#32;&#40;&#115;&#95;&#123;&#109;&#45;&#49;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#110;&#45;&#49;&#125;&#41;&#32;&#38;&#32;&#92;&#101;&#110;&#100;&#123;&#98;&#109;&#97;&#116;&#114;&#105;&#120;&#125;" title="Rendered by QuickLaTeX.com" height="76" width="314" style="vertical-align: -34px;" />
  4. The row echelon form will then be taken on matrix <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-f200273c73dab75f221b7a72aa083e3b_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#77;" title="Rendered by QuickLaTeX.com" height="12" width="19" style="vertical-align: 0px;" />. This is done to preserve order and make sure no &#8220;best&#8221; ordered pairs are found on one row that are ahead of best ordered pairs found in later rows as we will see later. Let&#8217;s call the row echelon form  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-45b2f360c5b52f6c4702720ef7bec03c_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#82;" title="Rendered by QuickLaTeX.com" height="12" width="14" style="vertical-align: 0px;" />such that <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-59a4748b742187323ba187ddc31bb142_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#82;&#32;&#61;&#32;&#92;&#98;&#101;&#103;&#105;&#110;&#123;&#98;&#109;&#97;&#116;&#114;&#105;&#120;&#125;&#32;&#38;&#32;&#40;&#115;&#95;&#123;&#48;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#48;&#125;&#41;&#32;&#92;&#59;&#92;&#59;&#32;&#92;&#99;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#32;&#40;&#115;&#95;&#123;&#48;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#110;&#45;&#49;&#125;&#41;&#32;&#38;&#32;&#92;&#92;&#32;&#38;&#32;&#92;&#118;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#32;&#92;&#100;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#92;&#59;&#32;&#92;&#118;&#100;&#111;&#116;&#115;&#32;&#38;&#32;&#92;&#92;&#32;&#38;&#32;&#40;&#115;&#95;&#123;&#109;&#45;&#49;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#110;&#45;&#109;&#45;&#49;&#125;&#41;&#32;&#92;&#59;&#92;&#59;&#32;&#92;&#99;&#100;&#111;&#116;&#115;&#32;&#92;&#59;&#92;&#59;&#32;&#40;&#115;&#95;&#123;&#109;&#45;&#49;&#125;&#44;&#92;&#58;&#32;&#108;&#95;&#123;&#110;&#45;&#49;&#125;&#41;&#32;&#38;&#32;&#92;&#101;&#110;&#100;&#123;&#98;&#109;&#97;&#116;&#114;&#105;&#120;&#125;" title="Rendered by QuickLaTeX.com" height="76" width="351" style="vertical-align: -34px;" />
  5. We then apply a similarity/cost function,  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-4a6a1a29318309672acf99704b5703a3_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#99;&#40;&#40;&#115;&#44;&#108;&#41;&#41;" title="Rendered by QuickLaTeX.com" height="18" width="56" style="vertical-align: -4px;" />to all ordered pairs in <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-45b2f360c5b52f6c4702720ef7bec03c_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#82;" title="Rendered by QuickLaTeX.com" height="12" width="14" style="vertical-align: 0px;" />.
  6. Starting at row <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-95d052bbdff5939edc0f8d73be3abe3f_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#109;" title="Rendered by QuickLaTeX.com" height="8" width="15" style="vertical-align: 0px;" />, we find the ordered pair within the range  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-972003edbcd37ab224ff577f7d669a45_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#48;&#32;&#92;&#108;&#101;&#113;&#32;&#105;" title="Rendered by QuickLaTeX.com" height="15" width="39" style="vertical-align: -3px;" />where, initially,  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-1e432c94637061c416641846150cb4ec_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#105;&#32;&#61;&#32;&#110;" title="Rendered by QuickLaTeX.com" height="12" width="41" style="vertical-align: 0px;" />with the highest similarity (or lowest if it&#8217;s a cost function) and add it to the similarity/cost values of the ordered pairs in the row above it (in this case row <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-19985356f2ebafe23198a171ba714423_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#109;&#45;&#49;" title="Rendered by QuickLaTeX.com" height="13" width="45" style="vertical-align: -1px;" />).
  7. We set  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-67494ad7556bce61778e1cf04bd3006b_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#105;" title="Rendered by QuickLaTeX.com" height="12" width="6" style="vertical-align: 0px;" />to whatever index that the ordered pair from step 6 was found at to a list of indices  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-0483fc2127dc46ac432b570290161e5d_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#73;" title="Rendered by QuickLaTeX.com" height="12" width="9" style="vertical-align: 0px;" />that we will later apply a gap penalty function to.
  8. Steps 6-7 are repeated until all rows have been examined. The total similarity/cost value that was found is then stored in <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-38ae40ac683a606b2770cce5b8ca8237_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#116;" title="Rendered by QuickLaTeX.com" height="12" width="6" style="vertical-align: 0px;" />.
  9. A gap penalty function  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-5250cbfab192d7d52affc80ef9fedcfe_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#112;&#40;&#73;&#41;" title="Rendered by QuickLaTeX.com" height="18" width="32" style="vertical-align: -4px;" />is then applied to the list of indices  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-0483fc2127dc46ac432b570290161e5d_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#73;" title="Rendered by QuickLaTeX.com" height="12" width="9" style="vertical-align: 0px;" />and subtracted from  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-38ae40ac683a606b2770cce5b8ca8237_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#116;" title="Rendered by QuickLaTeX.com" height="12" width="6" style="vertical-align: 0px;" />so that  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35039793b5e01ebf394f7b0608047964_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#102;&#32;&#61;&#32;&#116;&#32;&#45;&#32;&#112;&#40;&#73;&#41;" title="Rendered by QuickLaTeX.com" height="18" width="93" style="vertical-align: -4px;" />where <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-d87eed044670eb60b7d80f75e60d6aaa_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#102;" title="Rendered by QuickLaTeX.com" height="16" width="10" style="vertical-align: -4px;" />is the final similarity/cost score between the sequences  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />and <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35099fe3acb43f598fa0fec9d55e0844_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#76;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />.
 10. However, finding the similarity score is not enough since scores will vary relative to the sizes of the two sequences. For example, two small yet highly similar sequences may have a similarity score of 3 while two large yet dissimilar sequences may have a similarity score of 10. Thus, it is important to find a percentage of similarity for any two given sequences. To do this we simply find the similarity score (steps 1-9) of sequence  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35099fe3acb43f598fa0fec9d55e0844_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#76;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />on itself (let&#8217;s call this<img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-cd244aecfeaff20796a3225a17f333e6_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#104;" title="Rendered by QuickLaTeX.com" height="13" width="10" style="vertical-align: 0px;" />) and divide  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-d87eed044670eb60b7d80f75e60d6aaa_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#102;" title="Rendered by QuickLaTeX.com" height="16" width="10" style="vertical-align: -4px;" />by  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-cd244aecfeaff20796a3225a17f333e6_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#104;" title="Rendered by QuickLaTeX.com" height="13" width="10" style="vertical-align: 0px;" />to get the similarity (or dissimilarity if it was a cost function that was applied) ratio which we can then turn into a percentage where <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-49a2bdb80083d650a843219c2f55dc76_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#114;&#32;&#61;&#32;&#92;&#102;&#114;&#97;&#99;&#123;&#102;&#125;&#123;&#104;&#125;&#92;&#99;&#100;&#111;&#116;&#32;&#49;&#48;&#48;&#32;&#92;&#37;" title="Rendered by QuickLaTeX.com" height="23" width="98" style="vertical-align: -6px;" />
 11. We&#8217;re not done yet. Some sequences may actually have a higher similarity percentage by removing elements starting from the first element of  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />and repeating steps 1-10 on the newly formed  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />and the original  <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-35099fe3acb43f598fa0fec9d55e0844_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#76;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />(this is especially important for the original purpose of Prhymer since some shorter words may rhyme in the middle of the longer). Once there are no more elements in <img src="http://tomlisankie.com/blog/wp-content/ql-cache/quicklatex.com-df3618c96b55f38439e047834d452263_l3.png" class="ql-img-inline-formula quicklatex-auto-format" alt="&#83;" title="Rendered by QuickLaTeX.com" height="12" width="12" style="vertical-align: 0px;" />, the highest similarity percentage is chosen as the &#8220;true&#8221; similarity score.

* * *

Now that we have a step by step procedure, let&#8217;s do an example on two words. But before we do our example, we must define our similarity function for an ordered pair of phonemes as well as our gap penalty function. The similarity function will award a score based on the following table:

<table style="width: 625px; height: 193px;" border="2" cellpadding="2">
  <tr>
    <td style="width: 120px;">
       <strong>Comparison Case</strong>
    </td>
    
    <td style="width: 502px;">
       <strong>Number of Points Awarded</strong>
    </td>
  </tr>
  
  <tr>
    <td style="width: 120px;">
       Vowel and Vowel
    </td>
    
    <td style="width: 502px;">
       5 &#8211; (number of features they don&#8217;t share) &#8211; (difference in stress)
    </td>
  </tr>
  
  <tr>
    <td style="width: 120px;">
       Consonant and Consonant
    </td>
    
    <td style="width: 502px;">
       2 &#8211; (0.15 * (number of features they don&#8217;t share)) &#8211; (0.1 if they both aren&#8217;t voiced) &#8211; (1 if they both aren&#8217;t sonorous)
    </td>
  </tr>
  
  <tr>
    <td style="width: 120px;">
       Vowel and Consonant
    </td>
    
    <td style="width: 502px;">
      (0.1 * (the number of features that they have in common)) + (0.1 if they both are voiced) + (1 if they&#8217;re both sonorous)
    </td>
  </tr>
</table>

The features that are referred to are [distinctive features](https://en.wikipedia.org/wiki/Distinctive_feature). A list of the features that are used in the implementation can be found [here](http://tomlisankie.com/pronunciation-algorithm-resources/feature-to-number-reference.txt). In addition, the phonemes that are used for words come from the [CMU Pronouncing Dictionary](http://www.speech.cs.cmu.edu/cgi-bin/cmudict) and the list of the phonemes and their respective features that will be used can be found [here](http://tomlisankie.com/pronunciation-algorithm-resources/features.txt).

The gap penalty function is as follows: ((number of spaces between indices of ordered pairs) * 0.25) + log(1 + number of spaces from first ordered pair used to 0) + log(number of spaces from last ordered pair used to length of longest sequence + 1).

Using this, let&#8217;s go through an example using the words &#8220;ship&#8221; (SH IH1 P) and &#8220;shifter&#8221; (SH IH1 F T ER0). Let&#8217;s just skip right ahead to the matrix found on the Cartesian product of these two sequences:

<table class="tg">
  <tr>
    <th class="tg-031e">
    </th>
    
    <th class="tg-e3zv">
      SH
    </th>
    
    <th class="tg-e3zv">
      IH1
    </th>
    
    <th class="tg-e3zv">
      F
    </th>
    
    <th class="tg-e3zv">
      T
    </th>
    
    <th class="tg-e3zv">
      ER0
    </th>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>SH</strong>
    </td>
    
    <td class="tg-031e">
      (SH, SH)
    </td>
    
    <td class="tg-031e">
      (SH, IH1)
    </td>
    
    <td class="tg-031e">
      (SH, F)
    </td>
    
    <td class="tg-031e">
      (SH, T)
    </td>
    
    <td class="tg-031e">
      (SH, ER0)
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>IH1</strong>
    </td>
    
    <td class="tg-031e">
      (IH1, SH)
    </td>
    
    <td class="tg-031e">
      (IH1, IH1)
    </td>
    
    <td class="tg-031e">
      (IH1, F)
    </td>
    
    <td class="tg-031e">
      (IH1, T)
    </td>
    
    <td class="tg-031e">
      (IH1, ER0)
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>P</strong>
    </td>
    
    <td class="tg-031e">
      (P, SH)
    </td>
    
    <td class="tg-031e">
      (P, IH1)
    </td>
    
    <td class="tg-031e">
      (P, F)
    </td>
    
    <td class="tg-031e">
      (P, T)
    </td>
    
    <td class="tg-031e">
       (P, ER0)
    </td>
  </tr>
</table>

Let&#8217;s take the row echelon form on it:

<table class="tg">
  <tr>
    <th class="tg-031e">
    </th>
    
    <th class="tg-e3zv">
      SH
    </th>
    
    <th class="tg-e3zv">
      IH1
    </th>
    
    <th class="tg-e3zv">
      F
    </th>
    
    <th class="tg-e3zv">
      T
    </th>
    
    <th class="tg-e3zv">
      ER0
    </th>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>SH</strong>
    </td>
    
    <td class="tg-031e">
      (SH, SH)
    </td>
    
    <td class="tg-031e">
      (SH, IH1)
    </td>
    
    <td class="tg-031e">
      (SH, F)
    </td>
    
    <td class="tg-031e">
      (SH, T)
    </td>
    
    <td class="tg-031e">
      (SH, ER0)
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>IH1</strong>
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      (IH1, IH1)
    </td>
    
    <td class="tg-031e">
      (IH1, F)
    </td>
    
    <td class="tg-031e">
      (IH1, T)
    </td>
    
    <td class="tg-031e">
      (IH1, ER0)
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>P</strong>
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      (P, F)
    </td>
    
    <td class="tg-031e">
      (P, T)
    </td>
    
    <td class="tg-031e">
       (P, ER0)
    </td>
  </tr>
</table>

And let&#8217;s replace the ordered pairs with their numeric value after the similarity function is applied to all of them:

<table class="tg">
  <tr>
    <th class="tg-031e">
    </th>
    
    <th class="tg-e3zv">
      SH
    </th>
    
    <th class="tg-e3zv">
      IH1
    </th>
    
    <th class="tg-e3zv">
      F
    </th>
    
    <th class="tg-e3zv">
      T
    </th>
    
    <th class="tg-e3zv">
      ER0
    </th>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>SH</strong>
    </td>
    
    <td class="tg-031e">
      2
    </td>
    
    <td class="tg-031e">
      0.1
    </td>
    
    <td class="tg-031e">
      1.3
    </td>
    
    <td class="tg-031e">
      1.15
    </td>
    
    <td class="tg-031e">
      0.3
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>IH1</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      5
    </td>
    
    <td class="tg-031e">
      0.1
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      1
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>P</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      1.3
    </td>
    
    <td class="tg-031e">
      1.45
    </td>
    
    <td class="tg-031e">
    </td>
  </tr>
</table>

And now let&#8217;s do steps 6-7 (detailed above) and keep track of the indices of the chosen ordered pairs. In addition a &#8220;-&#8221; will replace ordered pairs that can no longer be accessed along the way:

<table class="tg">
  <tr>
    <th class="tg-031e">
    </th>
    
    <th class="tg-e3zv">
      SH
    </th>
    
    <th class="tg-e3zv">
      IH1
    </th>
    
    <th class="tg-e3zv">
      F
    </th>
    
    <th class="tg-e3zv">
      T
    </th>
    
    <th class="tg-e3zv">
      ER0
    </th>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>SH</strong>
    </td>
    
    <td class="tg-031e">
      2
    </td>
    
    <td class="tg-031e">
      0.1
    </td>
    
    <td class="tg-031e">
      1.3
    </td>
    
    <td class="tg-031e">
      1.15
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>IH1</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      6.45
    </td>
    
    <td class="tg-031e">
      1.55
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>P</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
</table>

Indices: [3]

<table class="tg">
  <tr>
    <th class="tg-031e">
    </th>
    
    <th class="tg-e3zv">
      SH
    </th>
    
    <th class="tg-e3zv">
      IH1
    </th>
    
    <th class="tg-e3zv">
      F
    </th>
    
    <th class="tg-e3zv">
      T
    </th>
    
    <th class="tg-e3zv">
      ER0
    </th>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>SH</strong>
    </td>
    
    <td class="tg-031e">
      8.45
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>IH1</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>P</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
</table>

Indices: [3, 1]

<table class="tg">
  <tr>
    <th class="tg-031e">
    </th>
    
    <th class="tg-e3zv">
      SH
    </th>
    
    <th class="tg-e3zv">
      IH1
    </th>
    
    <th class="tg-e3zv">
      F
    </th>
    
    <th class="tg-e3zv">
      T
    </th>
    
    <th class="tg-e3zv">
      ER0
    </th>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>SH</strong>
    </td>
    
    <td class="tg-031e">
      <em>8.45</em>
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>IH1</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
  
  <tr>
    <td class="tg-e3zv">
      <strong>P</strong>
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
    
    <td class="tg-031e">
      &#8211;
    </td>
  </tr>
</table>

Indices: [3, 1, 0]

Now, we&#8217;ve reached the end of the process with 8.45 as our similarity score before the gap penalty is applied. Applying the gap penalty to the list of indices, we get (0.25*1) + log(1) + log(2) = 0.55. This means our final similarity score will be 8.45 &#8211; 0.55 = 7.9.

But we&#8217;re not done yet. We now need to find the similarity score of SH IH1 F T ER0 on itself. This comes out to be 16. And then we calculate the similarity percentage as described in step 10 above and we get (7.9/16)*100% = 49.375%

And now we of course go through step 11 by removing the entire first row of ordered pairs in our matrix and applying steps 1-10 on that matrix and so on. I&#8217;ll spare you and myself that process though since 49.375% ends up being the highest similarity out of any of these.