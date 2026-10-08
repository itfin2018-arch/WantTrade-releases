# WantTrade MCP 도구 목록

WantTrade가 AI 도구에 연결해 주는 두 MCP의 도구를 정리한 문서입니다. 도구 이름을 직접 부를 필요는 없습니다. AI에게 "삼성전자 호가 보여줘"처럼 말로 요청하면 AI가 맞는 도구를 골라 씁니다.

| MCP | 이름 | 도구 수 | mercury 접속 |
| --- | --- | --- | --- |
| DevTool | `mercury-devtool` | 8개 | 하지 않음 (문서만 봄) |
| Trading | `mercury-trading` | 조회 전용 17개 / 모의·실계좌 21개 | 함 |

Trading MCP는 모드에 따라 등록되는 도구가 다릅니다. 조회 전용 모드에는 주문 도구 4개가 아예 없습니다.

## 한눈에 보기

| MCP | 분류 | 도구 | 하는 일 | 모드 |
| --- | --- | --- | --- | --- |
| DevTool | 문서 | `search_tr` | TR 검색·전체 목록 | — |
| | | `get_tr_spec` | TR 요청 형식·필드 | — |
| | | `get_tr_rules` | TR 서버 검증 순서·거부 코드 | — |
| | | `lookup_code` | 코드값·에러코드 | — |
| | | `get_realtime_spec` | 실시간 규격 | — |
| | | `get_data_schema` | 데이터 구조 | — |
| | | `search_external_api` | 외부 API 규격 | — |
| | 검사 | `validate_tr_payload` | TR 요청 검사 (전송 안 함) | — |
| Trading | 상태 | `get_trading_status` | 주문 가능 상태·한도·오늘 사용량 | 전부 |
| | 시세 | `get_quote` | 호가 | 전부 |
| | | `get_last_trade` | 최근 체결 | 전부 |
| | | `get_chart` | 차트 | 전부 |
| | | `get_market_status` | 장 상태 | 전부 |
| | | `get_symbol` | 종목 정보 | 전부 |
| | | `search_symbols` | 종목 검색·주문 허용 종목 확인 | 전부 |
| | 계좌 | `get_balance` | 잔고·예수금 | 전부 |
| | | `get_positions` | 보유 종목 | 전부 |
| | | `get_orders` | 주문 내역·미체결 | 전부 |
| | | `get_fills` | 체결 내역 | 전부 |
| | | `get_orderable_qty` | 주문 가능 수량·금액 | 전부 |
| | 주문 | `place_order` | 신규 주문 미리보기 | 모의·실계좌 |
| | | `modify_order` | 정정 주문 미리보기 | 모의·실계좌 |
| | | `cancel_order` | 취소 주문 미리보기 | 모의·실계좌 |
| | | `confirm_order` | 주문 전송 | 모의·실계좌 |
| | 백테스트 | `list_datasets` | 받아 둔 차트 데이터 목록 | 전부 |
| | | `get_strategy_schema` | 전략 형식·예시 | 전부 |
| | | `run_backtest` | 백테스트 실행 | 전부 |
| | | `list_backtest_results` | 저장된 백테스트 결과 목록 | 전부 |
| | | `get_backtest_result` | 백테스트 결과 | 전부 |

실제로 mercury에 주문을 보내는 도구는 `confirm_order` 하나뿐입니다. 나머지는 조회하거나 미리보기만 합니다.

## DevTool MCP

mercury에 접속하지 않고, 앱에 들어 있는 TR 문서만 봅니다. 설정이 필요 없고 모든 도구가 읽기 전용입니다.

