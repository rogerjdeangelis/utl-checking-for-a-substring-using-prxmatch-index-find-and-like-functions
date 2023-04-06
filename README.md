# utl-checking-for-a-substring-using-prxmatch-index-find-and-like-functions
Find and flag records that have any non missing value occurring after 'PB' or 'MB' across columns,
    %let pgm=utl-checking-for-a-substring-using-prxmatch-index-find-and-like-functions;

    Find and flag records that have any non missing value occurring after 'PB' or 'MB' across columns,

     Solutions
        1. SAS sql
        2. WPS sql


    github
    https://tinyurl.com/mub7zsba
    https://github.com/rogerjdeangelis/utl-checking-for-a-substring-using-prxmatch-index-find-and-like-functions

    Stackoverflow
    https://tinyurl.com/5n6z5vs7
    https://stackoverflow.com/questions/75769662/how-to-find-and-flag-if-there-are-values-after-a-certain-value-horizontally

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have;

       length catAll $200;

       input (p1-p10) ($,);

       catAll=catx('@',of p1-p10);

    cards4;
    PM MM JM MM PM PB . . PM .
    PM MM JM MM PM PB JM . . .
    PM MM JM MM PM MB PM MM . .
    PM MM JM MM PM PM MM MB . .
    PM MM JM MM PM PM MM PB . .
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  I addded the variable catAll as the concatenation of p1-p10                                                           */
    /*                                                                                                                        */
    /*  Up to 40 obs from last table WORK.HAVE total obs=5 05APR2023:19:07:17                                                 */
    /*                                                                                                                        */
    /*  Obs   P1    P2    P3    P4    P5    P6    P7    P8    P9    P10      CATALL catx('@',of p1-p10)                       */
    /*                                                                                                                        */
    /*   1    PM    MM    JM    MM    PM    PB                PM             PM@MM@JM@MM@PM@PB@PM                             */
    /*   2    PM    MM    JM    MM    PM    PB    JM                         PM@MM@JM@MM@PM@PB@JM                             */
    /*   3    PM    MM    JM    MM    PM    MB    PM    MM                   PM@MM@JM@MM@PM@MB@PM@MM                          */
    /*   4    PM    MM    JM    MM    PM    PM    MM    MB                   PM@MM@JM@MM@PM@PM@MM@MB                          */
    /*   5    PM    MM    JM    MM    PM    PM    MM    PB                   PM@MM@JM@MM@PM@PM@MM@PB                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  Up to 40 obs from last table WORK.WANT total obs=20 05APR2023:19:10:58                                                */
    /*                                                                                                                        */
    /*  Obs    OPERATOR    FLG   |    RULES                                                                                   */
    /*                           |                   =====                                                                    */
    /*    1    prxmatch     1    |    PM@MM@JM@MM@PM@PB@PM  PB@PM PM follows PB so set flg=1                                  */
    /*    2    prxmatch     1    |                   =====                                                                    */
    /*    3    prxmatch     1    |                                                                                            */
    /*    4    prxmatch     0    |                                                                                            */
    /*    5    prxmatch     0    |                                                                                            */
    /*                           |                                                                                            */
    /*    6    index        1    |                                                                                            */
    /*    7    index        1    |                                                                                            */
    /*    8    index        1    |                                                                                            */
    /*    9    index        0    |                                                                                            */
    /*   10    index        0    |                                                                                            */
    /*                           |                                                                                            */
    /*   11    like         1    |                                                                                            */
    /*   12    like         1    |                                                                                            */
    /*   13    like         1    |                                                                                            */
    /*   14    like         0    |                                                                                            */
    /*   15    like         0    |                                                                                            */
    /*                           |                                                                                            */
    /*   16    find         1    |                                                                                            */
    /*   17    find         1    |                                                                                            */
    /*   18    find         1    |                                                                                            */
    /*   19    find         0    |                                                                                            */
    /*   20    find         0    |                                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     ___  __ _ ___   _ __  _ __ ___   ___ ___  ___ ___
    / __|/ _` / __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    \__ \ (_| \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
    |___/\__,_|___/ | .__/|_|  \___/ \___\___||___/___/
                    |_|
    */

    proc sql;
      create
         table want as
      select
         'prxmatch'                                  as operator
         ,prxmatch('/PB@|MB@/',strip(catAll))  ne 0  as flg
      from
         have
      outer
         union corr
      select
         'index' as operator
         ,index(catAll, 'PB@')>0 or index(catAll,'MB@')>0 as flg
      from
         have
      outer
         union corr
      select
         'like' as operator
         ,(strip(catAll) like '%PB@%') or (strip(catAll) like '%MB@%')  as flg
      from
         have
      outer
         union corr
      select
         'find' as operator
         ,(find(catAll,"PB@")>0 or find(catAll,"MB@")>0)  as flg
      from
         have
    ;quit;

    /*
    __      ___ __  ___   _ __  _ __ ___   ___ ___  ___ ___
    \ \ /\ / / `_ \/ __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
     \ V  V /| |_) \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
      \_/\_/ | .__/|___/ | .__/|_|  \___/ \___\___||___/___/
             |_|         |_|
    */

    %let wrk=%sysfunc(pathname(work));

    %utl_submit_wps64("

    options validvarname=any;

    libname wrk '&work';

    proc sql;
      create
         table want as
      select
         'prxmatch' as func
         ,prxmatch('/PB@|MB@/',strip(catAll))  ne 0  as flg
      from
         wrk.have
      outer
         union corr
      select
         'index' as func
         ,index(catAll, 'PB@')>0 or index(catAll,'MB@')>0 as flg
      from
         wrk.have
      outer
         union corr
      select
         'like' as func
         ,(strip(catAll) like '%PB@%') or (strip(catAll) like '%MB@%')  as flg
      from
         wrk.have
      outer
         union corr
      select
         'find' as func
         ,(find(catAll,'PB@')>0 or find(catAll,'MB@')>0)  as flg
      from
         wrk.have
    ;quit;

    proc print;
    run;quit;
    ");

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|
