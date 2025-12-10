# KIS Auto Trader - 한국투자증권 자동매매 시스템

한국투자증권 Open API 기반 WPF 자동매매 프로그램입니다.

## 📋 주요 기능

### 🔍 종목 스캐닝
- **거래량 급등 감지**: 전일 대비 3배 이상 거래량 급등 종목 자동 탐색
- **골든크로스 감지**: 3분봉 기준 5일/20일 이동평균선 상향돌파 감지
- **실시간 모니터링**: WebSocket을 통한 실시간 시세 수신

### 📈 단타 알고리즘
- 3분봉 이평선 크로스 전략
- 거래량 폭증 진입 전략
- 자동 익절/손절 (기본: 익절 3%, 손절 2%)
- 재매수/재진입 차단 (손절 후 30분)

### 💰 리스크 관리 (1,000만원 기준)
- 1회 최대 매매금액: 100만원 (자본의 10%)
- 최대 보유 종목 수: 5종목
- 일일 손실 한도: 30만원 (자본의 3%)
- 변동성 기반 포지션 사이즈 조절

### 🖥️ GUI 기능
- 실시간 후보 종목 리스트
- 보유 종목 현황 및 손익
- 매매 이력 테이블
- 시스템 로그
- 수동 주문 기능
- Start/Stop 버튼

## 🚀 설치 및 실행

### 사전 요구사항
- .NET 8.0 SDK
- Visual Studio 2022 (권장)
- 한국투자증권 계좌 및 Open API 신청