| 도구 | 하는 일 | 입력 | 예시 요청 |
| --- | --- | --- | --- |
| `search_tr` | 허용된 TR을 이름·설명·필드로 검색합니다. 한글·영문 모두 됩니다. 검색어 없이 부르면 허용 TR 전체 목록을 분류 순으로 보여줍니다 | `query` 검색어 (선택)<br>`category` 분류 (선택): quote 시세, order 주문, product 종목·상품, account 계좌, info 정보<br>`limit` 1~50 (검색 기본 10, 목록 기본 50) | "주문 취소 TR 찾아줘", "계좌 TR 전부 보여줘" |
| `get_tr_spec` | TR 하나의 요청 형식 예시, 입력·출력 필드, `.qry`와 서버의 차이를 보여줍니다 | `trcode` (예: `price_get`) | "chart_get 요청 형식 보여줘" |
| `get_tr_rules` | 서버가 검사하는 순서와 거부 에러코드를 보여줍니다. 주문 TR이면 자주 나오는 거부 코드와 주문 타임아웃(10초)도 함께 보여줍니다 | `trcode` | "ord_acc가 거부되는 경우 알려줘" |
| `lookup_code` | 코드값과 에러코드를 찾습니다. 인자 없이 부르면 코드 그룹 목록을 보여줍니다 | `group` 코드 그룹 (선택)<br>`query` 값·이름·에러코드 (선택, 예: `2301`, `시장가`, `2xxx`) | "에러코드 2009가 뭐야?" |
| `get_realtime_spec` | 실시간 등록·수신 메시지 형식, 리얼키, rtcode를 보여줍니다 | `name` qry명·rtcode·리얼키 일부 (선택) | "호가 실시간 등록 형식 알려줘" |
| `get_data_schema` | 데이터 구조를 설명합니다. 실시간 리얼키는 `get_realtime_spec`에서 봅니다 | `topic` (필수): `chart` 차트, `symbol` 종목, `ticker` 종목 식별자, `ws` WS 프로토콜 | "차트 데이터 구조 알려줘" |
| `search_external_api` | LS OpenAPI 등 외부 API 규격과 주의점을 찾습니다 | `query` 검색어 | "LS 차트 조회 주의점 알려줘" |
| `validate_tr_payload` | TR 요청 JSON을 스펙으로 검사합니다. 아무것도 전송하지 않습니다 | `trcode`<br>`payload`: InBlock 객체, `{ "InBlock1": [...] }`, `{ "head", "body" }` 중 하나 | "이 요청 JSON이 맞는지 검사해줘" |

`lookup_code`의 기본 코드 그룹은 다음과 같습니다.

| 그룹 | 내용 |
| --- | --- |
| `sellbuy_type` | 매도/매수 구분 |
| `ord_action` | 주문 행위 (신규·정정·취소) |
| `ord_type` | 주문 유형 (지정가·시장가) |
| `ord_status` | 주문 상태 |
| `chart_interval` | 차트 주기 |
| `market_status` | 장 상태 |
| `market_hours` | 거래 시간 |
| `qual_limit` | 투자자격 한도 |
| `order_timeout` | 주문 타임아웃 |
| `errors` | 에러코드 전체 |

## Trading MCP

### 공통 규칙

- **계좌는 WantTrade에 설정한 계좌 하나뿐입니다.** 어떤 도구에도 계좌를 고르는 입력이 없습니다. 응답에 다른 계좌의 행이 섞여 오면 걸러냅니다. 보유 종목·주문·체결 내역에서는 걸러낸 수를 `dropped_other_accounts`로 알려줍니다.
- **종목은 코드나 이름으로 지정합니다.** 이름에 맞는 종목이 여러 개면 후보 목록과 함께 오류를 돌려줍니다. 이때는 `search_symbols`로 코드를 찾아 다시 요청하세요.
- **목록은 50개씩 나눠서 돌려줍니다.** 응답의 `next_cursor`를 다음 요청의 `cursor`로 넘기면 다음 50개를 받습니다.
- **날짜는 `YYYYMMDD` 형식입니다** (예: `20261006`).

### 상태 (모든 모드)

| 도구 | 하는 일 | 입력 | 예시 요청 |
| --- | --- | --- | --- |
| `get_trading_status` | 지금 주문할 수 있는지와 그 이유, 설정한 한도와 오늘 사용량을 보여줍니다. mercury를 호출하지 않습니다 | 없음 | "지금 주문할 수 있어?", "오늘 한도 얼마나 남았어?" |

돌려주는 값은 다음과 같습니다. 계좌 정보와 접속 서버 주소는 넣지 않습니다.

| 값 | 뜻 |
| --- | --- |
| `can_order`, `order_blockers` | 주문 가능 여부와, 막혀 있다면 그 이유 목록 (모드, 계좌 정보, 긴급 정지, 한도 미설정, 주문 허용 종목, 주문 TR 스펙, mercury 연결) |
| `mode`, `mercury`, `kill_switch` | 모드, mercury 접속 설정·연결 여부, 긴급 정지 여부 |
| `app_approval_required` | WantTrade 창에서 사람이 승인해야 전송되는지 |
| `spec` | TR 스펙 출처(설계 문서 기준 / 소스 인덱싱)와 주문 TR 3개의 확정 여부 |
| `limits` | 주문 한도, 괴리 허용치, 중복 판단 시간, 미리보기 유효 시간 |
| `today` | 오늘 신규 주문 수, 매수 누계, 남은 건수·금액, 결과를 확인하지 못한 주문(`UNKNOWN`) 수 |
| `allowed_symbols` | 주문 허용 종목 |

