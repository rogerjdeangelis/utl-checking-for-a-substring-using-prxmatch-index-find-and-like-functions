# utl-checking-for-a-substring-using-prxmatch-index-find-and-like-functions
Find and flag records that have any non missing value occurring after 'PB' or 'MB' across columns,
    %let pgm=utl-checking-for-a-substring-using-prxmatch-index-find-and-like-functions;

    Find and flag records that have any non missing value occurring after 'PB' or 'MB' across columns,

     Solutions
        1. SAS sql
        2. WPS sql
        3. SAS datastep (thanks to Nat Wooding)
        4. WPS datastep (thanks to Nat Wooding note input and output to sas work library)


    FYI (Not sure why this does not work )
      catAll in ( '%PB@%' , '%MB@%' )


    github
    https://tinyurl.com/mub7zsba
    https://github.com/rogerjdeangelis/utl-checking-for-a-substring-using-prxmatch-index-find-and-like-functions

    Stackoverflow
    https://tinyurl.com/5n6z5vs7
    https://stackoverflow.com/questions/75769662/how-to-find-and-flag-if-there-are-values-after-a-certain-value-horizontally

    /*                     _
     ___  ___   __ _ _ __ | |__   _____  __   ___  _ __
    / __|/ _ \ / _` | `_ \| `_ \ / _ \ \/ /  / _ \| `_ \
    \__ \ (_) | (_| | |_) | |_) | (_) >  <  | (_) | | | |
    |___/\___/ \__,_| .__/|_.__/ \___/_/\_\  \___/|_| |_|
                    |_|
    */

    /*---- WPS included products? ---*/

    ---ACTIANMATRIX  ---IMPALA               ---SAND
    ---BATCH         ---INFORMIX             ---SDK
    ---CLI           ---KOGNITIO             ---SNOWFLAKE
    ---COMMUNICATE   ---LINK                 ---SQLITE
    ---CORE          ---MARIADB              ---SQLSERVER
    ---DATAPROFILER  ---MATRIX               ---STATISTICS
    ---DB2           ---ML                   ---SYBASE
    ---DBFILES       ---MLTOOLS              ---SYBASEIQ
    ---GRAPHING      ---MYSQL                ---TERADATA
    ---GREENPLUM     ---NETEZZA              ---TIMESERIES
    ---HADOOP        ---ODBC                 ---VERTICA
    ---HANA          ---OLEDB                ---WORKBENCH
    ---IMPALA        ---OPERATIONALRESEARCH  ---WORKFLOW
    ---INFORMIX      ---ORACLE
    ---KOGNITIO      ---POSTGRESQL
    ---LINK          ---PYTHON
    ---MARIADB       ---QUALITYCONTROL
    ---MATRIX        ---R

    /*                     _                        __  __
     ___  ___   __ _ _ __ | |__   _____  __   ___  / _|/ _|
    / __|/ _ \ / _` | `_ \| `_ \ / _ \ \/ /  / _ \| |_| |_
    \__ \ (_) | (_| | |_) | |_) | (_) >  <  | (_) |  _|  _|
    |___/\___/ \__,_| .__/|_.__/ \___/_/\_\  \___/|_| |_|
                    |_|
    */

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

    /*                   _       _            _
     ___  __ _ ___    __| | __ _| |_ __ _ ___| |_ ___ _ __
    / __|/ _` / __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \
    \__ \ (_| \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) |
    |___/\__,_|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/
                                                     |_|
    */


    Data Want;
    set have;
    Array Check P:;
    Do I =  Dim(Check) to 1 by -1;
         If Check(i) in ('PB', 'MB') then return;
         if Check(i) gt '' then Do;
                Flag = 'Found';
              return;
         end;
    end;
    drop i;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Up to 40 obs from last table WORK.WANT total obs=5 06APR2023:08:29:53                                                  */
    /* Obs     P1    P2    P3    P4    P5    P6    P7    P8    P9    P10    FLAG                                              */
    /*                                                                                                                        */
    /*  1      PM    MM    JM    MM    PM    PB                PM           Found                                             */
    /*  2      PM    MM    JM    MM    PM    PB    JM                       Found                                             */
    /*  3      PM    MM    JM    MM    PM    MB    PM    MM                 Found                                             */
    /*  4      PM    MM    JM    MM    PM    PM    MM    MB                                                                   */
    /*  5      PM    MM    JM    MM    PM    PM    MM    PB                                                                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                        _       _            _
    __      ___ __  ___    __| | __ _| |_ __ _ ___| |_ ___ _ __
    \ \ /\ / / `_ \/ __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \
     \ V  V /| |_) \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) |
      \_/\_/ | .__/|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/
             |_|                                          |_|
    */

    /*--- input and output to sas work library ----*/
    /*--- easy way to take advantage of WPS products ----*/
    %let work=%sysfunc(pathname(work));

    %utl_submit_wps64("

    PROC SETiNIT;
    run;quit;

    libname wrk '&work';

    Data wrk.Want_wps;

    set wrk.have;
    Array Check P:;
    Do I =  Dim(Check) to 1 by -1;
         If Check(i) in ('PB', 'MB') then return;
         if Check(i) gt '' then Do;
                Flag = 'Found';
              return;
         end;
    end;
    drop i;
    run;

    ");

    proc print data=want_wps;
    run;quit;


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
