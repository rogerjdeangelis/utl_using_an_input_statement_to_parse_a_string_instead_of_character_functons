# utl_using_an_input_statement_to_parse_a_string_instead_of_character_functons
Using an input statement to parse a string instead of character functons. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.


    Using an input statement to parse a string instead of character functons

    Just some thoughts about the flexibility of the input statement.
    It seems to me the input statement can be a more powerfull
    string parser then scan, index and substring.

    Below I use

      input (a b c) ( $1. +1)  @10 cnt;

    Instead ofa looping scan function in a subsequent datastep

    see
    https://tinyurl.com/y9vbv89g
    https://communities.sas.com/t5/General-SAS-Programming/Adding-Frequencies/m-p/478490


    INPUT ( a file)
    ===============
                               |  RULES
       d:/txt/adding_freq.txt  |
                               |  Grp     Freq
          ++++5++++10+         |
          1        70          |  1       90    70+12+8
          1,2      12          |  2       27      12+15
          1,3      8           |  3        8
          2,4,5    15          |  ..
          4,5      16          |
          5        7           |

    EXAMPLE OUTPUT
    --------------

     WORK.WANT total obs=5

        V  CNTGRP

        1    90
        2    27
        3     8
        4    31
        5    38


    PROCESS
    =======

    data want;

      * normalize - long and skinny;
      if _n_=0 then do;

         %let rc=%sysfunc(dosubl('
              data have(keep=v cnt where=(v ne ''));
                 infile "d:/txt/adding_freq.txt";
                 input (a b c) ( $1. +1)  @10 cnt;
                 v=a;output;
                 v=b;output;
                 v=c;output;
              run;quit;
              proc sort data=have out=havSrt;
               by v;
              run;quit;
         '));

       end;

       retain v ' ' cntGrp 0;

       set havSrt;
        by v;
        cntGrp=cntGrp+cnt;
        if last.v then do;
           output;
           cntGrp=0;
        end;
        drop cnt;

    run;quit;

    OUTPUT
    ======

     WORK.WANT total obs=5

        V  CNTGRP

        1    90
        2    27
        3     8
        4    31
        5    38


    *                _                _       _
     _ __ ___   __ _| | _____      __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \    / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/   | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|    \__,_|\__,_|\__\__,_|

    ;


    data _null_;
     file "d:/txt/adding_freq.txt";
      input;
      put _infile_;
    cards;
    1        70
    1,2      12
    1,3      8
    2,4,5    15
    4,5      16
    5        7
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    see process


