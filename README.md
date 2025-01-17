java c
CS   2110   -   Fall   2024 
Project   2:   LC-3   Datapath
1 Introduction Welcome   everyone   to   Project   2! Please read through the entire document before starting.      Things   are   often   explained   in   more   detail   after   they   are   introduced,   so   reading   the   entire   document   first   can   give   you a better understanding of how the document is structured and   of the overall   goals   for   this   project. Start early,   and   remember   to   utilize   Office   Hours   and   Piazza   as   needed.
In   this   project,   you’ll   be   doing   the   following:
• Implementing   some   basic   sequential   logic   elements (3.1). 
• Building   three   missing   pieces   of   the   LC-3   hardware (3.2). 
• Writing the   microcode   for the   bulk   of the   LC-3   instructions (3.4). 
1.1 Latches The first thing you’ll implement are   logical   elements that   allow   us   to   retain   data   in   a   circuit,   and   —   in   other   words — “remember” previous   values.    This   allows   us   to   build   more   complex   systems   that   depend   on   current input   values   along   with   previous   events   such   as   the   previous   state   of   the   system   -   in   other   words,   sequential logic.
The   goal   of   this   section   is   to   produce   a   register.       A   register   is   an   edge-triggered   sequential   logic   circuit element   that   can   store   a   binary   value.   For   more   on   this   section,   see Implementation:   Latches 
1.2 The LC-3 The LC-3 datapath we’ve discussed in   class   contains   a   lot   of pieces   very   similar   to   circuits   we’ve   seen   or   even   made   before   (e.g.   an   ALU,   a   register   file   with   8   edge-triggered   general   purpose   registers,   a   RAM   unit,   etc.).   One   piece   that   we’ve   mostly   referred   to   as   a “black-box”   in   the   past   is   the   micro-controller.    It’s   responsible for controlling the entire datapath and   getting   it   to   properly   execute   the   instructions   that   we   give   it.    That’s   a   big   task!      So,   how   does   the   micro-controller   actually   work?      In   this   project,   we’ll   build   a   few   datapath   components to develop some familiarity   with   the   LC-3,   and   then   we’ll   actually   write   the   “microcode”   which   allows   the   micro-controller   to   function.The   micro-controller,   shown   above,   is   a   finite   state   machine.   It   has   59   possible   states   (holy   cow!)   and, thus,   needs   6   bits   to   store   all   its   possible   states.   It   also   has   49   output   bits   composed   of output   flags.      10   of these   bits   are   used   to   determine   the   next   state   and   the   remaining   39   extend   throughout   the   datapath   to   control other   pieces   of   the   LC-3.    That   would   be   a   lot   of   very   complex   hardware—if it    were    built entirely in hardware.As   it   turns   out, there   is   an   easier   way.    We   can   actually   use   a   ROM   (Read-Only   Memory)   in   order   to   specify the   behavior   of   each   distinct   state   in   the   state   machine   (e.g.   each   instruction   will   map   to   a   series   of   entries in the ROM. Each entry   in the   ROM   represents   a   micro-state,   which   is   an   individual   state   of the   finite   state   machine   and   a   component   of   a   larger   sequence   of   states   known   as   a   macro-state.    There   are   3   macro-states   in   the   LC-3:    FETCH,   DECODE,   and   EXECUTE.   Each   macro-state   requires   between   1-5   micro-states   to complete,   depending   on   the   complexity   of the   instruction.
What   does   a   ROM   entry   look   like?    We   encourage   you   to   go   ahead   and   open   up   microcode   .xlsx,   on   the   microcode   sheet,   to   follow   along.
A   ROM   entry   is   basically   a   long   binary   string.      The   last   few   bits   of   it   handle   the   transition   to   the   next   state—you   don’t   need   to worry   about this   at   all   during this   project,   so   we’ve   covered   it   in   dark   grey   on   the   right. Do NOT modify the NEXT bits. Each of the other bits corresponds to   a signal   asserted   onto   the   datapath   during   that   clock   cycle—this   is   what   you   will   fill   out!.We’ve   simplified   and   removed   a   number   of   micro-states   and   control   signals   which   aren’t   directly   a   part   of   the   LC-3’s   main   instructions.   You   are   given   the   microcode   for   some   of   these   micro-states,   along   with   entries for   the   DRMUX   and   SR1MUX—do NOT change these.    Your   task   is   to   fill   in   the   rest   and   finish   the   LC-3   micro-controller!
1.3 Assignment Files 
•    latches .sim -   a   CircuitSim   file   in   which   you   will   build   sequential   logic   components.
•      LC3   .sim   -   a   large   CircuitSim   file   containing   the   LC-3 AND a “Manual   LC-3” which   does   not   need   to be   modified   in   any   way   but   is   simply   present   as   a   tool   for   you   while   writing   microcode.    You   should   only   modify   the   PC,   CC-logic,   and   ALU   sub-circuits   in   this   file.
•   microcode   .xlsx   -   an   Excel   document   in   which   you   will   write   your   microcode. Do not touch cells that have been blacked out. 
• ROM.dat   -   a   text   file   which   you   can   paste   your   microcode   into   and   then   import   into   the   LC-3.
•   tests/   -   a   subdirectory   which   contains   a   number   of test   cases   you   can   use   to   verify   the   functionality   of your   circuit   and   microcode.
•   proj2-tester.jar -   a   local tester that you   can   use   to   test   all   of the   components   of the   project.   This   official   tester   is   available   on   Gradescope   (where   it   will   be   return   your   grade).
•    LC-3InstructionsDetail.pdf - a   PDF with   descriptions   and pseudocode   for each   instruction.
1.4 Elements to complete 
1.   Implement   the   RS   Latch,   Gated   D   Latch,   D   Flip-Flop,   and   Register   sub-circuits   in   latches   .sim.
2.   Implement   the   ALU,   PC,   and   CC-Logic   sub-circuits   in   LC3   .sim.
3.      Complete   the   LC-3   microcode   in microcode   .xlsx.
2 Setup 
The   software   you   will   be   using   for   this   project   and   all   future   circuit   based   assignments   is   called   CircuitSim
-   an   interactive   circuit   simulation   package.CircuitSim   is   a powerful   simulation tool   designed   for educational   use.    This gives   it the   advantage   of   being   a   little   more   forgiving   than   some   of the   more   commercial   simulators.   However,   it   still   requires   some   time   and   effort   to   be   able   to   use   the   program   efficiently.
Please   follow   the   instructions   in   the   file   ”CircuitSim   Installation   Guide.pdf”   in   Canvas,      under   ”Files    >   Course   Software   >   CircuitSim”   to   install   CircuitSim.
All .sim files should be opened in CircuitSim. 
Please do not move, rename, or remove any of the provided inputs/outputs. 
3          Implementation 
3.1 Latches 
For this   part   of the   assignment,   you will   build your   own   register   from   the   ground   up.    For   more   information   about   each   sub-circuit,   refer   to   your   textbook.    All   work   for   this   portion   of the   assignment   will   be   done   in the   latches   .sim file.
3.1.1 RS Latch In   the “RS Latch” sub-circuit in the latches   .sim file,   you   will   start   by   building   a   RS   latch   using   NAND   gates,   as   described   in   your   textbook.   RS   Latch   is   the   basic   circuit   for   sequential   logic.   It   stores   one   bit   of information   and   has   3   important   states:
1.    S=1,   R=1:   This   is   called   the Quiescent State.   In   this   state   the   latch   is   storing   a   value,   and   nothing is   trying   to   change   that   value.
2.    S=0,   R=1:   By   changing   momentarily   from   the   Quiescent   State   to   this   state,   the   value   of   the   latch   is changed   so   that   the   output   Q   now   stores   a   1.
3.    S=1,   R=0:   By   changing   momentarily   from   the   Quiescent   State   to   this   state,   the   value   of   the   latch   is changed   so   that   the   output   Q   now   stores   a   0.
Note:      In   order   for   the   RS   Latch   to   work   properly   (for   testing   purposes,   not   for   building),   both   R   and   S   cannot   be   0   at   the   same   time   (illegal   state).
Once   you   set   the   bit   you   wish   to   store,   change   back   to   the   Quiescent   State   to   keep   that   value   stored.
Notice that the   circuit   has two output   pins;   one   is   the   bit   the   latch   is   currently   storing,   and   the   other   is   the   opposite   of that   bit.

3.1.2          Gated D Latch 
In   the “Gated D Latch” sub-circuit in the latches   .sim file,   using   your   RS   latch   sub-circuit   (in   the
Circuits   tab),   implement   a   Gated   D   Latch   as   described   in   the   textbook.    You   are not allowed   to   use   the   built-in   SR   Latch   in   CircuitSim to   build this   circuit.
The   Gated D Latch is made   up   of   an   RS   Latch   as   well   as   two   additional   gates   that   control   when   a   value   can be   stored.
The value   of the   output   can   only   be   changed when Write   Enable   is   set to   1.    Notice that   the   Gated   D   Latch   sub-circuit   only   has   one   output   pin,   so   you   should   disregard   the   inverse   output   Q’   of your   RS   Latch.

3.1.3 D Flip-Flop 
In   the “D Flip-Flop” sub-circuit in the latches   .sim file,   using   the   Gated   D   Latch   circuit   you   built   (in   the   Circuits   tab),   create   a   D   Flip-Flop.
A   D   Flip-Flop   is   composed   of two   Gated   D   Latches   back   to   back,   and   it   implements   edge   triggered   logic.
Your   D   Flip-Flop output   should be   able to   change on   the rising edge,   which   means   that   the   state   of the   D   Flip-Flop   should   only   be   able   to   change   at   the   exact   instant   the   clock   goes   from   0   to   1.

3.1.4 Register 
In   the “Register” sub-circuit in the latches   .sim file,   using   the   D   Flip-Flop   you just   created   (in   the   Circuits   tab),   build   a   4-bit   Register.
Your register should also use   edge-triggered   logic.    The value   of the register   should   change   on   the   rising   edge.

3.2          Completing the LC-3 Sub-Circuits 
All   work   for   this   section   of the   assignment   will   be   done   in   the   LC3   .sim   file.
Note: DO NOT move the position of the inputs and outputs in   the   LC-3   sub-circuits;   this   could   break   the   rest   of the   LC-3   simulator.
3.2.1 ALU 
You will   need   to   build the   ALU   (Arithmetic   Logic   Unit)   sub-circuit   in   LC3   .sim.   Note:    You   may   use   the   built-in   adder   under   the   Arithmetic   tab!
The   ALU   will   perform   one   of 4   functions   and   Output   it   depending   on   the   ALUK   signal:
1. ALUK = 0b00: A   + B
2. ALUK = 0b01: A    B
3. ALUK = 0b10: NOT A
4. ALUK = 0b11: PASS   A
You   should   be   able   to   use   what   you   learned   from   Project   1   to   populate   this   sub-circuit   easily.
3.2.2 PC 
You   will   need   to   build   the   PC   (Program   Counter)   sub-circuit   in   LC3   .sim.
Note:    You   may   use   the   built-in   multiplexer   under   the   Plexer   tab!The PC is a 16 bit register that holds the address of   the next instruction to be executed.   During the FETCH stage,   the   contents   of   the   PC   are   loaded   into   the   memory   address   register   (MAR),   and   the   PC   is   updated with   the   address   of the   next   instruction.   There   are   three   scenarios   for   updating   the   PC:
1.    The   contents   of the   PC   are   incremented   by   1. Selected when PCMUX = 0b00.
2.      The   result   of the   ADDR   (an   address-adding   unit)   is   the   address   of the   next   instruction.    The   output   from   the   ADDR   should   be   stored   in   the   PC.   This   occurs   if we   use   the   branching   instruction   (BR).
Selected when PCMUX = 0b01.3.    The   value   on   the   BUS   is   the   address   of   the   next   instruction.    The   value   on   the   BUS   should   be   stored
into   the   PC.   For   example,   this   is   used   during   the   JMP   instruction. Selected when PCMUX = 0b10.
The   PC   should   only   be   loaded   on   a   rising   clock   edge   and   when   the   LD.PC   signal   is   on.
Ensure   that   you   don’t   reach   the   unused   case   (PCMUX   =   0b11)   of   the   circuit,   or   else   spooky   stuff   might happen   (undefined   behavior   in   LC-3).
3.2.3 CC-Logic 
You   will   implement   the   CC-Logic   sub-circuit   in   LC3   .sim.The   LC-3   has   three   condition   codes:   N   (negative),   Z   (zero),   and   P   (positive).   These   codes   are   saved   on   the   next   rising   edge   after   the   LC-3   executes   Operate   or   Load   instructions   that   include   loading   a   result   into   a   general   purpose   register,   such   as   ADD   and   LDR.
For example, if ADD R2, R0, R1 results in a positive number, then NZP = 001. The   CC   sub-circuit   should   set   N   to   1   if   the   input   is   negative,   set   Z   to   1   if   the   input   is   zero,   and   set   P   to   1   if the   input   is   positive.    Only   1   bit   in   NZP   should   be   set   at   any   given   time.    Zero   is   not   considered   a   positive number.
Bit   2   (the   MSB)   is   N,   Bit   1   is   Z,   Bit   0   is   P.
With代 写CS 2110 - Fall 2024 Project 2: LC-3 DatapathJava
代做程序编程语言   that   in   mind,   set   the   correct   bit   and   implement   this   circuit   in   the   CC-Logic   sub-circuit.
Hint:   you   can   use   a   comparator   for   this   sub-circuit!   They   are   found   in   the   Arithmetic   tab   in   CircuitSim.
3.3          Using Manual LC-3 (for testing only) 
•    Once   you   have   your   PC,   CC-Logic,   and   ALU   complete,   you   can   begin   playing   around   with   the   in-   structions   and   control   signals   to   understand   how   the   LC-3   works.
•   In   lecture   and   in   lab,   we   have   covered   the   control   signals   in   the   datapath   and   how   to   use   them   when tracing   an   instruction.
•      The   first   thing   you   will   want   to   do   is   use   the   Custom-Bus, GateBUS, and   LD.IR   signals   to   set   a   custom IR   value   on   the   next   rising   edge.   Until   you   figure   out   the   micro-states   for   FETCH,   you   can   use   these
steps   to   set   your   IR   with   any   instruction   you   want   to   work   on.
•    Once   you   have   an   idea   of   how   FETCH   works,   you   can   load   some   instruction(s)   in   the   RAM.   In   order to   do   that:
1.   right-click   the   RAM   near   the   bottom   of   the   Manual   LC-3   circuit
2.      select   “edit   contents”
3.      click   “Load   from   file”
4.      locate   and   select   one   of   the   provided   test   files   in   the   project   (ex:   add.dat)
5.    close   the   edit   contents   menu
• Now   that   you   have   loaded   the   RAM   with   a   program,   you   can   fetch   instructions   into   the   IR.
•      Now   that   you   have   an   instruction   in   the   IR,   you   can   start   executing   it.    In   order   to   do   that   you   can   turn   on   the   different   signal   pins   on   the   right   in   order   to   control   the   datapath   and   move   data   around   like   we   did   in   lecture/lab.    Once   you   think   you   know   how   an   instruction   is   executed,   you   can   enter   it into   the   microcode   spreadsheet,   the   process   for   which   is   outlined   below.
– Tests   inside   the   tests/   directory   have   a   comment   at   the   end   of the   .asm   file   which   explains   the   system   state   after   the   end   of   the   program’s   execution.    You   must   ensure   that   this   is   the   system state   after   you   have   run   every   instruction   sequentially   through   the   simulator.
– To   test   whether   the   program   acted   correctly,   go   to   the   ‘LC-3’   circuit   and   double-click   into   the   ‘REG   FILE’   element that is placed in the datapath.    Note:    you should    not just   click   into   the   ‘REG   FILE’   subcircuit,   as   this   will   not   properly   load   the   state   of   the   specific   ‘REG   FILE’   element   that’s   built   into   the   LC-3, just   some   generic   REG   FILE.
– Bonus   tip:    Use      Ctrl-R   to   reset   the   simulator   state   and   easily   clear   RAM   and   registers   to   0   in order   to   test   again.
•   If   you   are   familiar   with   LC-3   assembly      (you   will   learn   it   throughout   the   next   few   weeks),   you   are   welcome   to   write   your   own   test   programs   to   verify   your   code.   Make   sure   that   your   programs do not start   at   x3000,   as   in   this   project only we   will   start   execution   at   x0000   for   simplicity’s   sake.   You   can compile   LC-3   assembly   projects   to   machine   code   by   following   the   LC-3   ISA,   and   then   create   your   own RAM.dat   files.    However,   we   can’t   guarantee   that   we’ll   be   able   to   help   with   these   test   cases   in   office hours.
3.4 Writing the Microcode 
ONLY open this Excel Sheet in Microsoft Excel. Do NOT use other applications. 
If need   help   accessing   Excel: https://oit.gatech.edu/email 
Note:    We   recommend   you   read   this   entire   section   before   starting   to   fill   out   the   microcode   sheet.
• DO NOT CHANGE: 
– NEXT   bits   (the   6   greyed   out   bits   on   the   right   side)
– Values   for   DRMUX   and   SR1MUX   (aleady   filled   in)
– Any   microstates   that   are   already   filled   out   for   you
As   a   general   rule   of thumb,   try   not   to   break   the   spreadsheet   :).
•      Throughout   this      assignment,      you   will   find   the      LC-3      Datapath      Reference.pdf   to   be   very   helpful   (Canvas   →   Modules   →   Shared   Resources   →   LC-3   Datapath   Reference.pdf).
• For   information   on   filling   out LDSR and STII,   see   below.
• For   more   information   on   each   of the   control   signals,   see   the   Appendix   at   the   end   of this   PDF.
•      For the Store instructions,   ensure that you prioritize   loading   the   address   we   want   to   write   to   into   the   MAR   over   loading   the   value   we   want   to   write   with   into   the   MDR.   The   autograder   is   expecting   that
you   load   the   address   first   and   then   the   value.
•      Now   that   you’ve   developed   some   familiarity   with   the   datapath,   gotten   a   chance   to   “act   as   the   micro-   controller”,   and   run   the   execute   stage   of instructions   on   the   Manual   LC-3,   you   can   complete   the   final   part   of the   project:   writing   the   microcode   for   a   number   of micro-instructions   on   the   LC-3!
•   In   the   microcode   .xlsx   Excel   spreadsheet,   there   exists   a   number   of   macro-states.    Among   them   are   FETCH,   DECODE,   and   the   EXECUTE   stages   for   most   instructions   supported   by   the   LC-3   (we’ve   removed   several   macro-states   related   to   trap   and   interrupt   handling).      For   each   macro-state,   we’ve provided   space   for   the   micro-states   which   will   make   up   that   macro-state.   For   each   micro-state,   we’ve handled   all   of   the   logic   related   to   transitioning   to   the   next   micro-state.      We’ve   also   implemented   a   small   subset   of   the   macro-states   in   order   to   provide   inspiration   to   you,   our   students   (you’re   welcome).
• You   should   complete   all   the   remaining   macro-states   by   filling   in   their   micro-states.
– We recommend that you fill out   the   micro-states   for FETCH first;   however,   the   autograder   does   not   depend   on FETCH being   implemented   first.
– If   you   notice   that   the   output   column   to   the   right   of   the   blacked-out   columns   (column   AH)   is   showing   artifacts   like   #NAME?,   try   opening   the   Excel   spreadsheet   in   your   Georgia   Tech   Office   365   Online   Excel   workspace.    Sign   in   to   Office   365   with   Georgia   Tech   credentials,   select   ‘Excel’,   and   then   choose   ‘Upload   and   open’   to   edit   the   excel   sheet   online.
• STIRSUM 
– Your   fellow   TAs   are   trying   to   create   a   new   LC-3   instruction!      We’re   calling   it   - STIRSUM -   Store   Indirect   Register   Sum.
– This   instruction   accesses   memory   twice–the   first   loading   the   value   at   address   SR1   +   SR2   and second   storing   SR1   at   the   address   found   from   the   original   load.
– Mem[Mem[SR1   +   SR2]]   =   SR1
– The   16-bit   instruction   is   formatted   in   the   following:
[   OPCODE   |    SR1    (3   bits)   |    000000    (6   bits)    |    SR2    (3   bits)   ]
– Note:       We      do   not   care   about   the   value   that   is   left   in      SR   when   this   instruction   is   completed;   however,   the   values   in   any   of the   other   registers   must   remain   the   same.
– Note:    You   do NOT need   to   set   the   condition   codes   (CC)   for   this   instruction.
• PCHOP 
– The   TAs   got   together   again   (yikes)   and   decided   to   make   another   function - PCHOP -   Program   Counter   Hop.
– This   instruction   sets   the   program   counter   (PC)   to   the   value   in   SR   multiplied   by   8.
– PC   =   SR   *   8
– The   16-bit   instruction   is   formatted   in   the   following:
[   OPCODE   |    SR    (3   bits)   |    SR    (3   bits)    |    000    (3   bits)    |    SR    (3   bits)   ]
– Note:    You   do NOT need   to   set   the   condition   codes   (CC)   for   this   instruction.
We could really use your help!   Fill out the micro-states for STIRSUM and PCHOP in   the   microcode   .xlsx   excel   sheet.
3.4.1          How to Test your Microcode At any time that you want   to   test   your   microcode,   you   can   export   it   from   the   .xlsx   file   and   apply   it   to   LC-3   hardware   by   following   these   steps. IMPORTANT    NOTE:    Passing all of the tests provided does not guarantee that you have a functional datapath, any number of coincidences could cause you to get the correct output with incorrect functionality. As always, we reserve the right to grade with additional test cases. 
To   manually   test   your   microcode   in   the   LC-3:
1.      At the   bottom   of the microcode   .xlsx   file   select the output tab.
2.    Copy   all   of   column   D   from   row   1   through   row   64.
3.   In   CircuitSim,   open   LC3.sim.       Navigate   to   the   ‘Fsm’   sub-circuit.       This   circuit   contains   the   micro-   controller.    To   enter   the   sub-circuit,   either   double-click   on   it   inside   the   LC-3   sub-circuit   or   right-click   it   in   the   LC-3   and   select   “View   internal   state.”
4.      Right-click   on   the   ROM   in   the   ‘Fsm’   and   select   “Edit   contents.”
5.    Select   the   top   left   cell   and   paste   into   the   ROM.   You   should   see   the   values   in   the   ROM   change.
6.      Navigate   to   the   ‘LC-3’   sub-circuit.
7.   You   can   now   load   a   program   into   the   RAM,   following   the   instructions   above   in   the   Manual   LC-3   section   (Section   3.3   of   this   PDF).
8.      To   run   the   LC-3,   you   can   manually   click   through   the   CLK   signal   or   use   ‘Ctrl-K’   to   start   or   stop   the   automatic   clock.    After   your   program   has   stopped   executing    (you   can   tell   when   it’s   finished   running   because   it   will   HALT   and   the   datapath   will   stop   changing).
9.      Tests inside the tests/ directory have a   comment   at   the   end   of the   .asm   file which   explains   the   system   state   after the   end   of the   program’s   execution.    To test whether the   program   acted   correctly,   go   to   the   ‘LC-3’ circuit and double-click into the   ‘REG   FILE’   element that is placed in the datapath.    Note:   you should not just   click   into   the   ‘REG   FILE’   sub-circuit,   as   this   will   not   properly   load   the   state   of   the   specific   ‘REG   FILE’   element   that’s   built   into   the   LC-3, just   some   generic   REG   FILE.
3.4.2 Tips, Tricks, Resources 
This   is   all   pretty   crazy   to   take   in   at   first.      But   it’s   not   the   end   of   the   world   if   you   make   sure   to   use   the   resources   available   to   you.   Here   are   some   options:
•    LC-3   Datapath   Reference   Sheet:    Canvas    →   Modules   →   Shared   Resources   →   LC-3   Datapath   Refer-   ence.pdf
• LC-3 Instructions Detail   Sheet:   LC-3InstructionsDetail.pdf, in this   project.zip
• The   Manual   LC-3!
• Your   friendly   TAs   (Office   Hours,   Piazza,   etc.)
• Textbook
• Appendix   on   Datapath   Control   Signals   in this PDF.
4 Autograder 
To   run   the   autograder,   run
java      -jar      proj2-tester.jarat   a   command   prompt   in   the   same   directory   as   all   your   deliverable   files   (see   next   section   for   a   list).   This   is the   same   autograder   that   Gradescope   uses,   but   is   much   easier   and   faster   to   use. The autograder should be run from the terminal. Additionally, the autograder will grade each instruction   individually,   so   issues   will   not   cascade.
5 Deliverables 
Please   submit   the   follow   files:
1.      latches   .sim
2.    LC3   .sim
3.   microcode   .xlsx
to   Gradescope   under   the   assignment   “Project   2   -   LC-3   Datapath”   .
Note:      The   autograder   may   not   reflect   your   final   grade   on   this   assignment.    We   reserve   the   right   to   update the   autograder   as   we   see   fit   when   grading.
6 Demos 
This project will be demoed. Demos   are   designed   to   make   sure   that   you   understand   the   content   of the   project   and   related   topics.   They   may   include   technical   and/or   conceptual   questions.
•    Sign   up   for   a   demo   time   slot   via   Canvas before the   beginning   of the   first   demo   slot.   This   is   the   only   way   you   can   ensure   you   will   have   a   slot.
•   If you cannot attend any of the   predetermined   demo timeslots,   email   one   of the   Head   TAs before the   beginning   of the   first   demo   slot.
•   If   you   know   you   are   going   to   miss   your   demo,   you   can   cancel   your   slot   on   Canvas   with   no   penalty;   however,   you   are not guaranteed   another   time   slot.   You   cannot   cancel   your   demo   within   24   hours   or
else   it   will   be   counted   as   a   missed   demo.
•   Your   overall   project   score   will   be      ((submission_score      *    0.7)    +      (demo_score      *    0.3)), meaning   if you   received   a   100%   on   your   submission,   but   a   50%   on   the   demo   you   would   receive   an   overall   score   of 85%. If you miss your demo you will not receive any of these points and the maximum you can receive on the project is 70%. 


         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
