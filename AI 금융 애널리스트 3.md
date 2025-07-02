###### 12. Require specific output format

- AI Agent 노드 클릭 > Require Specific Output Format 활성화
- AI Agent > Parser '+' > Structured Output Parser
- JSON Example 항목에 아래 코드 삽입
  
⬇⬇⬇

{
  "ticker": "NFLX",
  "subject": "넷플릭스 광고 매출 분기별 성장세 분석 - 2025년 5월",
  "text": "# 넷플릭스 광고 매출 분기별 성장세(2023~2025)\n\n## 📈 성장 추이 요약\n- 넷플릭스의 광고 기반 요금제는 2022년 도입 이후 빠른 속도로 성장 중입니다.\n- 2023년 2분기 가입자 약 1,500만 명 → 2024년 5월 4,000만 명 → 2024년 11월 7,000만 명 → 2025년 5월 9,400만 명을 돌파하며, 1년 새 2배 이상 성장했습니다.\n- 광고 매출 자체는 세부 숫자가 공식 공개되진 않았지만, 광고 플랜 도입 후 매 분기마다 두자릿수 성장을 지속 중인 것으로 분석됩니다.\n- 2025년 1분기 전체 매출은 13% 증가했고, 이 중 광고 매출이 두드러진 기여 요인이라는 점이 실적 발표 및 업계 리서치에서 반복적으로 언급되고 있습니다.\n- 넷플릭스는 현재 다양한 신형 광고 포맷(인터랙티브·일시정지 광고 등) 추가 도입으로, 추가 성장 동력을 확보할 전망입니다.\n\n## 📝 결론\n\n넷플릭스 광고 매출은 가입자 수 폭증과 함께 2023~2025년 동안 분기마다 두자릿수 성장률을 기록 중입니다. 공식 분기별 금액 수치는 미공개지만, 광고 부문이 최근 실적 개선의 핵심 동력임은 명확합니다. 향후 광고 상품 다양화와 글로벌 추가 확산에 따라 성장세가 더 가속화될 가능성이 높습니다."
}

- Execute workflow 클릭하여 워크플로우 활성화
- 텔레그램 채팅창에서 테스트 질문 입력 "테슬라의 최근 영업이익을 분석해줘."
- AI Agent 노드를 클릭하여 output에서 답변 확인

___
###### 13. Telegram > Send a text message

 - AI Agent 내용을 텔레그램 채팅창으로 전송
 - Credential to connect with : 기존 Credential 자동 연결
 - Chat ID 항목은 이전 INPUT > Switch > chat > ID 값을 드래그 : {{ $('Switch').item.json.message.chat.id }}
 - Text 항목은 이전 INPUT > AI Agent > output > text 값을 드래그 : {{ $json.output.text }}
 - Additional Fields > Add Field > Additional Fields > Append n8n Attribution 비활성화
 - Execute workflow 클릭 > 텔레그램 채팅창 입력 > 텔레그램 채팅창 답변 확인

___
###### 14. Markdown 변환

- Telegram > Send a text message '+' > Markdown 검색 및 선택
- Mode > Markdown to HTML 선택
- Markdown 항목은 이전 INPUt > Telegram Send a text message > chat > text 항목 드래그
- Execute step 클릭하여 노드 연결 활성화 테스트

___

###### 15. Gmail 추가

- Markdown '+' > Gmail > Send a message 

- Gmail Credential 등록 방법
	- https://cloud.google.com 이동
	- 구글 클라우드 > 콘솔 접속 > 프로텍트 클릭 > 새 프로젝트
	- 프로젝트 이름 > 'n8n project' > 프로젝트 생성이 완료되면 선택
	- 검색창에서 Gmail API 검색 후 선택 > 사용 클릭
	- 왼쪽 메뉴 > API 및 서비스 > OAuth 동의 화면
		- 앱 정보 : 앱 이름 입력 후 사용자 지원 입력(본인 이메일) > 다음
		- 대상 : 외부 > 다음
		- 연락처 정보 : 본인 이메일 입력 > 다음
		- 완료 > 계속 > 만들기 클릭
		- 데이터 엑세스 > 범위 추가 또는 삭제 > 선택한 범위 지정 > 업데이트 클릭 > Save

	- 왼쪽 메뉴 > API 및 서비스 > 사용자 인증 정보 > 사용자 인증 정보 만들기 > OAuth Clinet ID
	- 애플리케이션 유형 : 웹 애플리케이션
	- 이름 : 자유 형식
	- 승인된 리디렉션 URL > URL 추가 : n8n Gmail 노드 추가 시 나오는 OAuth Redirect URL
	- 만들기 클릭 > 클라이언트 ID 및 비밀번호를 확인 후 > n8n Client ID 및 Secret에 입력
	- Sign in with Google에서 오류가 날 경우 OAuth 동의 화면 > 대상으로 이동하여 테스트 사용자에 본인 이메일 등록
	- 다시 Sign in with Google 클릭하여 연동 완료

- To > 수신자 이메일 등록
- Subject > 이전 INPUT > AI Agent > output > subject 드래그 : {{ $('AI Agent').item.json.output.subject }}
- Message > 이전 INPUT > Markdown > result > data 드래그 : {{ $json.data }}
- Options > Add Option > Append n8n Attribution > 비활성화
- Execute step 클릭하여 노드 연결 활성화 테스트
