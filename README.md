# utl-convert-mutiple-txt-files-into-sas-tables-with-logging
Convert mutiple txt files into sas tables with logging
    Convert mutiple txt files into sas tables with logging

    User did not want to use import and wanted to ignore first row with column names

    github
    https://tinyurl.com/y8ctwbra
    https://github.com/rogerjdeangelis/utl-convert-mutiple-txt-files-into-sas-tables-with-logging

    SAS Forum
    https://tinyurl.com/yaqvuocs
    https://communities.sas.com/t5/New-SAS-User/Use-2-dependant-variables-in-a-macro-loop/m-p/523411


    INPUT
    =====

     Create CSVs

       options firstobs=1  obs=5 ;dm 'dexport sashelp.class  "d:/csv/import217_anderson.csv" replace';
       options firstobs=6  obs=10;dm 'dexport sashelp.class "d:/csv/wilson_skj.csv" replace';
       options firstobs=11 obs=15;dm 'dexport sashelp.class "d:/csv/pelbigone_fhz_rtf.csv" replace';
       options firstobs=1  obs=max;


       d:/csv/import217_anderson.csv

         NAME,SEX,AGE,HEIGHT,WEIGHT
         Alfred,M,14,69,112.5
         Alice,F,13,56.5,84
         Barbara,F,13,65.3,98
         Carol,F,14,62.8,102.5
         Henry,M,14,63.5,102.5

       d:/csv/wilson_skj.csv"

         NAME,SEX,AGE,HEIGHT,WEIGHT
         James,M,12,57.3,83
         Jane,F,12,59.8,84.5
         Janet,F,15,62.5,112.5
         Jeffrey,M,13,62.5,84
         John,M,12,59,99.5

       d:/csv/pelbigone_fhz_rtf.csv"

         NAME,SEX,AGE,HEIGHT,WEIGHT
         Joyce,F,11,51.3,50.5
         Judy,F,14,64.3,90
         Louise,F,12,56.3,77
         Mary,F,15,66.5,112
         Philip,M,16,72,150



    EXAMPLE OUTPUT
    --------------

    WORK.LOG total obs=3

    Obs     TABLE     PLAYER                       STATUS

     1     player1    Frank Anderson      Table creation sucessful
     2     player2    Tom Wilson          Table creation sucessful
     3     player3    Andrew Pelbigone    Table creation sucessful


    PROCESS
    =======

    data log (keep=table player status) ;
      array files[3] $32 ("import217_anderson.csv", "wilson_skj.csv" , "pelbigone_fhz_rtf.csv");
      array players[3] $32 ("Frank Anderson","Tom Wilson","Andrew Pelbigone");
      do idx=1 to 3;
         call symputx('player',players[idx]);
         call symputx('file',files[idx]);
         call symputx('idx',idx);
         rc=dosubl('
            data player&idx.;
               length player $32;
               infile "d:/csv/&file" delimiter="," DSD firstobs=2;
               informat NAME $8.  SEX $1.  AGE HEIGHT WEIGHT best12.;
               input NAME $ SEX $ AGE HEIGHT WEIGHT ;
               player="&player";
            run;quit;
            %let cc=&syserr;
         ');

          table = cats("player",idx);
          player=players[idx];


         if symgetn('cc')=0 then  status="Table creation sucessful";
         else status="Table creation failed";

         output;
      end;
      stop;
    run;quit;



