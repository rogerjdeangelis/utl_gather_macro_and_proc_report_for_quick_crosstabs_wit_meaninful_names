# utl_gather_macro_and_proc_report_for_quick_crosstabs_wit_meaninful_names
Gather macro and proc report for quick ptoc report crosstab with meaninful names Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Gather macro and proc report for quick crosstab with meaninful names

    * gather macro ( i changed
    * Alea Iacta aleaiacta95@gmail.com via listserv.uga.edu macro;
    * https://github.com/clindocu;

    * my rptRen macro on the end;

    INPUT
    =====

    WORK.HAVE total obs=3

      INCOME2015  EXPENSES2015  INCOME2016  EXPENSES2016  INCOME2017  EXPENSES2017 DEBT2015  DEBT2016  DEBT2017

         70000        75500        80000        60000        70000        81000       no       no        yes
         50000        52000        55000        42000        53000        60000       no       yes       yes
         80000        90000        99000        70000        75000        85000       no       no        no


    WORKING CODE
    ============

         %utl_gather(have,var,val,,havxpo,valformat=$12.,withFormats=Y); * note with formats;

         /*
         WORK.HAVXPO total obs=27

                VAR         VAL      _COLFORMAT    _COLTYP

            INCOME2015      70000     BEST12.         N
            EXPENSES2015    75500     BEST12.         N
            INCOME2016      80000     BEST12.         N
            EXPENSES2016    60000     BEST12.         N
            INCOME2017      70000     BEST12.         N
            EXPENSES2017    81000     BEST12.         N
            DEBTCNT2015     no        $3.             C
            DEBTCNT2016     no        $3.             C
            DEBTCNT2017     yes       $3.             C
         */

          * splitoff year and change yes to 1 and no to 0;

          data havCut;
            set havxpo;
            namx=compress(var,,'dd');
            year=compress(var,,'kd');
            if _coltyp='N' then answer=inputn(val,_colformat); * use format from gather;
            else answer=(val='yes');
            drop var val _colformat _coltyp;
          run;quit;

          /*
          WORK.HAVCUT total obs=27

              NAMX      YEAR    ANSWER

            INCOME      2015     70000
            EXPENSES    2015     75500
            INCOME      2016     80000
            EXPENSES    2016     60000
            INCOME      2017     70000
            EXPENSES    2017     81000
            DEBTCNT     2015         0
            DEBTCNT     2016         0
            DEBTCNT     2017         1
           */

           /* I do not care about the report */
           proc report data=havCut
               nowd missing      /* CALLING RENAME MACRO */
               out=rptXpo(rename=(%rptRen(havcut)) drop=_break_);
           cols year namx, answer;
           define year / group "year" width=12;
           define namx / group across "" width=12;
           define answer /analysis sum "";
           run;quit;


           /*
           WORK.RPTXPO total obs=3

              YEAR   DEBTCNT  EXPENSES    INCOME

              2015      0      217500     200000
              2016      1      172000     234000
              2017      2      226000     198000
           */

         * you need to use gather and my renaming macro for this to work;

         I made these changes so I could run in 9.4M2

         /*_ColFor2=strip(vformatn(%scan(&COLNAMES, &I)));*/
         /* _ColTyp=fmtinfo(_ColFor2, 'cat');  SAS 9.4 M5 above */
         _ColTyp=vtypex("%scan(&COLNAMES, &I)");

         * renaming _C#_ variables to meaninful names;
         rptRen macro is on the end - only works for simple x*y crosstabs

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    data have;
      format
         income2015 expenses2015
         income2016 expenses2016
         income2017 expenses2017 best.
         debtCnt2015
         debtCnt2016
         debtCnt2017 $3.;
      input
        income2015 expenses2015 debtCnt2015$
        income2016 expenses2016 debtCnt2016$
        income2017 expenses2017 debtCnt2017$
     ;
    cards4;
    70000 75500 no 80000 60000 no 70000 81000 yes
    50000 52000 no 55000 42000 yes 53000 60000 yes
    80000 90000 no 99000 70000 no 75000 85000 no
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|




      %utl_gather(have,var,val,,havxpo,valformat=$12.,withFormats=Y); * note with formats;

     * splitoff year and change yes to 1 and no to 0;
     data havCut;
       set havxpo;
       namx=compress(var,,'dd');
       year=compress(var,,'kd');
       if _coltyp='N' then answer=inputn(val,_colformat); * use format from gather;
       else answer=(val='yes');
       drop var val _colformat _coltyp;
     run;quit;


      /* I do not care about the report */
      proc report data=havCut
          nowd missing      /* calling rename macro */
          out=rptXpo(rename=(%rptRen(havcut)) drop=_break_);
      cols year namx, answer;
      define year / group "year" width=12;
      define namx / group across "" width=12;
      define answer /analysis sum "";
      run;quit;

    *           _   ____
     _ __ _ __ | |_|  _ \ ___ _ __    _ __ ___   __ _  ___ _ __ ___
    | '__| '_ \| __| |_) / _ \ '_ \  | '_ ` _ \ / _` |/ __| '__/ _ \
    | |  | |_) | |_|  _ <  __/ | | | | | | | | | (_| | (__| | | (_) |
    |_|  | .__/ \__|_| \_\___|_| |_| |_| |_| |_|\__,_|\___|_|  \___/
         |_|
    ;
    %symdel nams / nowarn;
    %macro rptRen(nrm);
    %global name;
    %let rc=%sysfunc(dosubl('
       proc sql noprint;
         select cats("_C",put(monotonic()+1,1.),"_ =",liquidity) into :nams separated by " "
         from
          (select distinct liquidity from &nrm)
       ;quit;'));
    &nams
    %mend rptRen;