장 상태, 가격, 잔고는 주문할 때 따로 검사하므로 `can_order`가 true여도 주문이 거부될 수 있습니다.

### 시세 (모든 모드)

| 도구 | 하는 일 | 입력 | 예시 요청 |
| --- | --- | --- | --- |
| `get_quote` | 10단계 매도·매수 호가와 총잔량 | `symbol` | "삼성전자 호가 보여줘" |
| `get_last_trade` | 여러 종목의 최근 체결과 현재가 | `symbols` 1~20개 | "삼성전자, SK하이닉스 현재가 알려줘" |
| `get_chart` | 봉 데이터를 최신순으로 돌려줍니다. 봉 하나는 시각·시가·고가·저가·종가·거래량·거래대금(원)입니다 | `symbol`<br>`interval` 주기 (아래 표)<br>`limit` 1~500 (기본 50)<br>`cursor` 다음 페이지 | "삼성전자 일봉 100개 보여줘" |
| `get_market_status` | 장 상태: `HOLIDAY` 휴장, `BEFORE` 장 전, `OPEN` 장중, `CLOSED` 장 마감. 서버에서 받지 못하면 내장 시간표로 판단하고 `source: "fallback"`을 표시합니다 (공휴일은 모름) | `symbol` | "삼성전자 지금 장 열렸어?" |
| `get_symbol` | 종목 정보: 상·하한가, VI 상·하한, 거래 가능 여부, 상장일, 현재가, 현재가 구간의 호가단위. 52주 고·저는 요청할 때만 계산합니다(일봉 260개를 받아 느림) | `query` 종목 코드 또는 이름<br>`include_52w` 52주 고·저 계산 (선택, 기본 false) | "삼성전자 종목 정보 알려줘", "삼성전자 52주 최고가는?" |
| `search_symbols` | 종목 코드·표준코드·이름 일부로 종목을 찾습니다. 거래 가능 여부와 주문 허용 종목(`ALLOWED_SYMBOLS`)인지 함께 보여줍니다. 정확히 맞는 종목이 먼저 나옵니다 | `query` (선택, 없으면 전체)<br>`tradable_only` 거래 가능 종목만 (기본 false)<br>`allowed_only` 주문 허용 종목만 (기본 false)<br>`limit` 1~50 (기본 20) | "SK 들어가는 종목 찾아줘", "주문 허용 종목 목록 보여줘" |

차트 주기(`interval`)는 다음과 같습니다. 초봉은 조회할 수 없습니다.

| 값 | 주기 | 값 | 주기 |
| --- | --- | --- | --- |
| `t` | 틱 | `H` | 시간 |
| `m` | 분 | `D` | 일 |
| `5m` | 5분 | `W` | 주 |
| `HM` | (정의 확인 중) | `M` | 월 |
| | | `Y` | 년 |

### 계좌 (모든 모드)

계좌 정보(`user_uid`, `acnt_cd`)가 설정돼 있어야 합니다.

| 도구 | 하는 일 | 입력 | 예시 요청 |
| --- | --- | --- | --- |
| `get_balance` | 계좌 정보와 예수금 | 없음 | "내 잔고 알려줘" |
| `get_positions` | 보유 종목(50개씩)과 자산 요약 | `cursor` (선택) | "보유 종목 보여줘" |
| `get_orders` | 주문 내역 | `status`: `all` 전체(기본), `unfilled` 미체결<br>`from`, `to` 기간 (선택)<br>`side`: `buy`, `sell` (선택)<br>`cursor` (선택) | "오늘 미체결 주문 보여줘" |
| `get_fills` | 체결 내역 | `from`, `to`, `side`, `cursor` (모두 선택) | "이번 주 체결 내역 보여줘" |
| `get_orderable_qty` | 주문 가능 수량·금액 | `symbol`<br>`side`: `buy`, `sell` | "삼성전자 몇 주까지 살 수 있어?" |

### 주문 (모의·실계좌 모드만)

