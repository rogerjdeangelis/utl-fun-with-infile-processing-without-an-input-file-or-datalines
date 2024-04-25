# utl-fun-with-infile-processing-without-an-input-file-or-datalines
Fun with infile processing without an input file or datalines
    %let pgm=utl-fun-with-infile-processing-without-an-input-file-or-datalines;

    Fun with infile processing without an input file or datalines

       1. Parsing name and address (has aplications to json/xml/htm;)
       2. Read variable number of trailers
       3  Sorting a string alphabetically

    github
    https://tinyurl.com/35sbhehm
    https://github.com/rogerjdeangelis/utl-fun-with-infile-processing-without-an-input-file-or-datalines

    /****************************************************************************************************************************************/
    /*                                                             |                            |                                           */
    /*                  INPUT                                      |       PROCESS              |               OUTPUT                      */
    /*                  =====                                      |       =======              |               ======                      */
    /*                                                             |                            |                                           */
    /* 1. PARSING NAME AND ADDRESS                                 |                            |                                           */
    /*                                                             |                            |                                           */
    /*                                                             |                            |                                           */
    /*  data have;                                                 | data want(drop=lyn) ;      | NAME   STREET   CITY     STATE  ZIP       */
    /*    input ;                                                  |  set have;                 |                                           */
    /*    lyn=_infile_;                                            |  informat name street      | MARY  1 MAIN ST CHICAGO   IL   85750      */
    /*  cards4;                                                    |     city state zip $12.;   | ROGER 2 MAIN ST NEW YORK  NY   51301      */
    /*  NAME-MARY  ADR-1 MAIN ST  CITY-CHICAGO  ST-IL  ZIP-85750   |  infile cards ;            |                                           */
    /*  NAME-ROGER  ADR-2 MAIN ST  CITY-NEW YORK  ST-NY  ZIP-51301 |  if _n_=1 then input @;    |                                           */
    /*  ;;;;                                                       |  _infile_ = lyn;           |                                           */
    /*  run;quit;                                                  |  input                     |                                           */
    /*                                                             |     @'NAME-' name          |                                           */
    /*                             LYN                             |     @'ADR-' street &       |                                           */
    /*                                                             |     @'CITY-' city          |                                           */
    /*  NAME-MARY  ADR-1 MAIN ST  CITY-CHICAGO  ST-IL  ZIP-85750   |     @'ST-' state           |                                           */
    /*  NAME-ROGER  ADR-2 MAIN ST  CITY-NEW YORK  ST-NY  ZIP-51301 |     @'ZIP-' zip @1 @@;     |                                           */
    /*                                                             |     output;                |                                           */
    /*                                                             | cards;                     |                                           */
    /*                                                             | *                          |                                           */
    /*                                                             | ;;;;                       |                                           */
    /*                                                             | run;quit;                  |                                           */
    /*                                                             |                            |                                           */
    /*--------------------------------------------------------------------------------------------------------------------------------------*/
    /*                                                             |                            |                                           */
    /* 2. READ VARIABLE NUMBER OF TRAILERS                         |                            |                                           */
    /*                                                             |                            |                                           */
    /*                  LYN                                        | data want(drop=lyn) ;      |   NUM_                                    */
    /*                                                             |  set have;                 | TRAILERS    PERSON    MONTH    PAYMENT    */
    /*   2  Mary  Jan  46  Feb  23                                 |  informat num_trailers 1.  |                                           */
    /*   3  Alex  Jan  99  Feb  44  Mar  45                        |       person month         |     2        Mary      Jan       46       */
    /*                                                             |       payment $8.;         |     2        Mary      Feb       23       */
    /*                                                             |  infile cards ;            |     3        Alex      Jan       99       */
    /*                                                             |  input @;                  |     3        Alex      Feb       44       */
    /*                                                             |  _infile_ = lyn;           |     3        Alex      Mar       45       */
    /*                                                             |  input num_trailers &      |                                           */
    /*                                                             |    person & @;             |                                           */
    /*                                                             |    do i=1 to num_trailers; |                                           */
    /*                                                             |       input month &        |                                           */
    /*                                                             |         payment & @;       |                                           */
    /*                                                             |       output;              |                                           */
    /*                                                             |    end;                    |                                           */
    /*                                                             |    input @1 @@;            |                                           */
    /*                                                             | cards;                     |                                           */
    /*                                                             | *                          |                                           */
    /*                                                             | ;;;;                       |                                           */
    /*                                                             | run;quit;                  |                                           */
    /*                                                             |                            |                                           */
    /*--------------------------------------------------------------------------------------------------------------------------------------*/
    /*                                                             |                            |                                           */
    /* 3  SORTING A STRING ALPHABETICALLY                          |                            |                                           */
    /*                                                             |                            |                                           */
    /*  data have;                                                 | data want (drop=_:);       | Obs               STRING                  */
    /*    infile datalines;                                        |   set before;              |                                           */
    /*    input string $char70.;                                   |   infile cards ;           | 1  abcdeeefghhijklmnoooopqrrsttuuvwxyz    */
    /*  datalines;                                                 |   if _n_=1 then input  @;  | 2  aaeeeeeefhhhhiillnoooprrrrsssstttttw   */
    /*  the quick brown fox jumps over the lazy dog                |   array _ltrs {70} $1;     | 3  aceeeeefhhhimnnnnoorssttuuvw           */
    /*  here is another phrase with lots of letters                |   _infile_ = string;       |                                           */
    /*  when in the course of human events                         |   input (_LTRS1-_LTRS70)   |                                           */
    /*  run;                                                       |     ($1.) @1 @@;           |                                           */
    /*                                                             |   call sortc (of _ltrs{*});|                                           */
    /*                                                             |   string=cats(of _ltrs{*});|                                           */
    /*                                                             | cards;                     |                                           */
    /*                                                             | *                          |                                           */
    /*                                                             | run;                       |                                           */
    /*                                                             |                            |                                           */
    /*                                                             |                            |                                           */
    /****************************************************************************************************************************************/
    /*                        _                         _     _
    / |  _ __   __ _ _ __ ___(_)_ __   __ _    __ _  __| | __| |_ __ ___  ___ ___
    | | | `_ \ / _` | `__/ __| | `_ \ / _` |  / _` |/ _` |/ _` | `__/ _ \/ __/ __|
    | | | |_) | (_| | |  \__ \ | | | | (_| | | (_| | (_| | (_| | | |  __/\__ \__ \
    |_| | .__/ \__,_|_|  |___/_|_| |_|\__, |  \__,_|\__,_|\__,_|_|  \___||___/___/
     _  |_|              _            |___/
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have;
      input ;
      lyn=_infile_;
    cards4;
    NAME-MARY  ADR-1 MAIN ST  CITY-CHICAGO  ST-IL  ZIP-85750
    NAME-ROGER  ADR-2 MAIN ST  CITY-NEW YORK  ST-NY  ZIP-51301
    ;;;;
    run;quit;

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    data want(drop=lyn) ;
      set have;
      informat name street
         city state zip $12.;
      infile cards ;
      if _n_=1 then input @;
      _infile_ = lyn;
      input
          @'NAME-' name
          @'ADR-' street &
          @'CITY-' city  &
          @'ST-' state  &
          @'ZIP-' zip @1 @@;
      output;
    cards;
    *
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*WORK.WANT total obs=2 25APR2024:12:18:49                                                                                */
    /*                                                                                                                        */
    /* Obs    NAME      STREET        CITY      STATE     ZIP                                                                 */
    /*                                                                                                                        */
    /*  1     MARY     1 MAIN ST    CHICAGO      IL      85750                                                                */
    /*  2     ROGER    2 MAIN ST    NEW YORK     NY      51301                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                       _   _             _ _
    |___ \   _ __ ___  __ _  __| | | |_ _ __ __ _(_) | ___ _ __ ___
      __) | | `__/ _ \/ _` |/ _` | | __| `__/ _` | | |/ _ \ `__/ __|
     / __/  | | |  __/ (_| | (_| | | |_| | | (_| | | |  __/ |  \__ \
    |_____| |_|  \___|\__,_|\__,_|  \__|_|  \__,_|_|_|\___|_|  |___/
     _                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have;
      input ;
      lyn=_infile_;
    cards4;
    2  Mary  Jan  46  Feb  23
    3  Alex  Jan  99  Feb  44  Mar  45
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /* Obs                   LYN                                                                                              */
    /*                                                                                                                        */
    /*  1     2  Mary  Jan  46  Feb  23                                                                                       */
    /*  2     3  Alex  Jan  99  Feb  44  Mar  45                                                                              */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    data want(drop=lyn) ;
      set have;
      informat num_trailers 1.
           person month
           payment $8.;
      infile cards ;
      input @;
      _infile_ = lyn;
      input num_trailers & person & @;
        do i=1 to num_trailers;
           input month & payment & @;
           output;
        end;
        input @1 @@;
    cards;
    *
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*          NUM_                                                                                                          */
    /* Obs    TRAILERS    PERSON    MONTH    PAYMENT                                                                          */
    /*                                                                                                                        */
    /*  1         2        Mary      Jan       46                                                                             */
    /*  2         2        Mary      Feb       23                                                                             */
    /*  3         3        Alex      Jan       99                                                                             */
    /*  4         3        Alex      Feb       44                                                                             */
    /*  5         3        Alex      Mar       45                                                                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____                  _           _       _
    |___ /   ___  ___  _ __| |_    __ _| |_ __ | |__   __ _
      |_ \  / __|/ _ \| `__| __|  / _` | | `_ \| `_ \ / _` |
     ___) | \__ \ (_) | |  | |_  | (_| | | |_) | | | | (_| |
    |____/  |___/\___/|_|   \__|  \__,_|_| .__/|_| |_|\__,_|
     _                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have;
      infile datalines;
      input string $char70.;
    datalines;
    the quick brown fox jumps over the lazy dog
    here is another phrase with lots of letters
    when in the course of human events
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Obs                      STRING                                                                                        */
    /*                                                                                                                        */
    /*  1     the quick brown fox jumps over the lazy dog                                                                     */
    /*  2     here is another phrase with lots of letters                                                                     */
    /*  3     when in the course of human events                                                                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    data want (drop=_:);
      set before;
      infile cards ;
      if _n_=1 then input  @;
      array _ltrs {70} $1;
      _infile_ = string;
      input (_LTRS1-_LTRS70) ($1.) @1 @@;
      call sortc (of _ltrs{*});
      string=cats(of _ltrs{*});
    cards;
    *
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* Obs                   STRING                                                                                           */
    /*                                                                                                                        */
    /*  1     abcdeeefghhijklmnoooopqrrsttuuvwxyz                                                                             */
    /*  2     aaeeeeeefhhhhiillnoooprrrrsssstttttw                                                                            */
    /*  3     aceeeeefhhhimnnnnoorssttuuvw                                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
