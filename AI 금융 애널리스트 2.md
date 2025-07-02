###### 6. AI Agent Tool : Internet Search

- AI Agent Tool에서 어떤 기능이 필요한지 미리 정리
	- [ ] Internet Search : 인터넷 검색
	- [ ] Get Price : 주가 1년치 파악
	- [ ] Get Income Statement : 손익계산서 크롤링
	- [ ] Get Balance Sheet : 대차대조표 크롤링
	- [ ] Get Cash Flow : 현금흐름표 크롤링

 - 인터넷 검색과 크롤리을 도와주는 Tavily API 사용
 - https://www.tavily.com
 - Tavily 회원 가입 > 로그인 > 1,000 Credits 제공
 - API Keys '+' > API 키 생성 및 복사 > 메모장 저장
 
- Internet Search : 인터넷 검색 기능 추가
- AI Agent > Tool '+' > HTTP Request 검색 후 선택
- HTTP Request를 더블클릭하여 이름을 Internet Search로 변경
	- 아래 항목 입력 참조 사이트 : https://docs.tavily.com/documentation/api-reference/endpoint/search 
	- Description > 인터넷을 활용해 특정 회사에 대한 추가 리서치가 필요할 때 사용하며, 필요한 정보를 수집한다.
	- Method > POST (일반적으로 동적 크롤링을 할 때는 POST, 정적 크롤링을 할 때는 GET를 사용)
	- URL > https://api.tavily.com/search
	- Authorization > Generic Credential Type > Generic Auth Type > Header Auth
		- Header Auth > Creat new credential 생성 시 입력사항
			- Name > Authorization
			- Value > Bearer <token>
				- 여기서 토큰은 API 키를 말함
				- 예시 : Bearer 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

	- Send Headers 활성화
		- Name : Content-Type
		- Value : application/json
	
	- Send Body 활성화
		- Specify Body 항목 > Using JSON으로 변경
		- JSON 항목에 아래 코드 삽입 

⬇⬇⬇

{
  "query": "{{ $fromAI('query','search term') }}",
  "topic": "{{ $fromAI('topic','The category of search. The value should be either general or news') }}",
  "search_depth": "advanced",
  "chunks_per_source": 3,
  "max_results": 1,
  "time_range": null,
  "days": 7,
  "include_answer": false,
  "include_raw_content": false,
  "include_images": true,
  "include_image_descriptions": false,
  "include_domains": [],
  "exclude_domains": []
}

___

**7. AI Agent Tool : Get Price**

- Get Price : 주가 1년치 파악
-  Internet Search 노드 마우스 우클릭 > Duplicate > AI Agent  Tool '+' 연결
- 정보를 가져올 사이트 : - https://finance.yahoo.com

- 아래 항목 입력 참조 사이트 : https://docs.tavily.com/documentation/api-reference/endpoint/extract
	- Description > 특정 회사에 대한 주가 데이터가 필요할 때, Yahoo Finance에서 특정 회사의 주가 히스토리 데이터를 수집합니다.
	- Method > POST
	- URL > https://api.tavily.com/extract
	- Authorization > Generic Credential Type > Generic Auth Type > Header Auth > 기존 연동 사용

	- Send Headers 활성화
		- Name : Content-Type
		- Value : application/json
	
	- Send Body 활성화
		- Specify Body 항목 > Using JSON으로 변경
		- JSON 항목에 아래 코드 삽입

⬇⬇⬇

{
 "urls": "{{$fromAI('url','https://finance.yahoo.com/quote/{ticker}/history/')}}",
  "include_images": false,
  "extract_depth": "basic"
}

___

**8. AI Agent Tool : Get Income**

- Get Income : 손익계산서 크롤링 
- Get Price 노드 마우스 우클릭 > Duplicate > AI Agent  Tool '+' 연결 > 노드 이름 'Get Income' 변경
- 나머지는 Get Price 설정을 유지하고, Description와 Send Body 코드만 변경
- Description > 특정 회사에 대한 손익계산서 데이터가 필요할때 Yahoo Finance에서 손익계산서 데이터를 수집합니다.
- Send Body 아래 코드로 내용 변경, 실제로는 야후 파이낸션 URL만 변경됨

