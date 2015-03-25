[<< index](http://code.google.com/p/corkami/) [Android/Java/x86/... opcodes tables](http://opcodes.corkami.com) [PDF tricks](http://pdf.corkami.com) [Portable Executable](http://pe.corkami.com) [x86 oddities](http://x86.corkami.com) (this project is done in my spare time. **[Support ](http://code.google.com/p/corkami/wiki/About#Support_Corkami)** it!)

# Such a weird processor - messing with x86 opcodes (and a little bit of PE...) #

#### presented the 28th October 2011 during Hashdays 2011 at Luzern, Switzerland ####
> I studied [x86](http://code.google.com/p/corkami/wiki/x86oddities) and [PE](http://code.google.com/p/corkami/wiki/PE) from scratch, and failed all the tools I tried in the process:
> after an easy-to-medium level introduction, I present a few of the PoCs available on this site, as well as [CoST](http://code.google.com/p/corkami/wiki/StandardTest), a standard test for PE and x86.
# abstract #
> Whether it's for malware analysis, vulnerability research or emulation, having a correct disassembly of a binary is the essential thing you need when you analyze code. Unfortunately, many people are not aware that there are a lot of opcodes that are rarely used in normal files, but valid for execution, but also several common opcodes have rarely seen behaviours, which could lead to wrong conclusions after an improper analysis.

> For this research, I decided to go back to the basics and study assembly from scratch, covering all opcodes, whether they're obsolete or brand new, common or undocumented. This helped me to find bugs in all the disassemblers I tried, including the most famous ones. This presentation introduces the funniest aspects of the x86 CPUs, that I discovered in the process, including unexpected or rarely known opcodes and undocumented behavior of common opcodes.

> The talk will also cover opcodes that are used in armored code (malware/commercial protectors) that are likely to break tools (disassemblers, analyzers, emulators, tracers,...), and introduce some useful tools and documents that were created in the process of the research.

# slides #
> [Slideshare page (with speaker's notes)](http://www.slideshare.net/ange4771/such-a-weird-processor-messing-with-opcodes-and-a-little-bit-of-pe)
> &lt;wiki:gadget url="https://corkami.googlecode.com/svn/wiki/gadgets/hashdays\_slideshare.xml" width=595 height=497 border=0/&gt;

# downloads #
> [demo slides (PDF) + complete slides deck with notes (ODP) + Proof of Concepts](http://corkami.googlecode.com/files/hashdays2011.zip)

# video #
<a href='http://www.youtube.com/watch?feature=player_embedded&v=MJvsshovITE' target='_blank'><img src='http://img.youtube.com/vi/MJvsshovITE/0.jpg' width='425' height=344 /></a>

You might want to check my [BerlinSides presentation](http://bsx2.corkami.com) for recorded screencasts of the demos.

# comments & ranking #
  * "Besides a highly technical topic, the presentation was pleasant and accessible to non-experts. It gave a succinct yet convincing overview of some anti-debugging tricks used in malware." _Jean-Philippe Aumasson_
  * "It was nice to see a technical lecture at Hashdays. It taught me new things and Ange's enthousiasm made it very enjoyable. You can tell he's dived deep into it and enjoys it." _Walter Belgers_
  * "Personally I thought the presentation was great - you are a really energetic speaker and made the whole topic very interesting. Really liked all the examples - did not realise that half of that stuff existed in x86 or PE :) I've passed on the slides to the other reversers in work, and they've all had positive things to say. Overall really good job, would be great to see another presentation next year!" _Robert McArdle_
  * "Ange presented the results of his thorough research into the undocumented and unexpected realms of IA-32 and x64 instructions with quite a few surprises. He also covered cornercases of the PE file format, all of which he provides as inidividual test cases on his web site. I greatly enjoyed this presentation and would recommend Ange to give it again." _FX_
  * "The presentation Ange gave at the hashdays 2011 in Luzern Switzerland gave the audience a deep look into some of the less explored corners of x86 assembly and the PE executable format. My expectation of the talk was more then met and I learned some realy interesting things about both topics. I appreciate Anges hard work and the openness of the results as they can be used in daily work especially for debuggers and emulators. One of the most interesting talks at hashdays this year thanks so much." _Tim Kornau_

  * "best talk ever!" an anonymous Hashdays feedback form

## ranking ##
7th best rated from 25 talks (rated with 4.5 on a 1-5 scale, deviation 0.71, variance 0.55)

[<< index](http://code.google.com/p/corkami/) [Android/Java/x86/... opcodes tables](http://opcodes.corkami.com) [PDF tricks](http://pdf.corkami.com) [Portable Executable](http://pe.corkami.com) [x86 oddities](http://x86.corkami.com) (this project is done in my spare time. **[Support ](http://code.google.com/p/corkami/wiki/About#Support_Corkami)** it!)