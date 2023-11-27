현재 행에 대해 지정된 수만큼 이전 행의 값을 반환한다. 

* 기본형태
LAG( 이전 행에서 가져올 컬럼명 ) OVER ( PARTITION BY 파티셔닝의 기준컬럼 ORDER BY 파티셔닝한 결과를 정렬할때의 기준컬럼 )

> [!example]<mark style="background: #ABF7F7A6;">각 파티션의 첫번째 행에 대해서는 LAG(이전 행에서 가져올 컬럼명) 의 값이 항상 NULL 이 된다. 필요 없는 경우 WHERE 조건에서 LAG(컬럼) IS NOT NULL 처리 할 것!!</mark>







