| 도구 | 하는 일 | 입력 |
| --- | --- | --- |
| `place_order` | 지정가 신규 주문을 검사하고 미리보기와 확인 토큰을 만듭니다. **전송하지 않습니다** | `symbol`<br>`side`: `buy`, `sell`<br>`qty` 수량 (양의 정수)<br>`price` 가격<br>`reason` 주문 사유 (최대 500자, 감사로그에 남음) |
| `modify_order` | 미체결 주문의 가격을 바꾸는 미리보기. 가격만 바꿀 수 있습니다 | `ord_no` 주문번호<br>`new_price` 새 가격<br>`reason` |
| `cancel_order` | 미체결 주문의 남은 수량 전부를 취소하는 미리보기 | `ord_no`<br>`reason` |
| `confirm_order` | 미리보기 토큰의 주문을 **실제로 전송**합니다. 토큰은 한 번만 쓸 수 있고 60초 동안 유효합니다 | `confirm_token` |

#### 주문 순서

1. AI가 `place_order`(또는 `modify_order`, `cancel_order`)를 부릅니다. 아래 검사를 모두 통과하면 미리보기와 확인 토큰이 나옵니다.
2. AI가 미리보기를 보여 주고 사용자에게 확인을 받습니다.
3. AI가 `confirm_order`를 부릅니다.
   - 앱 서버 방식의 실계좌 주문은 WantTrade 창에서 사람이 승인해야 전송됩니다. 모의 주문은 설정한 경우에만 승인을 받습니다.
   - Claude Code는 이 단계에서 권한 확인을 묻거나(모의) 막습니다(실계좌 기본값).
4. 전송 직전에 장 상태·가격·한도·중복을 다시 검사한 뒤 전송합니다.

#### 미리보기에서 검사하는 항목

| 구분 | 검사 | 신규 | 정정 | 취소 |
| --- | --- | --- | --- | --- |
| 기본 | 조회 전용 모드가 아닌지, 긴급 정지가 꺼져 있는지, 주문 한도가 설정돼 있는지, 주문 TR 스펙이 확정됐는지 | ✓ | ✓ | ✓ |
| 소유 | 그 주문번호가 설정한 계좌의 미체결 주문인지 | | ✓ | ✓ |
| 종목 | 주문 허용 종목(`ALLOWED_SYMBOLS`)인지, 거래 가능한 종목인지, 상장일이 지났는지 | ✓ | ✓ | |
| 장 | 장중(`OPEN`)인지 | ✓ | ✓ | ✓ |
| 가격 | 0보다 큰지, 호가단위에 맞는지, 상·하한가 안인지, 현재가 대비 괴리(`PRICE_BAND_PCT`) 안인지 | ✓ | ✓ | |
| 수량 | 양의 정수인지 | ✓ | | |
| 잔고 | 매수는 주문 가능 금액 이하인지, 매도는 주문 가능 수량 이하인지 | ✓ | 늘어나는 금액만 | |
| 한도 | 1회 주문 금액, 당일 매수 누계, 당일 신규 주문 수, 미체결 주문 수 | ✓ | 1회 금액·당일 누계 | |
| 중복 | 10초 안에 같은 주문을 이미 보냈는지 | ✓ | ✓ | ✓ |

판단에 필요한 값(현재가, 주문 가능 금액 등)을 서버 응답에서 찾지 못하면 통과시키지 않습니다.

#### `confirm_order` 결과

| 결과 | 뜻 | 할 일 |
| --- | --- | --- |
| `ACCEPTED` | 접수됐습니다. 주문번호(`ord_no`)가 함께 옵니다 | — |
| `UNKNOWN` | 응답을 받지 못해(시간 초과·연결 끊김) **주문이 들어갔는지 알 수 없습니다.** 다시 보내지 않으며, 당일 누계와 건수에 포함됩니다 | 같은 주문을 다시 보내지 말고 `get_orders`로 실제 접수 여부부터 확인 |
| 오류 (서버 거부) | 서버가 받았지만 거부했습니다. 에러코드와 이름이 함께 나옵니다 | 에러코드는 DevTool의 `lookup_code`로 찾아볼 수 있음 |
| 오류 (전송 안 함) | 연결이 없거나 다시 검사에서 막혀 **주문이 나가지 않았습니다.** 승인 거부, 토큰 만료·재사용, 다른 연결에서 만든 토큰도 여기에 해당합니다 | 원인을 해결하고 미리보기부터 다시 |

