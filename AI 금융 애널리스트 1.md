###### 1. Telegram > On message⚡

- '+' > Telegram > On Message 트리거 선택
- Create new credential > Access Token 입력

- 텔레그램 Mobile 앱 설치 - 계정 생성 - 로그인
- 텔레그램 Desktop 앱 설치 
	- https://desktop.telegram.org
	- 모바일 앱 접속 > 설정 > 기기 > 데스트탑 기기 연결 > QR코드 인증

- 텔레그램 BotFather 검색 및 실행
	- https://telegram.me/BotFather > START BOT 클릭
	- `/newbot` 명령어 입력 후 Bot 이름 설정 
		- 예시 : Financial_Adviser_n8n_Bot
	- BotFather 답변 중에서 Access Token 확인 후 n8n 입력

- 텔레그램 채팅창 실행
	- BotFather 답변 중에서 채팅창 URL 확인 후 START 클릭
	- n8n Telegram Trigger 노드 > Execute step 클릭 > OUTPUT 값 확인

___
###### 2. Switch

- '+' > Switch 검색 > Switch 선택
- Rule 1 > 채팅창에서 텍스트가 입력될 때 텍스트로 인식
	- 이전 노드 INPUT에서 text 필드를 value1로 드래그 : {{ $json.message.text }}
	- is equal to 값을  String > exists로 변경
	- Rename Output 활성화 > Output Name을 '텍스트'로 입력
	- Execute step 클릭하여 노드 연결 활성화 테스트

- Rule 2 > 채팅창에서 보이스가 입력될 때 보이스로 인식
	- 텔레그램 채팅창에서 보이스 입력
	- Telegram Trigger 노드 > Execute step 클릭 > OUTPUT 값 확인
	- Switch 노드 > Add Routing Rule > 새로운 Rule 추가
		- 이전 노드 INPUT에서 mime_type 필드를 value1로 드래그 : {{ $json.message.voice.mime_type }}
		-  is equal to 값을  String > contains로 변경
		- value2를 'audio'로 입력
		- Rename Output 활성화 > Output Name을 '보이스'로 입력
		- Execute step 클릭하여 노드 연결 활성화 테스트

___
###### 3. Telegram > Get a file

- 보이스의 경우 파일 다운로드 후 텍스트 변환 필요
- Switch 노드 보이스 '+' 클릭 > Telegram > Get a file 선택

- Credential to connect with : 기존 Credential 자동 연결
	- 이전 노드 INPUT에서 file_id 필드를 File_ID 항목에 드래그 : {{ $json.message.voice.file_id }
	- Execute step 클릭하여 노드 연결 활성화 테스트

___
###### 4.  OpenAI > Transcribe a recording

- 보이스의 경우 파일 다운로드 후 텍스트 변환 필요
- Telegram Get a file 노드 '+' 클릭 > OpenAI > Transcribe a recording

- Create new credential > API Key 입력
- Execute step 클릭하여 노드 연결 활성화 테스트

___
###### 5. AI Agent

- OpenAI > Transcribe a recording 노드 '+'클릭 > AI Agent 검색 후 선택

- 보이스에서 텍스트로 변환한 값을 AI Agent와 연결
	- Source for Prompt (User Message) > Define below 선택
	- 이전 노드 INPUT > Transcribe a recording > text 필드를 Prompt (User Message) 항목에 드래그 : {{ $json.text }}

 - 보이스 외 원래 텍스트 값을 AI Agent와 연결
	 - 보이스에서 텍스트로 변환한 값 {{ $json.text }} ≠ 기존 텍스트 값 {{ $json.message.text }}
		 - 텔레그램 채팅창 텍스트 입력 > Telegram Trigger 노드 > Execute step 클릭 > text 필드 확인
		 - 필드를 통일하기 위해 기존 Switch 노드 텍스트 '+' > Edit Fields 검색 후 선택
			 - Fields to Set 클릭 > name 항목 'text' 입력
			 - 이전 노드 INPUT text 필드를 value 항목에 드래그
			 - 이 작업을 통해 보이스에서 텍스트로 변환한 값 {{ $json.text }} = 기존 텍스트 값 {{ $json.text }}

		- Edit Fields > Execute step 클릭하여 노드 연결 활성화 테스트
		- Edit Fields '+' > AI Agent 노드와 연결
		- AI Agent 노드 > Execute step 클릭 > Prompt (User Message) 값이 텍스트로 들어왔는지 확인

- AI Agent > Chat Model '+" > OpenAI Chat Model
	- Credential to connect with : 기존 Credential 자동 연결
	- Model > gpt-4o
	- Add Option > Maximum Number of Tokens > 16384

- AI Agent > Memory '+' > Simple Memory
	- Session ID > Define below 선택
	- Key > 텔레그램 채팅 ID 필드 입력 필요
		- 채팅 ID 확인? AI Agent 노드 >  Execute step 클릭 > 이전 INPUT > Switch > chat > ID 값을 드래그
		- 기존 프로세스대로 따라오셨다면 {{ $('Switch').item.json.message.chat.id }} 입력 > Result ID 값 재확인
	- Context Window Length > 10
	- AI Agent 노드 > Execute step 클릭 > OpenAI Chat Model 및 Simple Memory 값 확인