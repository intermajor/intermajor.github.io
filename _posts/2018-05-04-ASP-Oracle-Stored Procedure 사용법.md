---
layout: post
title:  "[ASP] 오라클 프로시저(Stored Procedure) 사용법"
date:   2018-05-03
desc: "desc"
keywords: "ASP"
categories: [Asp]
tags: [asp]
icon: icon-html
---

# 테스트 프로시저
~~~sql
	PROCEDURE TEST_PROC
	(     P_IDX IN NUMBER, 		//파라미터1
	P_NAME IN VARCHAR2,			//파라미터2
	OUT_CUR OUT SYS_REFCURSOR	//커서
	)
	IS
	BEGIN
	OPEN OUT_CUR FOR
	SELECT *
	  FROM TABLE_A
	WHERE IDX_SEQ = P_IDX
	   AND NAME=P_NAME;
	END TEST_PROC;
~~~


# test.asp 파일
~~~asp
	<!--METADATA TYPE= "typelib"  NAME= "ADODB Type Library" FILE="C:\Program Files\Common Files\SYSTEM\ADO\msado15.dll"  -->
	<%
	'위의 msado15.dll 참조코드 누락 시 에러 발생

	'DB연결 코드는 생략하겠습니다.(컨넥션명은 conn으로 설정)

	Dim seq, writerName
	seq = 3                        '프로시저에 들어갈 파라미터1
	writerName = "호박이"    '프로시저에 들어갈 파라미터12

	Set Cmd = Server.CreateObject("ADODB.Command")    'Command객체 선언

	WITH Cmd     'Cmd란 객체명 대신 .으로 대체
	 .ActiveConnection = c '커넥션개체 연결
	 .CommandType = adCmdStoredProc ' 타입설정
	 .CommandText = "TEST_PROC" '프로시저명

	  '프로시저 인수 설정(프로시저 인수명, 인수데이터타입, in/out 변수설정, 데이터크기, 값)
	 .Parameters.Append .CreateParameter("R_WRITER_NAME", adInteger, adParamInput, , seq)
	 .Parameters.Append .CreateParameter("R_PASSWORD", adVarChar, adParamInput, 10, writerName)

	Set rs = .Execute    '프로시저 실행 후 커서에 담긴 값을 rs라는 레코드셋에 저장
	END WITH
	Set Cmd = Nothing

	if rs.eof or rs.bof then    '불러온 값이 없을 경우
	response.write ""
	else
	response.write rs(0) & "<br>"
	response.write rs(1)
	end if

	rs.close()    '레코드셋 해제
	set rs = nothing

	conn.close()    '컨넥션 해제
	set conn = nothing
	%>
~~~