### 백테스트 (모든 모드)

| 도구 | 하는 일 | 입력 |
| --- | --- | --- |
| `list_datasets` | 이 PC에 받아 둔 차트 데이터 목록 (종목, 주기, 봉 수, 기간, 마지막 동기화 시각) | `symbol` (선택) |
| `get_strategy_schema` | 아래 [전략 형식](#전략-형식)의 설명, 예시, JSON Schema, `run_backtest` 옵션, 백테스트의 한계를 돌려줍니다. AI가 전략을 만들기 전에 이 도구로 형식을 확인합니다. mercury를 호출하지 않습니다 | 없음 |
| `run_backtest` | 전략으로 백테스트합니다. 실행 전에 필요한 차트 데이터를 서버에서 이어 받아 저장하고, 받지 못하면 저장된 데이터만 씁니다. 결과는 저장되고 `result_id`가 나옵니다 | `symbols` 1~20개<br>`interval`: `m` 분, `D` 일, `W` 주<br>`from`, `to`: `YYYYMMDD` 또는 `YYYYMMDDHHmm`<br>`strategy` 전략 (아래 형식)<br>`initial_cash` 시작 금액<br>`fee_bps` 수수료 0~1000 (선택, 기본 0)<br>`slippage_ticks` 슬리피지 0~100틱 (선택, 기본 0)<br>`fill`: `next_open` 다음 봉 시가(기본), `close` 신호 봉 종가 |
| `list_backtest_results` | 이 PC에 저장된 백테스트 결과를 최신순으로 보여줍니다 (`result_id`, 실행 시각, 전략 이름, 종목, 주기, 기간, 수익률, MDD, 거래 수). 다른 대화에서 실행한 결과를 다시 찾을 때 씁니다. mercury를 호출하지 않습니다 | `symbol` 종목 코드 (선택, 이 종목이 들어간 결과만)<br>`limit` 1~50 (기본 20)<br>`cursor` 다음 페이지 (선택) |
| `get_backtest_result` | 저장된 결과를 봅니다. `result_id`를 모르면 `list_backtest_results`로 찾습니다 | `result_id`<br>`include`: `summary` 요약(기본), `trades` 거래 내역, `equity` 자산 추이<br>`limit` 1~5000 (기본 200) |

`fee_bps`는 체결 금액 대비 수수료를 1만분율로 넣습니다 (예: 0.015% → `1.5`). 넣지 않으면 수수료 0으로 계산하고 경고를 남깁니다.

#### 전략 형식

전략은 정해진 형식의 JSON으로만 받습니다. 코드는 실행하지 않습니다. AI는 `get_strategy_schema`로 같은 내용을 확인할 수 있습니다.

```json
{
  "name": "SMA 5/20 골든크로스",
  "indicators": {
    "fast": { "type": "sma", "period": 5 },
    "slow": { "type": "sma", "period": 20 }
  },
  "entry": { "cross_over": [{ "ind": "fast" }, { "ind": "slow" }] },
  "exit": { "cross_under": [{ "ind": "fast" }, { "ind": "slow" }] },
  "position": { "type": "percent_equity", "ratio": 0.95 },
  "stop_loss_pct": 5
}
```

| 항목 | 필수 | 쓸 수 있는 값 |
| --- | --- | --- |
| `indicators` | 선택 | 이름(영문·숫자·`_`, 32자 이내)별 지표 정의, 최대 20개<br>`sma`, `ema`: `period`<br>`rsi`: `period` (기본 14)<br>`bb` 볼린저 밴드: `period` (기본 20), `stddev` (기본 2)<br>`highest`, `lowest`: `period`<br>모든 지표에 `source`를 줄 수 있음: `open`, `high`, `low`, `close`(기본), `volume` |
| `entry` | 필수 | 진입 조건 (아래 조건 형식) |
| `exit` | 선택 | 청산 조건 |
| `position` | 필수 | `{ "type": "fixed_qty", "qty": 10 }` 고정 수량<br>`{ "type": "fixed_cash", "amount": 1000000 }` 고정 금액<br>`{ "type": "percent_equity", "ratio": 0.95 }` 자산 대비 비율 |
| `stop_loss_pct` | 선택 | 진입가 대비 손절 % |
| `take_profit_pct` | 선택 | 진입가 대비 익절 % |

조건은 다음 형식으로 씁니다.

| 형식 | 뜻 |
| --- | --- |
| `{ "op": ">", "left": A, "right": B }` | 비교. `op`는 `>`, `>=`, `<`, `<=`, `==` |
| `{ "cross_over": [A, B] }` | A가 B를 위로 뚫음 (직전 봉 A ≤ B, 이번 봉 A > B) |
| `{ "cross_under": [A, B] }` | A가 B를 아래로 뚫음 |
| `{ "and": [...] }`, `{ "or": [...] }`, `{ "not": 조건 }` | 조건 묶기 |

A, B 자리에는 다음을 쓸 수 있습니다.

- 숫자
- 가격: `{ "price": "close" }`
- 지표: `{ "ind": "fast" }`. 볼린저 밴드는 `"band": "upper" | "middle" | "lower"`를 꼭 붙입니다
- 몇 봉 전 값: 가격과 지표에 `"offset": n`을 붙입니다

#### 결과 요약 (`summary`)

비율은 소수로 나옵니다 (0.12 = 12%).

| 항목 | 뜻 |
| --- | --- |
| `total_return` | 총수익률 |
| `cagr` | 연환산 수익률 |
| `mdd` | 최대 낙폭 (고점 대비 가장 크게 떨어진 비율) |
| `sharpe` | 샤프 지수 (무위험 수익률 0 기준) |
| `win_rate` | 승률 (청산된 거래만) |
| `profit_factor` | 손익비 (총이익 ÷ 총손실). 손실 거래가 없으면 null |
| `trades` | 매수→매도 왕복 거래 수 |
| `exposure` | 전체 기간 중 종목을 들고 있던 비율 |
| `buy_hold_return`, `excess_return` | 그냥 사서 들고 있었을 때의 수익률과, 그 대비 초과 수익률 |
| `initial_cash`, `final_equity` | 시작 금액, 최종 자산 |
| `skipped_entries` | 현금이 모자라 건너뛴 진입 |
| `open_positions` | 기간이 끝날 때 들고 있던 종목 (마지막 종가로 평가) |

#### 백테스트의 한계

- 상·하한가, VI, 호가 잔량은 반영하지 않습니다. 신호가 나면 항상 체결된다고 봅니다.
- 매수만 합니다(공매도 없음). 종목마다 포지션은 하나만 열고 수량은 정수입니다.
- 손절과 익절이 한 봉에서 둘 다 닿으면 손절로 봅니다.
- 시작일(`from`) 이전 봉은 지표 계산에만 씁니다.

## 현재 버전의 제한

WantTrade 화면 오른쪽 위에 **"TR 스펙: 설계 문서 기준 · 주문 필드 미확정"**이 보이면 아래 제한이 있습니다. AI에게 "지금 주문할 수 있어?"라고 물으면 `get_trading_status`가 막힌 이유를 한 번에 보여줍니다.

| 영향 받는 도구 | 제한 |
| --- | --- |
| 주문 도구 4개 | 모의·실계좌 모드에서 등록은 되지만 미리보기가 거부됩니다 (`order_spec`) |
| `get_symbol` | 호가단위(`tick_unit`)가 비어 있습니다 |
| `run_backtest` | 호가단위표를 받지 못해 가격을 1원 단위로 반올림합니다 |
| DevTool 도구 | 일부 TR의 필드와 검증 규칙이 "미확인"으로 나옵니다 |

이와 별개로 지금은 수수료를 계산하지 않습니다. 주문 미리보기의 예상 수수료(`est_fee`)는 항상 비어 있고, 백테스트 수수료는 `fee_bps`로 직접 넣어야 합니다.

## Claude Code에서 보이는 도구 이름

Claude Code에서는 도구 이름 앞에 MCP 이름이 붙습니다 (예: `mcp__mercury-trading__get_quote`). 권한 설정이나 `/mcp` 화면에서 이 이름으로 보입니다.

WantTrade는 Trading MCP를 추가할 때 Claude Code 설정(`~/.claude/settings.json`)에 `mcp__mercury-trading__confirm_order` 권한을 자동으로 적습니다.

| 모드 | 권한 |
| --- | --- |
| 모의 | 매번 확인 (`ask`) |
| 실계좌 | 차단 (`deny`). 화면에서 "Claude Code에서 live 주문 전송을 허용하고 매번 확인받기"를 켜면 매번 확인 (`ask`) |
| 조회 전용 | 항목 없음 (주문 도구가 없음) |