### 1. 한국투자증권 Open API 신청
1. [KIS Developers](https://apiportal.koreainvestment.com) 접속
2. 회원가입 및 로그인
3. **API 신청** → **KIS Developers 서비스 신청**
4. 모의투자/실전투자 AppKey, AppSecret 발급

### 2. 프로젝트 설정
```bash
# 프로젝트 폴더로 이동
cd KisAutoTraderWpf

# NuGet 패키지 복원
dotnet restore

# 빌드
dotnet build
```

### 3. appsettings.json 설정
```json
{
  "KisApi": {
    "Environment": "vps",  // vps: 모의투자, prod: 실전투자
    
    "ProdAppKey": "실전투자 AppKey",
    "ProdAppSecret": "실전투자 AppSecret",
    
    "VpsAppKey": "모의투자 AppKey",
    "VpsAppSecret": "모의투자 AppSecret",
    
    "AccountNo": "12345678",      // 계좌번호 앞 8자리
    "AccountProdCode": "01",       // 계좌번호 뒤 2자리
    "HtsId": "YOUR_HTS_ID"        // HTS 아이디
  }
}
```

### 4. 실행
```bash
dotnet run
```

또는 Visual Studio에서 F5로 실행

## ⚙️ 설정 항목

### Trading (매매 설정)
| 항목 | 기본값 | 설명 |
|------|--------|------|
| InitialCapital | 10,000,000 | 초기 자본금 |
| MaxPositionRatio | 10% | 1회 매매 최대 비율 |
| MaxPositionAmount | 1,000,000 | 1회 최대 매매금액 |
| MaxHoldingCount | 5 | 최대 보유 종목 수 |
| DailyLossLimitPercent | 3% | 일일 최대 손실률 |
| DailyLossLimitAmount | 300,000 | 일일 최대 손실금액 |
| TakeProfitPercent | 3% | 익절률 |
| StopLossPercent | 2% | 손절률 |
| RebuyBlockMinutes | 30 | 재매수 금지 시간 (분) |

### Scanner (스캐너 설정)
| 항목 | 기본값 | 설명 |
|------|--------|------|
| VolumeMultiplier | 3.0 | 거래량 급등 배율 |
| MinTradingValue | 10 | 최소 거래대금 (억원) |
| MinPrice | 1,000 | 최소 가격 |
| MaxPrice | 100,000 | 최대 가격 |
| ShortMaPeriod | 5 | 단기 이평선 기간 |
| LongMaPeriod | 20 | 장기 이평선 기간 |
| ScanIntervalSeconds | 10 | 스캔 주기 (초) |

## 📁 프로젝트 구조

```
KisAutoTraderWpf/
├── KisAutoTraderWpf.sln           # 솔루션 파일
├── README.md                       # 이 문서
└── KisAutoTraderWpf/
    ├── App.xaml                    # 애플리케이션 리소스
    ├── App.xaml.cs
    ├── MainWindow.xaml             # 메인 윈도우 UI
    ├── MainWindow.xaml.cs
    ├── appsettings.json            # 설정 파일
    ├── nlog.config                 # 로그 설정
    │
    ├── Models/
    │   ├── AppSettings.cs          # 설정 모델
    │   ├── MarketSnapshot.cs       # 시세 데이터 모델
    │   └── OrderRecord.cs          # 주문 기록 모델
    │
    ├── Services/
    │   ├── ApiClient.cs            # REST API 클라이언트
    │   ├── WsClient.cs             # WebSocket 클라이언트
    │   ├── ScannerService.cs       # 종목 스캐너
    │   ├── ExecutorService.cs      # 주문 실행
    │   ├── RiskManager.cs          # 리스크 관리
    │   └── DbService.cs            # SQLite DB
    │
    ├── ViewModels/
    │   └── MainViewModel.cs        # 메인 뷰모델
    │
    └── Converters/
        └── Converters.cs           # 값 변환기
```

## ⚠️ 주의사항

### 모의투자 먼저 테스트
- **반드시 모의투자 환경에서 충분히 테스트한 후 실전투자에 사용하세요.**
- 모의투자와 실전투자의 API 동작이 다를 수 있습니다.

### API 호출 제한
- 한국투자증권 API는 초당 호출 횟수 제한이 있습니다.
- 과도한 API 호출 시 일시적으로 차단될 수 있습니다.

### 투자 위험
- **자동매매 프로그램 사용으로 인한 손실에 대해 개발자는 책임지지 않습니다.**
- 투자 결정은 본인의 판단과 책임 하에 이루어져야 합니다.
- 자동매매 시스템은 시장 상황에 따라 예상과 다르게 동작할 수 있습니다.

### 장 운영 시간
- 정규장: 09:00 ~ 15:30
- 동시호가: 08:30 ~ 09:00, 15:20 ~ 15:30
- 시간외 거래는 지원하지 않습니다.

## 📊 알고리즘 설명

### 매수 조건 (AND 조건)
1. 거래량이 전일 대비 3배 이상 급등
2. 3분봉 기준 5일 이평선이 20일 이평선을 상향 돌파 (골든크로스)
3. 거래대금 10억원 이상
4. 주가 1,000원 ~ 100,000원 범위
5. 상한가/하한가 아님
6. 리스크 한도 내

### 매도 조건 (OR 조건)
1. **익절**: 매수가 대비 +3% 도달
2. **손절**: 매수가 대비 -2% 도달
3. **일일 손실 한도**: 일일 누적 손실 30만원 초과 시 전량 청산
4. **수동 매도**: 사용자 직접 매도

### 재매수 차단
- 손절 매도 후 해당 종목 30분간 재매수 차단
- 과도한 손실 방지 목적

## 🔧 문제 해결

### 토큰 발급 실패
- AppKey, AppSecret 확인
- 모의/실전 환경 설정 확인
- 네트워크 연결 상태 확인

### WebSocket 연결 실패
- HTS ID 확인
- 방화벽 설정 확인
- VPN 사용 시 해제 후 시도

### 주문 실패
- 주문 가능 시간 확인 (09:00 ~ 15:20)
- 계좌 잔고 확인
- 주문 수량/가격 확인

## 📝 로그 파일

- 위치: `./logs/`
- 일반 로그: `{날짜}.log`
- 매매 로그: `trades/{날짜}_trades.log`
- 보관 기간: 30일

## 📜 라이선스

이 프로젝트는 개인 학습 및 연구 목적으로 제공됩니다.
상업적 사용 시 한국투자증권의 API 이용약관을 확인하세요.

## 🔗 참고 링크

- [KIS Developers 포털](https://apiportal.koreainvestment.com)
- [한국투자증권 Open API GitHub](https://github.com/koreainvestment/open-trading-api)
- [API 문서](https://apiportal.koreainvestment.com/apiservice)

---

**⚠️ 투자에 대한 모든 책임은 투자자 본인에게 있습니다.**
