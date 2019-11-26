# UpCounter
3. HW1_3
① 개요
7-SEGMENT 두 자리에 00 - 99 까지 표시하는 업카운터(UP-COUNTER)를 만든다. 증가 표시 주기는 적절하게 설정하고, P2.0에 연결된 스위치를 누르면 누르면 00부터 다시 증가하도록 초기화 한다.


회로도에서 볼 수 있듯이 LED가 출력될 DISP를 결정하는 비트는 P3.4와 P3.3임을 알 수 있다. 이를 이용해 원하는 위치에 LED를 출력한다. 

② 소스 코드

	ORG 	0			;PC = 0H부터 시작
	MOV	DPTR, #300H		;DPTR은 300H를 가리킴
DF:	MOV	R1, #0		;R1 초기화(십의 자리)
	MOV	R0, #0		;R2 초기화(일의 자리)
		
LP:	MOV	R5, #100		;한 숫자를 100번 정도 출력한다(잔상효과를 위함)
LP1:	MOV	A, R1			;십의 자리 숫자 값을 읽어온다
	MOVC	A, @A + DPTR	;LOOK UP TABLE을 참조해 LED값을 가져온다
	JNB	P2.0, DF		;출력하기 전에 스위치 값을 확인한 후 스위치를 누르면 처음부터 시작한다
	MOV	P1, A			;LED값을 출력한다
	CALL	DELAY			;약간의 지연시간을 가진 후
	MOV	P1, #0FFH		;LED를 껐다가(그 전의 숫자가 남아 있는 것을 방지하기 위해서)
	CLR	P3.3			;DISP3에 출력한다		
		
	MOV	A, R0			;일의 자리 숫자 값을 읽어온다
	MOVC	A, @A + DPTR	;LOOK UP TABLE을 참조해 LED값을 읽어온다
	JNB	P2.0, DF 		;출력하기 전에 스위치 값을 확인한 후 스위치를 누르면 처음부터 시작한다
	MOV	P1, A			;LED값을 출력한다	
	CALL	DELAY			;약간의 지연시간을 가진 후
	MOV	P1, #0FFH		;LED를 껐다가(이전과 같은 이유로)
	SETB	P3.3			;DISP2에 출력한다
	DJNZ	R5, LP1		;다시 돌아가서 같은 숫자를 반복해서 출력한다
	INC	R0			;한 숫자 출력이 끝나면 일의 자리 숫자를 증가한다
	CJNE	R0, #10, LP2		;출력하기 전에 일의 자리 숫자 값을 검사한다
	CJNE	R1, #9, LP3		;십의 자리 숫자 값도 검사한다
	MOV	P1,	#0FFH		;만약 십의 자리 숫자가 9라면 LED를 끄고		
FIN:	SJMP	FIN			;무한루프를 돈다

LP3:	MOV	R0, #0		;일의 자리 숫자가 10이라면 0으로 바꾸고
	INC	R1			;십의 자리 숫자를 증가 시키고
	LJMP	LP			;처음으로 돌아가 숫자를 출력한다

LP2:	LJMP	LP			;10이 아니라면 다시 돌아가서 숫자를 출력한다
	
DELAY:MOV	R2, #100		;약 5ms의 지연시간
AGAIN:MOV	R3, #11
HERE:	NOP
	NOP	
	DJNZ	R3, HERE
	DJNZ	R2, AGAIN
	RET

	ORG 300H
TABLE:				;LOOK UP TABLE
DB	192,249,164,176,153,146,130,216,128, 144

END


③ 동작 확인 및 분석
-프로그램 시작 : 00

숫자가 동시에 출력되는 것처럼 보이지 않는데 위에 시간을 보면 수 ms동안 작동하는 모습인 것을 알 수 있다. 따라서 실제 동작 시에는 엄청나게 빠른 속도로 깜빡이기 때문에 눈의 잔상 효과가 나타날 것이다.

-03

 


-99




-99이후


LED가 모두 꺼진 것을 알 수 있다.

-스위치를 눌렀을 때

먼저 스위치를 누르기 전,  LED가 01를 표시하고 있다.


스위치를 누른 상태일 때, LED가 꺼진다




스위치를 떼니, 00부터 다시 시작한다
