# Informix-4GL

## 特性
- uses non-procedural and procedural statements
- is compatible with UNIX, MS-DOS, VMS systems
- works with Informix-SQL
- is case-insensitive
- creates report
- uses RDSQL to manipulate a database
- appies pound sign \# and curly braces \{\} to make comments
- scope: within (1) global/program, (2) module/4gl file, (3) local/block
    | 元素 \ 作用域 | Program | Module | Function |
    | -- | -- | -- | -- |
    | 變數(variable) | GLOBAL | | LOCAL |
    | 辨識子(identifier) | FORMS, WINDOWS | CURSORS, PREPARED STATEMENTS | | 
    | 指令(command) | DEFER | WHENEVER | |

## 4GL指令
1. Program Defination Statements
    - [MAIN](https://4js.com/online_documentation/fjs-fgl-2.50.02-manual-html/index.html#c_fgl_programs_MAIN.html)
    - [FUNCTION](https://4js.com/online_documentation/fjs-fgl-2.50.02-manual-html/index.html#c_fgl_Functions_syntax.html)
    - [REPORT](https://4js.com/online_documentation/fjs-fgl-2.50.02-manual-html/index.html#c_fgl_reports_Report_Definition.html)
2. Variable Definition Statements
    - DEFINE var_name data-type
        - data-type引用DB coulun需先宣告 DATABASE erp。重新編譯(recompile)可取得最新DB coulun。
        - var_name長度1-18碼，若要專指它不是變數，可用@DB, @tbl, @col表示。
        - variable用於變數。identifier用於表格(程式內)、視窗(程式內)、cursor (模組內)、prepared statement (模組內)。
    - GLOBALS
        - 必須放在MAIN之前，需要放在DATABSE之後。
        - 可以引用4gl檔案。
3. Program Flow Statements
    - CALL
    - WHILE
    - FOR
    - CONTINUE
    - EXIT
    - CASE
    - IF
    - RETURN
    - RUN
    - SLEEP
4. Error Handling Statements
    - DEFER INTERRUPT
        - 當使用者按下中斷鍵(DEL, CONTROL-C, CONTROL-Q, ESC)，全域變數INT_FLAG變成TRUE。INT_FLAG變成TURE應還原成FALSE利於下次的判斷。
        - 避免程式臨時中斷的替代方式，是用ON KEY指定鍵盤行為。
    - WHENEVER ERROR CALL errmsg ... WHENEVER ERROR STOP
        - 搭配EXIT PROGRAM
    - WHENEVER ERROR CONTINUE ... WHENEVER ERROR STOP
5. Screen Interface Statements
    - OPEN FORM f_name FROM "file_form"
    - DISPLAY FORM f_name
    - DISPLAY ARRAY
        - 搭配在畫面檔的 SCREEN RECORD scr_record\[num\]\(var2 THRU var5\)
    - OPEN WINDOW w_name
    - CURRENT WINDOW IS w_name
    - PROMPT
    - INPUT BY NAME var_name.*
        - 當程式變數與螢幕欄位全為相同時，無須一個一個用THRU指定
        - 跳離時機：1.任意時刻按下Esc，2.最末欄位按下Enter
        - OPTIONS INPUT NO WRAP可回到第一欄，避免最末欄按下Enter即被送出。
        - ON KEY key包括F1~F36, CONTROL-A/D/H/J/L/M/Q/R/S/X, ESCAPE, DEL
        - INPUT BY NAME var_name.* WITHOUT DEFAULTS
    - INPUT ARRAY
        - 以單列資料為一個element。
        - INPUT ARRAY prg_arr_name FROM scr_arr_name.*
    - CONSTRUCT BY NAME ls_sql_where ON subno, alias
    - CONSTRUCT BY NAME ls_sql_where ON seccom.*
    - CONSTRACT ls_sql_where ON col1, col2, col3 FROM field1, field2, field3
    - CLEAR SCREEN
    - CLEAR FORM
    - CLEAR scr_var.*
    - CLEAR WINDOW w_name
    - CLOSE FORM f_name
    - CLOSE WINDOW w_name
    - DISPLAY BY NAME var_name.*
    - DISPLAY var_name1, var_name2 TO field1, field2
    - ERROR
    - MASSAGE
    - MENU
    - OPTIONS
6. Variable Assignment Statements
    - LET var_name = 0
    - INITAILIZE var_name.* TO NULL
    - 計算式(expression)若有任一變數是MULL，結果必為NULL。
7. Report Statements
    - 寫入pipe (前端呈現，對應 /u1/mis/bin/cpri)
        - START REPORT rep_func TO PIPE "cpri"
        - OUTPUT TO REPORT rep_func(params.*) 搭配 FOREACH、WHILE loop
        - FINISH REPORT rep_func
    - 寫入檔案 (資料備份/整合)
        - START REPORT rep_func TO "file_path"
        - OUTPUT TO REPORT rep_func(params.*) 搭配 FOREACH、WHILE loop
        - FINISH REPORT rep_func
8. Cursor Manipulation Statements & Dynamic Statements
    > - Pattern 1 - 查詢單筆、執行異動: PREPARE-EXECUTE
    > - Pattern 2 - 查詢多筆: PREPARE-DECLARE-OPEN-FETCH-CLOSE/FREE
    > - Pattern 3 - 查詢多筆: PREPARE-DECLARE-FOREACH

    - PREPARE stm_name FROM ls_sql
    - EXECUTE stm_name USING lc_var INTO lr_var.*
    - DECLARE cur_name CURSOR FOR stm_name 
    - DECLARE cur_name SCROLL CURSOR FOR stm_name
        - 限用於SELECT，不可與UPDATE、DELETE併用
        - FETCH出現時，必須用這個才能捲動資料。每次捲動只把1列資料丟入temp table的buffer。
    - OPEN cur_name USING lc_var
        - invoke statement associated with the cursor
    - FETCH cur_name INTO lr_var.*
        - bump the cursor to the next row of active set
        - 外面通常包著WHILE判斷，以及PREPARE-DECLARE-OPEN-FETCH-CLOSE/FREE搭配使用
    - CLOSE cur_name      
    - FOREACH cur_name USING lc_var INTO lr_var.*
        - cause a sequence of statement

9. RDSQL Statements: DDL, DML, DCL
    - DATABASE erp
    - CREATE TABLE erp:tbl
    - CREATE TEMP TABLE erp:tbl
    - DROP TABLE erp:tbl
    - CREATE VIEW
    - DROP VIEW erp:tbl
    - CREATE INDEX ix_tbl_01 ON tbl(pk1, pk2)
    - CREATE UNIQUE INDEX ix_tbl_02 ON tbl(pk1, pk2)
    - INSERT INTO erp:tbl 
    - DELETE FROM erp:tbl WHERE col='xxx'
    - UPDATE erp:tbl SET col='xxx'
    - SELECT * FROM erp:tbl WHERE col='xxx'
    - GRANT RESOURCE TO user1, user2
    - REVOKE RESOURCE FROM user1, user2
    - 每當前後端溝通時，後端DB會回傳前端4GL程式SQLCA資訊
        - SQLCA.SALCODE = 0 成功，< 0 不成功，= 100 或 = NOTFOUND 查無此行。
        - 每當 SQL 執行後，這個值 SQLCA.SALCODE 會同步更新給 STATUS。
        - SQLCA.SQLERRD[6] 取得新增列的 ROWID。
        - SQLCA.SQLERRD[3] 取得已處理的筆數。

## UNIX指令

1. 開啟dbschema互動介面
    - `dbschema -t acttxr -d tst schema_acttxr.sql`
2. 編譯腳本-畫面檔
    - `form4gl inv925` 用於4GL環境，inv925.per -> inv925.frm
    - `fglform inv925` 用於Genero環境，inv925.per -> inv925.42f
    - `r.f2 cinvp001` 用於tiptop環境，cinvp001.per -> cinvp001.frm
3. 編譯腳本-原始碼
    - `fglpc inv925r` 用於4GL環境，inv925r.4gl -> inv925.4go
    - `fglcomp inv925r` 用於Genero環境，inv925r.4gl -> inv925.42m
    - `r.c2 cinvp001` 用於tiptop環境，cinvp001.4gl -> cinvp001.4go
    - Any descrepacies of fields length will be noted in the filename.err.
4. 結合所有檔案(link modules together to create an executable program)
    - `cat inv925r.o invlib.4go abc.4go >inv925.4gi` 用於4GL環境 
    - `fgllink -o inv925.42r inv925r.42m invlib.42m abc.42m` 用於Genero環境
5. 執行檔案
    - `fglgo inv925` 用於4GL環境
    - `fglrun inv925` 用於Genero環境
    - `r.r2 cinvp001` 用於tiptop環境

## FORM
1. Screen Display
    - Line 1: Prompts
    - Line 2: Messages
    - Line 3-22: Screen Form
    - Line 23: Comments
    - Line 24: Errors

## Array
1. 編輯模式下的 OPTIONS 預設鍵
    - ESC for 跳脫 INPUT ARRAY
    - F1 for 新增一列
    - F2 for 刪除一列
    - F3 for 滾動到下一頁
    - F4 for 滾動道上一頁
2. 原生函式
    - SCR_LINE() 是指標在螢幕陣列上的列號
    - ARR_CURR() 是指標在程式陣列上的列號
    - ARR_COUNT() 是程式陣列的列總數
    - SET_COUNT(li_variable) (必出現在 DISPLAY ARRAY or INPUT ARRAY 之前)