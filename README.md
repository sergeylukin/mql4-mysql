Overview
--------

Since MT4 build 600 was released, it's no longer easy to communicate with DLLs
that use ANSI format for strings because strings in MQL4 are now in UNICODE
format (read more [here](http://forum.mql4.com/60555)).

This library tries to solve that. While it has not been tested enough, it works
for simple database operations. Any feedback and contribution is highly
appreciated.

Supported 32 bit environment only.

How to use
----------

```c
#include <mql4-mysql.mqh>

string  host     = "localhost";
string  user     = "root";
string  pass     = "123456";
string  dbName   = "information_schema";

int     port     = 3306;
int     socket   = 0;
int     client   = 0;

int     dbConnectId = 0;
bool    goodConnect = false;
//+------------------------------------------------------------------+
//|                                                                  |
//+------------------------------------------------------------------+
int start () {
    goodConnect = init_MySQL(dbConnectId, host, user, pass, dbName, port, socket, client);
    
    if ( !goodConnect ) {
        return (1); // bad connect
    }

    //+-------------------------------------------------------------------
    //| Fetch multiple columns in multiple rows
    //+-------------------------------------------------------------------    
    string query = StringConcatenate(
        "SELECT `TABLE_NAME`, `TABLE_ROWS`, `CREATE_TIME`, `CHECK_TIME` ",
            "FROM `TABLES` ",
            "WHERE `TABLE_SCHEMA` = \'mysql\'");
    string data[][4];   // important: second dimension size must be equal to the number of columns
    int result = MySQL_FetchArray(dbConnectId, query, data);
    
    if ( result == 0 ) {
        // Print("0 rows selected");
    } else if ( result == -1 ) {
        // Print("some error occured");
    } else {
        // Print("Query was successful. Printing rows...");
        int num_rows = ArrayRange(data, 0);
        int num_fields = ArrayRange(data, 1);
        
        for ( int i = 0; i < num_rows; i++) {
            string line = "";
            
            for ( int j = 0; j < num_fields; j++ ) {
               string value = data[i][j];
                line = StringConcatenate(line, value, ";");
            }
            Print(line);
        }
    }
    
    //+-------------------------------------------------------------------
    //| Single row fetch
    //| Similar to array fetch
    //+-------------------------------------------------------------------
    string row[][4];   // important: second dimension size must be equal to the number of columns
    string rowQuery = StringConcatenate("SELECT `TABLE_NAME`, `TABLE_ROWS`, `CREATE_TIME`, `CHECK_TIME` ",
            "FROM `TABLES` ",
            "WHERE `TABLE_SCHEMA` = \'mysql\' ",
            "LIMIT 0,1");
    int row_result = MySQL_FetchArray(dbConnectId, rowQuery, row);
    line = "One row query: ";
    for(int r=0; r < ArrayRange(row, 1); r++) {
      line = StringConcatenate(line, row[0][r], ";");
    }
    Print(line);
    
    //+-------------------------------------------------------------------
    //| Sample INSERT
    //| It's just an example, nobody can insert into information_schema.
    //+-------------------------------------------------------------------
    /*string insertQuery  = StringConcatenate(
        "INSERT INTO `CHARACTER_SETS` VALUES (", "sometext"
                                        , "," , "oneMoreText"
                                        , "," , "andVarialbe"
                                        , "," , "nextWillBeNumber"
                                        , "," , 2
                                        , ")"
        );
    if ( MySQL_Query(dbConnectId, insertQuery) ) {
        Print("insert good");
    }*/
    
    
    //+-------------------------------------------------------------------
    deinit_MySQL(dbConnectId);
    return (0);
}
//+------------------------------------------------------------------+
```

Credits
-------

This library is based on [vedroid's modification](http://codebase.mql4.com/8122)
of [Russel's MySQL wrapper](http://codebase.mql4.com/5040) and
uses techniques taken from [EAX_Mysql library](http://www.mql5.com/en/code/855)
and from [snippets](http://forum.mql4.com/60608#903608)
[posted](http://forum.mql4.com/60708#905036) by
[gchrmt4](http://www.mql4.com/users/gchrmt4) in
[this thread](http://forum.mql4.com/60708).

License
-------

This is free and unencumbered software released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or
distribute this software, either in source code form or as a compiled
binary, for any purpose, commercial or non-commercial, and by any
means.

In jurisdictions that recognize copyright laws, the author or authors
of this software dedicate any and all copyright interest in the
software to the public domain. We make this dedication for the benefit
of the public at large and to the detriment of our heirs and
successors. We intend this dedication to be an overt act of
relinquishment in perpetuity of all present and future rights to this
software under copyright law.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

For more information, please refer to [<http://unlicense.org/>](http://unlicense.org)
