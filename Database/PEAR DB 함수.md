# pear DB 함수
~~~php
    DB::query( $query ) // 해당 DB로 쿼리를 전송한다.

    DB::isError() // 어떤 에러인가를 확인한다.

    DB::getMessage() // 에러메세지를 리턴한다.

    DB::numRows(); // 결과수를 리턴한다. 

    // ex )
    $res = DB::query( $query ); 
    $count = $res->numRows();

    DB::getOne( $query ) // 해당 쿼리로 부터 최초의 행의 최초의 컬럼값을 가져온다.

    DB::getRow($query, array(), DB_FETCHMODE_ASSOC) // 쿼리로부터 최초의 행만 가져온다.        

    DB_Result::fetchRow(DB_FETCHMODE_ASSOC) // mysql_fetch_array() 함수처럼 쓸수 있다.

    // ex )
    $res = DB::query( $query );
    while ($row = $res->fetchRow(DB_FETCHMODE_ORDERED)) {
        $id = $row[0]; // 첨자 배열로 리턴하게 됩니다.
    }
        
    DB::disconnect() // 접속 종료
~~~

## PEAR
[PEAR 공식 사이트](https://pear.php.net/)
