# utl-pivot-long--excel-sheet-and-run-a-regression-in-r-and-python
Pivot long transpose an excel sheet and run a regression in r and python
    %let pgm=utl-pivot-long--excel-sheet-and-run-a-regression-in-r-and-python;

    %stop_submission;

    Pivot long transpose an excel sheet and run a regression in r and python

    github
    https://tinyurl.com/bhxrjawe
    https://github.com/rogerjdeangelis/utl-pivot-long--excel-sheet-and-run-a-regression-in-r-and-python

    Github Python create regression excel output
    https://tinyurl.com/5y8yrn7w
    https://github.com/rogerjdeangelis/utl-pivot-long--excel-sheet-and-run-a-regression-in-r-and-python/blob/main/pyregout.xlsx

    GithubR create regression excel output
    https://tinyurl.com/yysx9dfv
    https://github.com/rogerjdeangelis/utl-pivot-long--excel-sheet-and-run-a-regression-in-r-and-python/blob/main/regout.xlsx

        SOLUTIONS

           1 generate sql code

           2 create workbook

           3 r pivot regression
             SOAPBOX ON
               I prefer r for stat
             SAPBOX OFF

           4 python regression

             SOAPBOX ON
              ankle biters (too many datastructures?)
               anova = anova.reset_index()  * make index column part of a panda dataframe otherwise it will be removed from dataframe
               coef = coef.to_frame()       * coef is a series object so we need to make it a dataframe for normal access
               coef = coef.reset_index()    * even though you made it a data frame, you need to convert index to a column
             SOAPBOX OFF

           5 related repos

    Spending you tme learning r stats and openxls is worth it.


    Fat datasets are often less efficient and can be less flexible.
    I suspect the op does not have a table with dozens of columns?

    The sql transpose may not be as slow as you think, depends on the compiler.
    Genreally repetitive code can easily be parallized and
    has much better locality of reference keeping
    the needed data closer to the cpu.

    /*************************************************************************************************************************/
    /*  _ __                                                                        |                                        */
    /* | `__|                                                                       |                                        */
    /* | |                                                                          |                                        */
    /* |_|                                                                          |                                        */
    /*                                                                              |                                        */
    /*=======================================================================================================================*/
    /*                                                                              |                                        */
    /*                             INPUT                                            | OUTPUT (SHEETS ADDED TO  WORKBOOK      */
    /*                             ====-=                                           | =================================      */
    /*                                                                              |                                        */
    /*                                                                              | CONTENTS  d:/xls/regout.xlsx           */
    /* INPUT d:/xls/regout.xlsx sheet=HAVE                                          | SHEETS                                 */
    /*                                                                              |                                        */
    /* -----------------------+                                                     |  1 have      - input                   */
    /* | A1| fx    | COLUMN   |                                                     |  2 havxpo    - pivoted have            */
    /* -----------------------------------------------------------------+           |  3 predicted - range 'fitted values'   */
    /* [_] |    A     |    B    |    C    |    E    |    F    |    G    |           |                range 'coefficients'    */
    /* -----------------------------------------------------------------|           |  4 summary   - anova rsquare erors     */
    /*  1  | COLUMN   |   TYM1  |   TYM2  |  TYM3   |  TYM4   |  TYM5   |           |                                        */
    /*  -- |----------+---------+---------+---------+---------+---------+           | d:/xls/regout.xlsx SHEET HAVXPO (PIVOT)*/
    /*  2  |HEIGHT    | 69      | 56.5    | 65.3    | 62.8    | 63.5    |           |                                        */
    /*  -- |----------+---------+---------+---------+---------+---------+           |  -------------------------+            */
    /*  3  |WEIGHT    | 98.1    | 84      | 98      | 88.5    | 80.5    |           |  | A1| fx    | HEIGHT     |            */
    /*  -- |----------+---------+---------+---------+---------+---------+           |  --------------------------            */
    /*  ...                                                                         |  [_] |    A     |    B    |            */
    /* [HAVE]                                                                       |  --------------------------            */
    /*                                                                              |   1  | HEIGHT   | WEIGHT  |            */
    /* GENERATED SQL PIVOT CADE                                                     |   -- |----------+---------+            */
    /*                                                                              |   2  |69        | 98.1    |            */
    /* select max(case when column='WEIGHT' then tym1 else null end)  as weight     |   -- |----------+---------+            */
    /* ,max(case when column='HEIGHT' then tym1 else null end)  as height           |   3  |56.5      | 84      |            */
    /*  from sd1.have union all                                                     |   -- |----------+---------+            */
    /* select max(case when column='WEIGHT' then tym2 else null end)  as weight     |   4  |65.3      | 98      |            */
    /* ,max(case when column='HEIGHT' then tym2 else null end)  as height           |   -- |----------+---------+            */
    /*  from sd1.have union all                                                     |   5  |62.8      | 88.5    |            */
    /* select max(case when column='WEIGHT' then tym3 else null end)  as weight     |   -- |----------+---------+            */
    /* ,max(case when column='HEIGHT' then tym3 else null end)  as height           |   6  |63.5      | 80.5    |            */
    /*  from sd1.have union all                                                     |   -- |----------+---------+            */
    /* select max(case when column='WEIGHT' then tym4 else null end)  as weight     |   [HAVXPO}                             */
    /* ,max(case when column='HEIGHT' then tym4 else null end)  as height           |                                        */
    /*  from sd1.have union all                                                     |  SHEET PREDICTED (TWO RANGES)          */
    /* select max(case when column='WEIGHT' then tym5 else null end)  as weight     |                                        */
    /*  from sd1.have union all                                                     |  --------------------+                 */
    /* ,max(case when column='HEIGHT' then tym5 else null end)  as height           |  | A1| fx    |HEIGHT|                  */
    /*                                                                              |  ------------------------------------- */
    /* SQL GERERATOR                                                                |  [_] |   A   | B    |    C    |    E | */
    /*                                                                              |  ------------------------------------- */
    /* array(_tym,values=1-5);                                                      |   1  | HEIGHT|WEIGHT|PREDICTED| RESID| */
    /*                                                                              |   -- |-------+------+---------+------+ */
    /* data _null_;                                                                 |   2  |98.1   |69    | 66.6918 | 2.308| */
    /*  %do_over(_tym,phrase=%str(                                                  |   -- |-------+------+---------+------+ */
    /*  put "select max(case when column='WEIGHT' then tym? else . end) as weight"; |   3  |84     |56.5  | 61.1202 | -4.62| */
    /*   put                                                                        |   -- |-------+------+---------+------+ */
    /*   ",max(case when column='HEIGHT' then tym? else NULL end) as height"        |   4  |98     |65.3  | 66.6523 | -1.35| */
    /*      " from sd1.have union all";)                                            |   -- |-------+------+---------+------+ */
    /*   );                                                                         |   5  |88.5   |62.8  | 62.8984 | -0.09| */
    /* run;quit;                                                                    |   -- |-------+------+---------+------+ */
    /*                                                                              |   6  |80.5   |63.5  | 59.7372 | 3.762| */
    /* CREATE INPUT EXCEL WORKBOOK WITH SHEET HAVE                                  |   -- |-------+------+---------+------+ */
    /*                                                                              |  [PREDICTED]                           */
    /* %utlfkil(d:/xls/wantxl.xlsx);                                                |                                        */
    /*                                                                              |                                        */
    /* %utl_rbeginx;                                                                |  COEFFICIENTS (ROWS 7-9)               */
    /* parmcards4;                                                                  |                                        */
    /* library(openxlsx)                                                            |  -------------------------+            */
    /* library(sqldf)                                                               |  | A1| fx       | PARAMTR |            */
    /* library(haven)                                                               |  --------------------------            */
    /* have<-read_sas("d:/sd1/have.sas7bdat")                                       |  [_] |    A     |    B    |            */
    /* have                                                                         |  --------------------------            */
    /* wb <- createWorkbook()                                                       |   7  |PARAMTR   |VALUE    |            */
    /* addWorksheet(wb, "have")                                                     |   -- |----------+---------+            */
    /* writeData(wb, sheet = "have", x = have)                                      |   8  |INTERCEPT | 27.928  |            */
    /* saveWorkbook(                                                                |   -- |----------+---------+            */
    /*     wb                                                                       |   9  |SLOPE     | 0.394   |            */
    /*    ,"d:/xls/regout.xlsx"                                                     |   -- |----------+---------+            */
    /*    ,overwrite=TRUE)                                                          |   [PREDICTED]                          */
    /* ;;;;                                                                         |                                        */
    /* %utl_rendx;                                                                  |                                        */
    /*                                                                              |   SAME WORKOOK SHEET SUMMARY           */
    /*------------------------------------------------------------------------------+                                        */
    /*                                                        |                          SHEET SUMMARY                       */
    /*                                                        |    ---------------------                                     */
    /*                                                        |    | A1| fx    | CALL  |                                     */
    /*                                                        |    --------------------------------------------------------- */
    /*                                                        |    [_] |                   A                               | */
    /*                                                        |    --------------------------------------------------------  */
    /*                                                        |     1 |Call:                                               | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |     2 |lm(formula = havxpo$height ~ havxpo$weight)         | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |     3 |Residuals:                                          | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |     4 |1       2       3       4       5                   | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |     5 |2.3082  4.6202  1.3523  0.0984  3.7628              | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |     6 |Coefficients:                                       | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |     7 |Estimate          Std.     Error  t value  Pr(>|t|) | */
    /*                                                        |    -- -                                                      */
    /*                                                        |     8 |(Intercept)    27.9277    21.1591   1.320    0.279  | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |     9 |havxpo$weight   0.3951     0.2348   1.683    0.191  | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |    10 |Residual standard : 3.771 on 3 degrees of freedom   | */
    /*                                                        |    -- +                                                    + */
    /*                                                        |    11 |Multiple R squared:0.4856 Adjusted R squared: 0.3141| */
    /*                                                        |    -- +                                                    + */
    /*                                                        |    12 |F statistic: 2.832 on 1 and 3 DF,  p value: 0.191   | */
    /*                                                        |    -- -----------------------------------------------------  */
    /*                                                        |    [SUMMARY]                                                 */
    /*                                                                                                                       */
    /*=======================================================================================================================*/
    /*              _   _                                    _               _               _                              */
    /*  _ __  _   _| |_| |__   ___  _ __    _____  _____ ___| |   ___  _   _| |_ _ __  _   _| |_                            */
    /* | `_ \| | | | __| `_ \ / _ \| `_ \  / _ \ \/ / __/ _ \ |  / _ \| | | | __| `_ \| | | | __|                           */
    /* | |_) | |_| | |_| | | | (_) | | | ||  __/>  < (_|  __/ | | (_) | |_| | |_| |_) | |_| | |_                            */
    /* | .__/ \__, |\__|_| |_|\___/|_| |_| \___/_/\_\___\___|_|  \___/ \__,_|\__| .__/ \__,_|\__|                           */
    /* |_|    |___/                                                             |_|                                         */
    /*                                                                                                                      */
    /*=======================================================================================================================*/
    /*                                                                                                                       */
    /*                                                                                                                       */
    /* d:/xls/regout.xlsx                                                                                                    */
    /*                                                                                                                       */
    /* SHEET HAVXPO (PIVOT)            PREDICTED SHEET                       ANOVA                    COEFFICIENTS SHEET     */
    /*                                                                                                                       */
    /* --------------------      -----------------+               ---------------------              ----------------------+ */
    /* | A1| fx    |HEIGHT|     |   A1|fx     |HEIGHT   |         | A1| fx    | CALL  |              | A1| fx      |PARAMTR| */
    /* --------------------     --------------------------------  ---------------------------------  ----------------------- */
    /* [_] |    A  |    B |[ [_]|   A  | B    |    C    |    E |  [_]|   A   |  B   |C |  D |   E |  [_] |    A    |    B  | */
    /* ---------------------  ----------------------------------  ---------------------------------  ----------------------- */
    /*  1  | HEIGHT| WEIGH|   1 |HEIGHT|WEIGHT|PREDICTED| RESID|   1 | INDEX |SUM_SQ|DF|  F | PR_F|   1  | INDEX   |  V0E  | */
    /*  -- |-------+------+   - |------+------+---------+------+  -- +-------+------+--+----+-----+   -- |---------+-------+ */
    /*  2  |69     | 98.1 |   2 |98.1  |69    | 66.6918 | 2.308|   2 | weight| 40.28|1 |2.83|0.191|   2  |INTERCEPT| 27.93 | */
    /*  -- |-------+------+   - |------+------+---------+------+  -- +-------+------+--+----+-----+   -  |---------+-------+ */
    /*  3  |56.5   | 84   |   3 |84    |56.5  | 61.1202 | -4.62|   3 | height| 42.67|3 |    |     |   3  |SLOPE    | 0.394 | */
    /*  -- |-------+------+   - |------+------+---------+------+  ---------------------------------   -- |---------+-------+ */
    /*  4  |65.3   | 98   |   4 |98    |65.3  | 66.6523 | -1.35|                                      [COEF]                 */
    /*  -- |-------+------+   - |------+------+---------+------+                                                             */
    /*  5  |62.8   | 88.5 |   5 |88.5  |62.8  | 62.8984 | -0.09|                                                             */
    /*  -- |-------+------+   - |------+------+---------+------+                                                             */
    /*  6  |63.5   | 80.5 |   6 |80.5  |63.5  | 59.7372 | 3.762|                                                             */
    /*  -- |-------+------+   -- |------+------+---------+-----+                                                             */
    /*                        [PREDICTED]                                                                                    */
    /*                                                                                                                       */
    /*                                                                                                                       */
    /*                                     SHEET SUMMARY                                                                     */
    /*                                                                                                                       */
    /*   ---------------------                                                                                               */
    /*   | A1| fx    model   |                                                                                               */
    /*   ---------------------------------------------------------------------------------------                             */
    /*   [_] |                   A                                                             |                             */
    /*    --------------------------------------------------------------------------------------                             */
    /*    1 |  Model:                            OLS   Adj. R-squared:                  0.314  |                             */
    /*   -- +                                                                                  +                             */
    /*    2 |  Method:                 Least Squares   F-statistic:                     2.832  |                             */
    /*   -- +                                                                                  +                             */
    /*    3 |  Date:                Wed, 29 Jan 2025   Prob (F-statistic):              0.191  |                             */
    /*   -- +                                                                                  +                             */
    /*    4 |  Time:                        15:12:35   Log-Likelihood:                -12.455  |                             */
    /*   -- +                                                                                  +                             */
    /*    5 |  No. Observations:                   5   AIC:                             28.91  |                             */
    /*   -- +                                                                                  +                             */
    /*    6 |  Df Residuals:                       3   BIC:                             28.13  |                             */
    /*   -- +                                                                                  +                             */
    /*    7 |  Df Model:                           1                                           |                             */
    /*   -- -                                                                                  -                             */
    /*    8 |  Covariance Type:            nonrobust                                           |                             */
    /*   -- +                                                                                  +                             */
    /*    9 |                   coef    std err          t      P>|t|      [0.025      0.975]  |                             */
    /*   -- +                                                                                  +                             */
    /*   10 |--------------------------------------------------------------------------------- |                             */
    /*   -- +                                                                                  +                             */
    /*   11 |  Intercept     27.9277     21.159      1.320      0.279     -39.410      95.266  |                             */
    /*   -- +                                                                                  +                             */
    /*   12 |  weight         0.3951      0.235      1.683      0.191      -0.352       1.142  |                             */
    /*   -- |                                                                                  |                             */
    /*   13 +  Omnibus:                          nan   Durbin-Watson:                   1.761  +                             */
    /*   -- |                                                                                  |                             */
    /*   14 +  Prob(Omnibus):                    nan   Jarque-Bera (JB):                0.325  +                             */
    /*   -- |                                                                                  |                             */
    /*   15 +  Skew:                          -0.285   Prob(JB):                        0.850  +                             */
    /*   -- |                                                                                  |                             */
    /*   16 +  Kurtosis:                       1.889   Cond. No.                     1.13e+03  +                             */
    /*   -- |                                                                                  |                             */
    /*   17 +  Notes:                                                                          +                             */
    /*   -- |                                                                                  |                             */
    /*   18 + [1] Standard Errors assume that the covariance matrix of the errors is specified +                             */
    /*   -- |                                                                                  |                             */
    /*   19 - [2] The condition number is large, 1.13e+03. This might indicate that there are  +                             */
    /*   -- |                                                                                  |                             */                                                                                              */
    /*    --------------------------------------------------------------------------------------                             */                                                                                              */
    /*  [SUMMARY]                                                                                                            */                                                                                              */
    /*                                                                                                                       */                                                                                            */
    /*************************************************************************************************************************/

    / |   __ _  ___ _ __   ___ _ __ __ _| |_ ___   ___  __ _| |
    | |  / _` |/ _ \ `_ \ / _ \ `__/ _` | __/ _ \ / __|/ _` | |
    | | | (_| |  __/ | | |  __/ | | (_| | ||  __/ \__ \ (_| | |
    |_|  \__, |\___|_| |_|\___|_|  \__,_|\__\___| |___/\__, |_|
         |___/                                            |_|
    */

    array(_tym,values=1-5);

    data _null_;
     %do_over(_tym,phrase=%str(
     put "select max(case when column='WEIGHT' then tym? else . end) as weight";
      put
      ",max(case when column='HEIGHT' then tym? else NULL end) as height"
         " from sd1.have union all";)
      );
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  PASTE THIS CODE INTO SQL MINOR EDITIONG                                                                               */
    /*                                                                                                                        */
    /*  IN THE LOG                                                                                                            */
    /*                                                                                                                        */
    /*  ,max(case when column='HEIGHT' then tym1 else NULL end) as height from sd1.have union all                             */
    /*  select max(case when column='WEIGHT' then tym2 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym2 else NULL end) as height from sd1.have union all                             */
    /*  select max(case when column='WEIGHT' then tym3 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym3 else NULL end) as height from sd1.have union all                             */
    /*  select max(case when column='WEIGHT' then tym4 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym4 else NULL end) as height from sd1.have union all                             */
    /*  select max(case when column='WEIGHT' then tym5 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym5 else NULL end) as height from sd1.have union all                             */
    /*                                                                                                                        */
    /*  MINOR EDITS                                                                                                           */
    /*                                                                                                                        */
    /*  REMOVE FIRST COMMA AND LAST UNION ALL                                                                                 */
    /*                                                                                                                        */
    /*  max(case when column='HEIGHT' then tym1 else NULL end) as height from sd1.have union all                              */
    /*  select max(case when column='WEIGHT' then tym2 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym2 else NULL end) as height from sd1.have union all                             */
    /*  select max(case when column='WEIGHT' then tym3 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym3 else NULL end) as height from sd1.have union all                             */
    /*  select max(case when column='WEIGHT' then tym4 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym4 else NULL end) as height from sd1.have union all                             */
    /*  select max(case when column='WEIGHT' then tym5 else . end) as weight                                                  */
    /*  ,max(case when column='HEIGHT' then tym5 else NULL end) as height from sd1.have                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                        _                           _    _                 _      _                   _
    |___ \    ___ _ __ ___  __ _| |_ ___ __      _____  _ __| | _| |__   ___   ___ | | __ (_)_ __  _ __  _   _| |_
      __) |  / __| `__/ _ \/ _` | __/ _ \\ \ /\ / / _ \| `__| |/ / `_ \ / _ \ / _ \| |/ / | | `_ \| `_ \| | | | __|
     / __/  | (__| | |  __/ (_| | ||  __/ \ V  V / (_) | |  |   <| |_) | (_) | (_) |   <  | | | | | |_) | |_| | |_
    |_____|  \___|_|  \___|\__,_|\__\___|  \_/\_/ \___/|_|  |_|\_\_.__/ \___/ \___/|_|\_\ |_|_| |_| .__/ \__,_|\__|
                                                                                                  |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    input
     COLUMN$  tym1 tym2 tym3 tym4 tym5;
    cards4;
    HEIGHT 69.0 56.5 65.3 62.8 63.5
    WEIGHT 98.1 84.0 98.0 88.5 80.5
    ;;;;
    run;quit;


    %utlfkil(d:/xls/regout.xlsx);

    %utl_rbeginx;
    parmcards4;
    library(openxlsx)
    library(sqldf)
    library(haven)
    have<-read_sas("d:/sd1/have.sas7bdat")
    have
    wb <- createWorkbook()
    addWorksheet(wb, "have")
    writeData(wb, sheet = "have", x = have)
    saveWorkbook(
        wb
       ,"d:/xls/regout.xlsx"
       ,overwrite=TRUE)
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  d:/xls/regout.xlsx                                                                                                    */
    /*                                                                                                                        */
    /*  SHEET HAVXPO (PIVOT)                                                                                                  */
    /*                                                                                                                        */
    /*  -------------------------+                                                                                            */
    /*  | A1| fx    | HEIGHT     |                                                                                            */
    /*  --------------------------                                                                                            */
    /*  [_] |    A     |    B    |                                                                                            */
    /*  --------------------------                                                                                            */
    /*   1  | HEIGHT   | WEIGHT  |                                                                                            */
    /*   -- |----------+---------+                                                                                            */
    /*   2  |69        | 98.1    |                                                                                            */
    /*   -- |----------+---------+                                                                                            */
    /*   3  |56.5      | 84      |                                                                                            */
    /*   -- |----------+---------+                                                                                            */
    /*   4  |65.3      | 98      |                                                                                            */
    /*   -- |----------+---------+                                                                                            */
    /*   5  |62.8      | 88.5    |                                                                                            */
    /*   -- |----------+---------+                                                                                            */
    /*   6  |63.5      | 80.5    |                                                                                            */
    /*   -- |----------+---------+                                                                                            */
    /*   [HAVXPO}                                                                                                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____               _            _                                     _
    |___ /  _ __   _ __ (_)_   _____ | |_  _ __ ___  __ _ _ __ ___  ___ ___(_) ___  _ __
      |_ \ | `__| | `_ \| \ \ / / _ \| __|| `__/ _ \/ _` | `__/ _ \/ __/ __| |/ _ \| `_ \
     ___) || |    | |_) | |\ V / (_) | |_ | | |  __/ (_| | | |  __/\__ \__ \ | (_) | | | |
    |____/ |_|    | .__/|_| \_/ \___/ \__||_|  \___|\__, |_|  \___||___/___/_|\___/|_| |_|
                  |_|                               |___/
    */

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    library(openxlsx)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    havxpo<-sqldf("
     select max(case when column='WEIGHT' then tym1 else null end)  as weight
     ,max(case when column='HEIGHT' then tym1 else null end)  as height from have union all
     select max(case when column='WEIGHT' then tym2 else null end)  as weight
     ,max(case when column='HEIGHT' then tym2 else null end)  as height from have union all
     select max(case when column='WEIGHT' then tym3 else null end)  as weight
     ,max(case when column='HEIGHT' then tym3 else null end)  as height from have union all
     select max(case when column='WEIGHT' then tym4 else null end)  as weight
     ,max(case when column='HEIGHT' then tym4 else null end)  as height from have union all
     select max(case when column='WEIGHT' then tym5 else null end)  as weight
     ,max(case when column='HEIGHT' then tym5 else null end)  as height from have
     ");
    havxpo
    model          <-  lm(havxpo$height ~ havxpo$weight)
    str(model)
    havxpo$fitted  <-  model$fitted.values
    havxpo$resid   <-  model$residuals
    havxpo
    coef    <-  model$coefficients
    coef    <-  cbind(c("INTERCEPT","SLOPE"),coef)
    colnames(coef)<-c("PARAMTR","VALUE")
    coef
    temp_file <- tempfile(pattern = "myfile", fileext = ".txt")
    sink(temp_file)
    summary(model)
    sink();
    wb <- loadWorkbook("d:/xls/regout.xlsx")
    addWorksheet(wb, "predicted")
    addWorksheet(wb, "havxpo")
    writeData(wb, sheet = "havxpo", x = havxpo[,1:2])
    writeData(wb, sheet = "predicted", x = havxpo, startCol = 1)
    writeData(wb, sheet = "predicted", x = coef,startCol=1, startRow = nrow(havxpo)+2)
    addWorksheet(wb, "summary")
    text_content <- readLines(temp_file)
    writeData(wb, "summary", text_content, startRow = 1, startCol = 1)
    saveWorkbook(wb, "d:/xls/regout.xlsx", overwrite = TRUE)
    fn_tosas9x(
          inp    = coef
         ,outlib ="d:/sd1/"
         ,outdsn ="coef"
         )
    fn_tosas9x(
          inp    = havxpo
         ,outlib ="d:/sd1/"
         ,outdsn ="predicted"
         )
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* CONTENTS  d:/xls/regout.xlsx                                                                                           */
    /*                                                                                                                        */
    /* SHEETS                                                                                                                 */
    /*                                                                                                                        */
    /* 1 have      - input                                                                                                    */
    /* 2 havxpo    - pivoted have                                                                                             */
    /* 3 predicted - range 'fitted values'                                                                                    */
    /*               range 'coefficients'                                                                                     */
    /* 4 summary   - anova rsquare erors                                                                                      */
    /*                                                                                                                        */
    /* d:/xls/regout.xlsx                                                                                                     */
    /*                                                                                                                        */
    /* SHEET HAVXPO (PIVOT       SHEET PREDICTED (TWO RANGES)                           SHEET SUMMARY                         */
    /*                                                                                                                        */
    /* --------------------+ ------------------+                   ---------------------                                      */
    /* | A1| fx    |HEIGHT | | A1| fx   |HEIGHT|                   | A1| fx    | CALL  |                                      */
    /* --------------------- ------------------------------------  ---------------------------------------------------------  */
    /* [_] |    A  |    B  | [_]|   A   | B    |    C    |    E |  [_] |                   A                               |  */
    /* --------------------- ------------------------------------   --------------------------------------------------------  */
    /*  1  |HEIGHT |WEIGT  |  1 | HEIGHT|WEIGHT|PREDICTED| RESID|   1 |Call:                                               |  */
    /*  -- |-------+-------+  - |-------+------+---------+------+  -- +                                                    +  */
    /*  2  |69     | 98.1  |  2 |98.1   |69    | 66.6918 | 2.308|   2 |lm(formula = havxpo$height ~ havxpo$weight)         |  */
    /*  -- |-------+-------+  - |-------+------+---------+------+  -- +                                                    +  */
    /*  3  |56.5   | 84    |  3 |84     |56.5  | 61.1202 | -4.62|   3 |Residuals:                                          |  */
    /*  -- |-------+-------+  - |-------+------+---------+------+  -- +                                                    +  */
    /*  4  |65.3   | 98    |  4 |98     |65.3  | 66.6523 | -1.35|   4 |1       2       3       4       5                   |  */
    /*  -- |-------+-------+  - |-------+------+---------+------+  -- +                                                    +  */
    /*  5  |62.8   | 88.5  |  5 |88.5   |62.8  | 62.8984 | -0.09|   5 |2.3082  4.6202  1.3523  0.0984  3.7628              |  */
    /*  -- |-------+-------+  - |-------+------+---------+------+  -- +                                                    +  */
    /*  6  |63.5   | 80.5  |  6 |80.5   |63.5  | 59.7372 | 3.762|   6 |Coefficients:                                       |  */
    /*  -- |-------+-------+  -- |-------+------+---------+-----+  -- +                                                    +  */
    /*  [HAVXPO}             [PREDICTED]                            7 |Estimate          Std.     Error  t value  Pr(>|t|) |  */
    /*                                                             -- -                                                       */
    /*                       COEFFICIENTS (ROWS 7-9)                8 |(Intercept)    27.9277    21.1591   1.320    0.279  |  */
    /*                                                             -- +                                                    +  */
    /*                       -------------------------+             9 |havxpo$weight   0.3951     0.2348   1.683    0.191  |  */
    /*                       | A1| fx       | PARAMTR |            -- +                                                    +  */
    /*                       --------------------------            10 |Residual standard : 3.771 on 3 degrees of freedom   |  */
    /*                       [_] |    A     |    B    |            -- +                                                    +  */
    /*                       --------------------------            11 |Multiple R squared:0.4856 Adjusted R squared: 0.3141|  */
    /*                        7  |PARAMTR   |VALUE    |            -- +                                                    +  */
    /*                        -- |----------+---------+            12 |F statistic: 2.832 on 1 and 3 DF,  p value: 0.191   |  */
    /*                        8  |INTERCEPT | 27.928  |            -- -----------------------------------------------------   */
    /*                        -  |----------+---------+           [SUMMARY]                                                   */
    /*                        9  |SLOPE     | 0.394   |                                                                       */
    /*                        -- |----------+---------+                                                                       */
    /*                        [COEF]                                                                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*
        _                 _   _                                                   _
    | || |    _ __  _   _| |_| |__   ___  _ __   _ __ ___  __ _ _ __ ___  ___ ___(_) ___  _ __
    | || |_  | `_ \| | | | __| `_ \ / _ \| `_ \ | `__/ _ \/ _` | `__/ _ \/ __/ __| |/ _ \| `_ \
    |__   _| | |_) | |_| | |_| | | | (_) | | | || | |  __/ (_| | | |  __/\__ \__ \ | (_) | | | |
       |_|   | .__/ \__, |\__|_| |_|\___/|_| |_||_|  \___|\__, |_|  \___||___/___/_|\___/|_| |_|
             |_|    |___/                                 |___/
                         _                            _    _                 _                 _                   _
      ___ _ __ ___  __ _| |_ ___  __      _____  _ __| | _| |__   ___   ___ | | __ __      __ (_)_ __  _ __  _   _| |_
     / __| `__/ _ \/ _` | __/ _ \ \ \ /\ / / _ \| `__| |/ / `_ \ / _ \ / _ \| |/ / \ \ /\ / / | | `_ \| `_ \| | | | __|
    | (__| | |  __/ (_| | ||  __/  \ V  V / (_) | |  |   <| |_) | (_) | (_) |   <   \ V  V /  | | | | | |_) | |_| | |_
     \___|_|  \___|\__,_|\__\___|   \_/\_/ \___/|_|  |_|\_\_.__/ \___/ \___/|_|\_\   \_/\_/   |_|_| |_| .__/ \__,_|\__|
                                                                                                      |_|
    */

    %utlfkil(d:/xls/pyregout.xlsx);

    %utl_pybeginx;
    parmcards4;
    import openpyxl
    import pandas as pd
    exec(open('c:/oto/fn_python.py').read())
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    have.to_excel('d:/xls/pyregout.xlsx', sheet_name='have', index=False)
    ;;;;
    %utl_pyendx;

    %utl_pybeginx;
    parmcards4;
    import io
    import numpy as np;
    import pandas as pd
    from openpyxl import load_workbook
    import statsmodels.api as sm
    from statsmodels.formula.api import ols
    exec(open('c:/oto/fn_python.py').read());
    xlspth = 'd:/xls/pyregout.xlsx'
    have = pd.read_excel(xlspth, sheet_name='have')
    print(have)
    havxpo=pdsql('''
     select max(case when column="WEIGHT" then tym1 else null end)  as weight
     ,max(case when column="HEIGHT" then tym1 else null end)  as height from have union all
     select max(case when column="WEIGHT" then tym2 else null end)  as weight
     ,max(case when column="HEIGHT" then tym2 else null end)  as height from have union all
     select max(case when column="WEIGHT" then tym3 else null end)  as weight
     ,max(case when column="HEIGHT" then tym3 else null end)  as height from have union all
     select max(case when column="WEIGHT" then tym4 else null end)  as weight
     ,max(case when column="HEIGHT" then tym4 else null end)  as height from have union all
     select max(case when column="WEIGHT" then tym5 else null end)  as weight
     ,max(case when column="HEIGHT" then tym5 else null end)  as height from have
    ''')
    print(havxpo)
    model = ols('height ~ weight', data=havxpo).fit()
    sumry=model.summary().as_text()
    summary_df = pd.read_csv(io.StringIO(sumry), delimiter="\t")
    print(model.params)
    coef=model.params
    anova = sm.stats.anova_lm(model, typ=2)
    print(anova)
    predictions = model.predict()
    residuals = model.resid
    result = pd.DataFrame({
        'height': havxpo['height'],
        'weight': havxpo['weight'],
        'Predicted': predictions,
        'Residuals': residuals
    })
    print(result)
    anova = anova.reset_index()
    coef = coef.to_frame()
    coef = coef.reset_index()
    with pd.ExcelWriter(xlspth) as writer:
      have.to_excel(writer, sheet_name='havraw', index=False)
      havxpo.to_excel(writer, sheet_name='havxpo', index=False)
      result.to_excel(writer, sheet_name='result', index=False)
      anova.to_excel (writer, sheet_name='anova',  index=False)
      coef.to_excel (writer, sheet_name='coef',  index=False)
      summary_df.to_excel(writer, sheet_name='summary_df', index=False)
    fn_tosas9x(havxpo,outlib='d:/sd1/',outdsn='havxpo',timeest=3);
    fn_tosas9x(result,outlib='d:/sd1/',outdsn='result',timeest=3);
    fn_tosas9x(coef ,outlib='d:/sd1/',outdsn='coef',timeest=3);
    fn_tosas9x(summary_df ,outlib='d:/sd1/',outdsn='summary_df',timeest=3);
    fn_tosas9x(result ,outlib='d:/sd1/',outdsn='result',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.havxpo;run;quit;
    proc print data=sd1.result;run;quit;
    proc print data=sd1.anova;run;quit;
    proc print data=sd1.coef;run;quit;
    proc print data=sd1.summary_df;run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* d:/xls/regout.xlsx                                                                                                     */
    /*                                                                                                                        */
    /* SHEET HAVXPO (PIVOT)            PREDICTED SHEET                       ANOVA                    COEFFICIENTS SHEET      */
    /*                                                                                                                        */
    /* --------------------      -----------------+               ---------------------              ----------------------+  */
    /* | A1| fx    |HEIGHT|     |   A1|fx     |HEIGHT   |         | A1| fx    | CALL  |              | A1| fx      |PARAMTR|  */
    /* --------------------     --------------------------------  ---------------------------------  -----------------------  */
    /* [_] |    A  |    B |[ [_]|   A  | B    |    C    |    E |  [_]|   A   |  B   |C |  D |   E |  [_] |    A    |    B  |  */
    /* ---------------------  ----------------------------------  ---------------------------------  -----------------------  */
    /*  1  | HEIGHT| WEIGH|   1 |HEIGHT|WEIGHT|PREDICTED| RESID|   1 | INDEX |SUM_SQ|DF|  F | PR_F|   1  | INDEX   |  V0E  |  */
    /*  -- |-------+------+   - |------+------+---------+------+  -- +-------+------+--+----+-----+   -- |---------+-------+  */
    /*  2  |69     | 98.1 |   2 |98.1  |69    | 66.6918 | 2.308|   2 | weight| 40.28|1 |2.83|0.191|   2  |INTERCEPT| 27.93 |  */
    /*  -- |-------+------+   - |------+------+---------+------+  -- +-------+------+--+----+-----+   -  |---------+-------+  */
    /*  3  |56.5   | 84   |   3 |84    |56.5  | 61.1202 | -4.62|   3 | height| 42.67|3 |    |     |   3  |SLOPE    | 0.394 |  */
    /*  -- |-------+------+   - |------+------+---------+------+  ---------------------------------   -- |---------+-------+  */
    /*  4  |65.3   | 98   |   4 |98    |65.3  | 66.6523 | -1.35|                                      [COEF]                  */
    /*  -- |-------+------+   - |------+------+---------+------+                                                              */
    /*  5  |62.8   | 88.5 |   5 |88.5  |62.8  | 62.8984 | -0.09|                                                              */
    /*  -- |-------+------+   - |------+------+---------+------+                                                              */
    /*  6  |63.5   | 80.5 |   6 |80.5  |63.5  | 59.7372 | 3.762|                                                              */
    /*  -- |-------+------+   -- |------+------+---------+-----+                                                              */
    /*                        [PREDICTED]                                                                                     */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                     SHEET SUMMARY                                                                      */
    /*                                                                                                                        */
    /*   ---------------------                                                                                                */
    /*   | A1| fx    model   |                                                                                                */
    /*   ---------------------------------------------------------------------------------------                              */
    /*   [_] |                   A                                                             |                              */
    /*    --------------------------------------------------------------------------------------                              */
    /*    1 |  Model:                            OLS   Adj. R-squared:                  0.314  |                              */
    /*   -- +                                                                                  +                              */
    /*    2 |  Method:                 Least Squares   F-statistic:                     2.832  |                              */
    /*   -- +                                                                                  +                              */
    /*    3 |  Date:                Wed, 29 Jan 2025   Prob (F-statistic):              0.191  |                              */
    /*   -- +                                                                                  +                              */
    /*    4 |  Time:                        15:12:35   Log-Likelihood:                -12.455  |                              */
    /*   -- +                                                                                  +                              */
    /*    5 |  No. Observations:                   5   AIC:                             28.91  |                              */
    /*   -- +                                                                                  +                              */
    /*    6 |  Df Residuals:                       3   BIC:                             28.13  |                              */
    /*   -- +                                                                                  +                              */
    /*    7 |  Df Model:                           1                                           |                              */
    /*   -- -                                                                                  -                              */
    /*    8 |  Covariance Type:            nonrobust                                           |                              */
    /*   -- +                                                                                  +                              */
    /*    9 |                   coef    std err          t      P>|t|      [0.025      0.975]  |                              */
    /*   -- +                                                                                  +                              */
    /*   10 |--------------------------------------------------------------------------------- |                              */
    /*   -- +                                                                                  +                              */
    /*   11 |  Intercept     27.9277     21.159      1.320      0.279     -39.410      95.266  |                              */
    /*   -- +                                                                                  +                              */
    /*   12 |  weight         0.3951      0.235      1.683      0.191      -0.352       1.142  |                              */
    /*   -- |                                                                                  |                              */
    /*   13 +  Omnibus:                          nan   Durbin-Watson:                   1.761  +                              */
    /*   -- |                                                                                  |                              */
    /*   14 +  Prob(Omnibus):                    nan   Jarque-Bera (JB):                0.325  +                              */
    /*   -- |                                                                                  |                              */
    /*   15 +  Skew:                          -0.285   Prob(JB):                        0.850  +                              */
    /*   -- |                                                                                  |                              */
    /*   16 +  Kurtosis:                       1.889   Cond. No.                     1.13e+03  +                              */
    /*   -- |                                                                                  |                              */
    /*   17 +  Notes:                                                                          +                              */
    /*   -- |                                                                                  |                              */
    /*   18 + [1] Standard Errors assume that the covariance matrix of the errors is specified +                              */
    /*   -- |                                                                                  |                              */
    /*   19 - [2] The condition number is large, 1.13e+03. This might indicate that there are  +                              */
    /*   -- |                                                                                  |                              */                                                                                              */
    /*    --------------------------------------------------------------------------------------                              */                                                                                              */
    /*  [SUMMARY]                                                                                                             */                                                                                              */
    /*                                                                                                                        */                                                                                            */
    /*                                                                                                                        */                                                                                            */
    /*  SAS DATASETS                                                                                                          */                                                                                            */
    /*                                                                                                                        */
    /*  Obs    WEIGHT    HEIGHT                                                                                               */
    /*                                                                                                                        */
    /*   1      98.1      69.0                                                                                                */
    /*   2      84.0      56.5                                                                                                */
    /*   3      98.0      65.3                                                                                                */
    /*   4      88.5      62.8                                                                                                */
    /*   5      80.5      63.5                                                                                                */
    /*                                                                                                                        */
    /* Obs    INDEX           V0                                                                                              */
    /*                                                                                                                        */
    /*  1     Intercept    27.9277                                                                                            */
    /*  2     weight        0.3951                                                                                            */
    /*                                                                                                                        */
    /* Obs     INDEX       SUM_SQ    DF       F        PR__F_                                                                 */
    /*                                                                                                                        */
    / * 1     weight      40.2768     1    2.83166    0.19101                                                                 */
    /*  2     Residual    42.6712     3     .          .                                                                      */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  Obs                                 V____________________________OLS                                                  */
    /*                                                                    */                                                  */
    /*    1    ==============================================================================                                 */
    /*    2    Dep. Variable:                 height   R-squared:                       0.486                                 */
    /*    3    Model:                            OLS   Adj. R-squared:                  0.314                                 */
    /*    4    Method:                 Least Squares   F-statistic:                     2.832                                 */
    /*    5    Date:                Wed, 29 Jan 2025   Prob (F-statistic):              0.191                                 */
    /*    6    Time:                        15:51:00   Log-Likelihood:                -12.455                                 */
    /*    7    No. Observations:                   5   AIC:                             28.91                                 */
    /*    8    Df Residuals:                       3   BIC:                             28.13                                 */
    /*    9    Df Model:                           1                                                                          */
    /*   10    Covariance Type:            nonrobust                                                                          */
    /*   11    ==============================================================================                                 */
    /*   12                     coef    std err          t      P>|t|      [0.025      0.975]                                 */
    /*   13    ------------------------------------------------------------------------------                                 */
    /*   14    Intercept     27.9277     21.159      1.320      0.279     -39.410      95.266                                 */
    /*   15    weight         0.3951      0.235      1.683      0.191      -0.352       1.142                                 */
    /*   16    ==============================================================================                                 */
    /*   17    Omnibus:                          nan   Durbin-Watson:                   1.761                                 */
    /*   18    Prob(Omnibus):                    nan   Jarque-Bera (JB):                0.325                                 */
    /*   19    Skew:                          -0.285   Prob(JB):                        0.850                                 */
    /*   20    Kurtosis:                       1.889   Cond. No.                     1.13e+03                                 */
    /*   21    ==============================================================================                                 */
    /*   22    Notes:                                                                                                         */
    /*   23    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.                    */
    /*   24    [2] The condition number is large, 1.13e+03. This might indicate that there are                                */
    /*   25    strong multicollinearity or other numerical problems.                                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___            _       _           _
    | ___|  _ __ ___| | __ _| |_ ___  __| |  _ __ ___ _ __   ___  ___
    |___ \ | `__/ _ \ |/ _` | __/ _ \/ _` | | `__/ _ \ `_ \ / _ \/ __|
     ___) || | |  __/ | (_| | ||  __/ (_| | | | |  __/ |_) | (_) \__ \
    |____/ |_|  \___|_|\__,_|\__\___|\__,_| |_|  \___| .__/ \___/|___/
                                                     |_|
    */

    https://github.com/rogerjdeangelis/utl-betas-for-rolling-regressions
    https://github.com/rogerjdeangelis/utl-calculate-regression-coeficients-in-base-sas-fcmp-proc-reg-r-and-python
    https://github.com/rogerjdeangelis/utl-calculate-the-regression-slope-for-each-patient-by-treatment
    https://github.com/rogerjdeangelis/utl-challenges-associated-with-hilbert-matrices-in-regression-design
    https://github.com/rogerjdeangelis/utl-drop-down-to-python-for-a-regression-sas-python-interface
    https://github.com/rogerjdeangelis/utl-find-area-under-curve-and-compute-regression-slope-and-intercept-using-sqllite-r-python
    https://github.com/rogerjdeangelis/utl-generate-all-possible-paiwise-interactions-products-regression
    https://github.com/rogerjdeangelis/utl-linear-regression-in-python-R-and-sas
    https://github.com/rogerjdeangelis/utl-locating-breakpoints-for-dogleg-mutiple-regression-lines
    https://github.com/rogerjdeangelis/utl-maximum-liklihood-regresssion-wps-python-sympy
    https://github.com/rogerjdeangelis/utl-outlier-analysis-based-on-robust-regression
    https://github.com/rogerjdeangelis/utl-piecewise-regression-find-the-breakpoint
    https://github.com/rogerjdeangelis/utl-plotting-a-regression-line-over-the-conditional-distributions-of-y-for-each-value-of-x-r-sas
    https://github.com/rogerjdeangelis/utl-random-forest-regression-vs-linear-regression-with-uncorrelated-independent-variables-in-r
    https://github.com/rogerjdeangelis/utl-regression-line-plus-and-minus-the-interquartile-range-of-dependent-variable
    https://github.com/rogerjdeangelis/utl-regression-on-correlated-and-uncorrelated-independent-variables-using-sas-r-principle-components
    https://github.com/rogerjdeangelis/utl-scatter-plot-with-regression-line-coefficients-and-pvalue-in-one-datastep-sgplot
    https://github.com/rogerjdeangelis/utl-simple-example-of-meta-regression-using-SAS-and-R
    https://github.com/rogerjdeangelis/utl-using-linear-regression-with-base-sas-and-r-to-interpolate-missimg-values
    https://github.com/rogerjdeangelis/utl-using-the-regression-equation-to-score-a-table-like_a_weighted-sum
    https://github.com/rogerjdeangelis/utl-visualizing-regression-differences-when-regressing-y-vs-x-and-x-vs-y-using-sas-r-ggplot
    https://github.com/rogerjdeangelis/utl_dosubl_do_regressions_when_data_is_between_dates
    https://github.com/rogerjdeangelis/utl_excluding_rolling_regressions_with_one_on_more_missing_values_in_the_window
    https://github.com/rogerjdeangelis/utl_how_to_automate_a_series_of_logistic_regressions
    https://github.com/rogerjdeangelis/utl_multiple-regressions-using-arrays

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
