# 문자열의 중복을 제거하며 추가하기(c사 신입 공채 sw) - javscript  
기존의 문자열 배열이 제공된다.  
>original = {"hello", "world"};

새로운 문자열 배열을 입력받는다.  
>additional = {"aa", "hello", "bbbb", "aa", "world"};  

새로 입력받은 문자열 배열 원소 중 original의 원소, 이미 저장된 additional의 원소를 제외하고 새로운 배열 answer에 저장한다.  
>answer = {"aa", "bbbb"};

answer 출력하는 문제

```javascript
//original은 기존의 배열(테스트 케이스는 본인이 작성할 것)
//additional은 새로 입력받은 배열(테스트 케이스는 본인이 작성할 것)
function solution(original, additional){
	var answer= [];//결과를 출력할 배열
	var check = 0;//문자열 중복을 체크하는 변수, check=0일 때만 answer에 원소 추가
  
	for(var i = 0; i<additional.length; i++){
		check = 0;
		for(var j = 0; j<original.length; j++){
      //original의 원소와 중복된다면 check 값을 증가시킨 후 for문 탈출
			if(additional[i] == original[j]){
				check++;
        break;
			}
		}
		for(var j = 0; j<i;j++){
      //additional의 이전 원소와 중복된다면 check 값을 증가시킨 후 for문 탈출
			if(additional[i]==additional[j]){
				check++;
        break;
			}
		}
		if(check == 0){
			answer.push(additional[i]);	
		}
	}
	return answer;
}

```
