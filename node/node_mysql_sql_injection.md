# node mysql escaping query value vs mysql prepared statement  
**mysql escaping query**  

    connection.query('SELECT * FROM users WHERE id = ?', [userId], function (error, results, fields) {
      if (error) throw error;
      // ...
    });

**mysql prepared statement**  

    "INSERT INTO MyGuests (firstname, lastname, email) VALUES (?, ?, ?)"
    
 언뜻보면 둘이 같아보이는데 엄연히 다르다. mysql이 제공하는 prepared statement는 statement를 사용할 때 쿼리를 수행할 때 마다  
 1. 쿼리 문장 분석  
 2. 컴파일  
 3. 실행  
 을 하고 prepared statement는 처음 한 번만 위의 단계를 거친 후 캐시에 담아 재사용 하는 것이다.  
 (물론 sql injection을 회피할 수도 있다.  
 If the original statement template is not derived from external input, SQL injection cannot occur.  
 원래 명령문이 외부에서 입력되는 값이 아니라면? )  
 
 그러나 node mysql module이 제공하는 **'?'** 는 escape(), escapeId() method와 같은 역할이다.  
 node-mysql이 친절히  
 **Caution : This also differs from prepared statements in that all ? are replaced, even those contained in comments and strings.**  
 '모듈에서의 '?'는 prepared statement와 다르다. '?'는 string, comments도 모두 escape해주기 때문이다.'라고 설명하고 있다.  
 
 
 ## 결론적으로 node module에서의 ?와 mysql prepared statement는 다르다!


### 참고  
**node-mysql sql injection prevention**  
https://github.com/mysqljs/mysql#escaping-query-values  
https://github.com/mysqljs/sqlstring/blob/master/lib/SqlString.js 
**mysql prepared statement**  
https://www.w3schools.com/php/php_mysql_prepared_statements.asp  
https://devbox.tistory.com/entry/Comporison


 