⬇⬇⬇

{
 "urls": "{{$fromAI('url','https://finance.yahoo.com/quote/{ticker}/financials/')}}",
  "include_images": false,
  "extract_depth": "basic"
}

___

**9. AI Agent Tool : Get Sheet**

- Get Sheet : 대차대조표 크롤링 
- Get Price 노드 마우스 우클릭 > Duplicate > AI Agent  Tool '+' 연결 > 노드 이름 'Get Sheet' 변경
- 나머지는 Get Price 설정을 유지하고, Description와 Send Body 코드만 변경
- Description > 특정 회사에 대한 재무제표 데이터가 필요할때 Yahoo Finance에서 재무제표 데이터를 수집합니다.
- Send Body 아래 코드로 내용 변경, 실제로는 야후 파이낸션 URL만 변경됨

⬇⬇⬇

{
 "urls": "{{$fromAI('url','https://finance.yahoo.com/quote/{ticker}/balance-sheet/')}}",
  "include_images": false,
  "extract_depth": "basic"
}

___

**10. AI Agent Tool : Get Cash**

- Get Cash : 현금흐름표 크롤링 
- Get Price 노드 마우스 우클릭 > Duplicate > AI Agent  Tool '+' 연결 > 노드 이름 'Get Cash' 변경
- 나머지는 Get Price 설정을 유지하고, Description와 Send Body 코드만 변경
- Description > 특정 회사에 대한 현금흐름표 데이터가 필요할때 Yahoo Finance에서 현금흐름표 데이터를 수집합니다.
- Send Body 아래 코드로 내용 변경, 실제로는 야후 파이낸션 URL만 변경됨

⬇⬇⬇

{
 "urls": "{{$fromAI('url','https://finance.yahoo.com/quote/{ticker}/cash-flow/')}}",
  "include_images": false,
  "extract_depth": "basic"
}

___

**11. AI Agent Tool : 시스템 프롬프트**

- AI Agent 노드 클릭 > Options > Add Option > System Message 
- 아래 시스템 프롬프트 입력 > 필요 시 ChatGPT를 통해서 변경 후 수정

⬇⬇⬇

당신은 전문 금융 애널리스트이자 자동화된 투자 리서치 시스템입니다.  
오늘 날짜는 {{ $today.format('yyyy-MM-dd') }}입니다.

당신의 임무는 사용자의 질문 의도에 따라 **필요한 데이터만 선별**해  
**전략적 통찰이 담긴 투자 보고서**를 Markdown 형식으로 작성하는 것입니다.

---

📊 사용 가능한 도구:

1. **Get Price**  
- “주가”, “차트”, “흐름”, “가격 변동” 관련 질문이거나 최근 주가 반응이 핵심일 때

2. **Get Income Statement**  
- “매출”, “이익”, “영업이익률”, “수익성” 언급이 있거나 수익 구조 분석이 중요할 때

3. **Get Balance Sheet**  
- “자산”, “부채”, “재무 건전성”, “안정성” 언급이 있거나 재무 구조 평가가 필요할 때

4. **Get Cash Flow**  
- “FCF”, “현금흐름”, “현금창출력” 등 언급이 있거나 실제 유입/지출이 분석 핵심일 때

5. **Search**  
- “뉴스”, “급등 원인”, “CEO 발언”, “정책 변화” 등 언급이 있거나  
  데이터만으로 판단 어려운 경우

---

✅ 도구는 **명시적 키워드** 외에도 문맥상 중요하다고 판단되면 사용하세요.  
**불필요한 모든 도구 호출은 피하고**, 요청당 **토큰 사용량을 최소화**하세요.

---

📐 반드시 아래 **JSON 형식**으로만 출력하세요.  
**설명, 코드 블럭(```), 자연어 문장 등은 절대 포함하지 마세요.**

👇 출력은 다음 형식을 정확히 따르세요:

{
  "ticker": "AAPL",
  "subject": "애플(AAPL) 2025년 5월 투자 분석",
  "text": "# 애플(AAPL) 투자 분석\n\n## 📈 요약\n- 실적 상승세\n- AI 관련 주가 기대감\n\n## 🧾 손익계산서 요약\n...\n\n## 📊 결론\n..."
}