# StyleUs 프론트엔드 UX 개선 구현 로드맵

## 개요
이 문서는 `docs/ux-improvement-proposal.md`에서 제안한 UX 개선사항을 기존 프론트엔드에 적용하기 위한 상세한 개발 계획입니다. 기존 코드를 심층 분석하여 필요한 변경사항과 추가 개발 태스크를 정의합니다.

## 현재 프론트엔드 상태 분석

### 1. 기존 구조 분석
```
현재 5탭 구조: Home → Experts → Closet → Market → My
목표 3탭 구조: Style → Community → Market
```

### 2. 현재 기술 스택
- **Framework**: Next.js 15.2.4 (App Router)
- **UI Components**: Shadcn/ui (40+ 컴포넌트)
- **Styling**: Tailwind CSS (다크 테마 기본)
- **State Management**: React built-in (상태 관리 라이브러리 없음)
- **API**: Mock 데이터 기반
- **i18n**: Next.js 내장 (4개 언어 지원)
- **Authentication**: 쿠키 기반 간단 구현

### 3. 기존 컴포넌트 재사용성 분석

#### ✅ 재사용 가능한 컴포넌트
- **UI 컴포넌트**: 모든 Shadcn/ui 컴포넌트 (40+)
- **Closet 관련**: `ClosetGrid`, `ItemCard`, `CategoryFilter`, `ItemDetail`
- **Market 관련**: `ProductGrid`, `ProductCard`, `MarketFilter`
- **Expert 관련**: `ExpertGrid`, `ExpertProfile`, `ServiceList`, `ReviewList`
- **Layout**: `MainLayout`, `PageTransition`

#### ⚠️ 수정이 필요한 컴포넌트
- **Navigation**: `BottomNavigation` (5탭 → 3탭)
- **Header**: 현재 단순한 로고만 있음
- **Home Components**: 기존 구조와 맞지 않음

#### ❌ 새로 구현해야 하는 컴포넌트
- **Community Feed**: 통합 피드 시스템
- **Expert Consultation**: 컨설팅 예약 및 결제
- **Profile Modal**: 상단 우측 접근 프로필
- **Floating Action Button**: 각 탭별 주요 액션
- **Native Ads**: 자연스러운 광고 컴포넌트

---

## Phase 1: 네비게이션 구조 변경 (1-2주)

### 1.1 BottomNavigation 리팩토링
**파일**: `components/layout/bottom-navigation.tsx`

**현재 코드**:
```typescript
const navItems = [
  { id: "home", path: "/", icon: Home, label: "Home" },
  { id: "experts", path: "/experts", icon: Star, label: "Experts" },
  { id: "closet", path: "/closet", icon: Shirt, label: "Closet" },
  { id: "market", path: "/market", icon: ShoppingBag, label: "Market" },
  { id: "my", path: "/my", icon: User, label: "My" },
]
```

**변경 후**:
```typescript
const navItems = [
  { id: "style", path: "/style", icon: Shirt, label: "Style" },
  { id: "community", path: "/community", icon: Users, label: "Community" },
  { id: "market", path: "/market", icon: ShoppingBag, label: "Market" },
]
```

**작업 내용**:
- [ ] `navItems` 배열 수정
- [ ] 아이콘 import 추가 (`Users` from lucide-react)
- [ ] 레이아웃 조정 (3개 탭에 맞는 간격)
- [ ] 라벨 다국어 키 수정

### 1.2 라우트 구조 변경
**기존 라우트 이동 계획**:
```
/              → /community (기본 피드)
/experts       → /community (서브 섹션)
/closet        → /style
/market        → /market (유지)
/my            → 프로필 모달
```

**작업 내용**:
- [ ] `app/[locale]/style/` 디렉토리 생성
- [ ] `app/[locale]/community/` 디렉토리 생성
- [ ] 기존 `/closet` 내용을 `/style`로 이동
- [ ] 기존 `/` 내용을 `/community`로 이동
- [ ] 기존 `/experts` 내용을 `/community` 서브 섹션으로 통합
- [ ] 기존 `/my` 내용을 모달 컴포넌트로 변경

### 1.3 프로필 모달 구현
**새 파일**: `components/layout/profile-modal.tsx`

**기능**:
- 상단 우측 아바타 클릭 시 풀스크린 모달 오픈
- 기존 `/my` 페이지 내용 포함
- 설정, 거래내역, 컨설팅 내역 등

**작업 내용**:
- [ ] 프로필 모달 컴포넌트 생성
- [ ] 헤더에 아바타 버튼 추가
- [ ] 기존 My 페이지 컴포넌트들 모달에 재사용
- [ ] 모달 내 네비게이션 구현

---

## Phase 2: Style 탭 구현 (2-3주)

### 2.1 Style 메인 페이지 구현
**새 파일**: `app/[locale]/style/page.tsx`

**통합 기능**:
- 내 옷장 (기존 `/closet`)
- OOTD 기록 (기존 `/my`에서 이동)
- 착용 기록 및 분석
- 판매하기 버튼 (마켓 연동)

**작업 내용**:
- [ ] 기존 `ClosetGrid` 컴포넌트 재사용
- [ ] OOTD 섹션 추가
- [ ] 탭 전환 UI 구현 (옷장 | OOTD | 분석)
- [ ] 아이템 상세에서 "판매하기" 버튼 추가

### 2.2 OOTD 기능 강화
**수정 파일**: `components/features/my/ootd-calendar.tsx`

**기능 추가**:
- OOTD 작성 시 옷장 아이템 태그 연동
- 착용 기록 자동 업데이트
- 소셜 공유 기능 (Community 피드로)

**작업 내용**:
- [ ] 옷장 아이템 선택 모달 구현
- [ ] 착용 기록 업데이트 로직
- [ ] 이미지 업로드 기능 강화
- [ ] 태그 시스템 구현

### 2.3 스타일 분석 기능 강화
**수정 파일**: `components/features/my/style-analysis.tsx`

**기능 추가**:
- Cost Per Wear 계산 로직
- 카테고리별 착용 빈도 분석
- 컨설턴트 추천 아이템 표시
- 판매 추천 기능

**작업 내용**:
- [ ] 분석 로직 구현
- [ ] 차트 컴포넌트 추가 (recharts 또는 chart.js)
- [ ] 추천 알고리즘 구현
- [ ] 판매 제안 UI 구현

---

## Phase 3: Community 탭 구현 (3-4주)

### 3.1 Community 메인 페이지 구현
**새 파일**: `app/[locale]/community/page.tsx`

**통합 기능**:
- OOTD 피드 (기존 Home)
- 스타일 챌린지 (새 기능)
- 전문가 무료 팁 (기존 Experts 변형)
- 네이티브 광고 (새 기능)

**작업 내용**:
- [ ] 통합 피드 컴포넌트 구현
- [ ] 탭 전환 UI (For You | 팔로잉 | 챌린지)
- [ ] 무한 스크롤 구현
- [ ] 네이티브 광고 슬롯 추가

### 3.2 전문가 콘텐츠 통합
**수정 파일**: `components/features/experts/`

**기능 변경**:
- 전문가 프로필 → 피드 콘텐츠 형태
- 유료 컨설팅 → 무료 팁 + 컨설팅 CTA
- 독립 탭 → 피드 내 섹션

**작업 내용**:
- [ ] 전문가 피드 카드 컴포넌트 생성
- [ ] 무료 팁 콘텐츠 템플릿 구현
- [ ] 컨설팅 CTA 버튼 추가
- [ ] 전문가 프로필 모달 구현

### 3.3 스타일 챌린지 기능 구현
**새 파일**: `components/features/community/style-challenge.tsx`

**기능**:
- 주간/월간 스타일 챌린지
- 해시태그 기반 참여
- 참여자 피드 표시
- 브랜드 스폰서 챌린지

**작업 내용**:
- [ ] 챌린지 목록 컴포넌트
- [ ] 참여 버튼 및 모달
- [ ] 해시태그 시스템
- [ ] 참여자 갤러리 구현

### 3.4 네이티브 광고 시스템 구현
**새 파일**: `components/features/community/native-ads.tsx`

**기능**:
- 피드 내 자연스러운 광고 표시
- 브랜드 스폰서 콘텐츠
- 클릭 추적 시스템
- A/B 테스트 지원

**작업 내용**:
- [ ] 광고 카드 컴포넌트 생성
- [ ] 광고 배치 로직 구현
- [ ] 클릭 추적 시스템
- [ ] 광고 관리 인터페이스

---

## Phase 4: 전문가 컨설팅 시스템 구현 (4-5주)

### 4.1 컨설팅 예약 시스템
**새 파일**: `components/features/experts/consultation-booking.tsx`

**기능**:
- 전문가 프로필에서 "내 옷장 분석 요청" 버튼
- 사용자 옷장 미리보기 전송
- 전문가의 컨설팅 제안 수신
- 가격 협상 및 확정

**작업 내용**:
- [ ] 예약 모달 구현
- [ ] 옷장 데이터 전송 로직
- [ ] 전문가 제안 UI
- [ ] 가격 표시 및 협상 시스템

### 4.2 Stripe 결제 시스템 구현
**새 파일**: `lib/payment/stripe.ts`

**기능**:
- Stripe 결제 처리
- 전문가에게 직접 결제 (플랫폼 수수료 차감)
- 에스크로 시스템 (컨설팅 완료 후 정산)
- 환불 처리

**작업 내용**:
- [ ] Stripe SDK 설정
- [ ] 결제 플로우 구현
- [ ] 에스크로 로직 구현
- [ ] 환불 시스템 구현
- [ ] 수수료 계산 로직

### 4.3 컨설팅 진행 시스템
**새 파일**: `components/features/experts/consultation-session.tsx`

**기능**:
- 컨설팅 채팅방
- 이미지 공유 기능
- 추천 상품 리스트
- 컨설팅 완료 처리

**작업 내용**:
- [ ] 실시간 채팅 구현 (Socket.io 또는 WebRTC)
- [ ] 파일 업로드 시스템
- [ ] 추천 상품 관리
- [ ] 완료 확인 프로세스

### 4.4 리뷰 및 평가 시스템
**수정 파일**: `components/features/experts/review-list.tsx`

**기능 추가**:
- 컨설팅 후 리뷰 작성
- 평점 시스템 강화
- 전문가 검증 시스템
- 리뷰 신뢰도 관리

**작업 내용**:
- [ ] 리뷰 작성 모달
- [ ] 평점 계산 로직
- [ ] 검증 배지 시스템
- [ ] 리뷰 신고 기능

---

## Phase 5: 마켓플레이스 강화 (2-3주)

### 5.1 옷장-마켓 연동 강화
**수정 파일**: `components/features/closet/item-detail.tsx`

**기능 추가**:
- 아이템 상세에서 "판매하기" 버튼
- 자동 가격 추천 (AI 기반)
- 판매 상태 관리
- 구매 관심 알림

**작업 내용**:
- [ ] 판매 전환 버튼 추가
- [ ] AI 가격 추천 API 연동
- [ ] 상태 관리 시스템
- [ ] 알림 시스템 구현

### 5.2 거래 안전성 강화
**새 파일**: `components/features/market/transaction-safety.tsx`

**기능**:
- 안전 거래 가이드
- 거래자 신뢰도 점수
- 안전 결제 시스템
- 분쟁 해결 시스템

**작업 내용**:
- [ ] 안전 거래 UI 구현
- [ ] 신뢰도 점수 시스템
- [ ] 에스크로 결제 시스템
- [ ] 분쟁 처리 인터페이스

---

## Phase 6: 성능 최적화 및 고도화 (2-3주)

### 6.1 상태 관리 시스템 도입
**새 파일**: `lib/store/`

**도입 라이브러리**: Zustand
**관리 상태**:
- 사용자 인증 상태
- 옷장 아이템 상태
- 피드 데이터 상태
- UI 상태 (모달, 필터 등)

**작업 내용**:
- [ ] Zustand 설치 및 설정
- [ ] 스토어 구조 설계
- [ ] 컴포넌트 상태 마이그레이션
- [ ] 전역 상태 최적화

### 6.2 실제 API 연동
**수정 파일**: `lib/api/`

**변경 사항**:
- Mock 데이터 제거
- 실제 백엔드 API 연동
- 에러 처리 강화
- 로딩 상태 관리

**작업 내용**:
- [ ] API 엔드포인트 정의
- [ ] HTTP 클라이언트 설정 (axios)
- [ ] 에러 바운더리 구현
- [ ] 로딩 스켈레톤 추가

### 6.3 성능 최적화
**최적화 영역**:
- 이미지 최적화
- 코드 스플리팅
- 메모이제이션
- 번들 크기 최적화

**작업 내용**:
- [ ] Next.js Image 최적화 활성화
- [ ] React.memo 적용
- [ ] 동적 import 구현
- [ ] 번들 분석 및 최적화

---

## Phase 7: 고급 기능 구현 (선택적, 3-4주)

### 7.1 AI 기반 추천 시스템
**새 파일**: `lib/ai/recommendation.ts`

**기능**:
- 개인 스타일 분석
- 아이템 추천 알고리즘
- 컬러 매칭 시스템
- 시즌별 추천

**작업 내용**:
- [ ] AI 모델 API 연동
- [ ] 추천 알고리즘 구현
- [ ] 컬러 분석 시스템
- [ ] 개인화 엔진 구현

### 7.2 소셜 기능 강화
**새 파일**: `components/features/social/`

**기능**:
- 팔로우/팔로워 시스템
- 댓글 및 좋아요
- 공유 기능
- 알림 시스템

**작업 내용**:
- [ ] 소셜 그래프 구현
- [ ] 상호작용 UI 구현
- [ ] 알림 센터 구현
- [ ] 공유 기능 구현

### 7.3 분석 및 대시보드
**새 파일**: `components/features/analytics/`

**기능**:
- 사용자 행동 분석
- 옷장 가치 추적
- 트렌드 분석
- 개인 스타일 진단

**작업 내용**:
- [ ] 분석 대시보드 구현
- [ ] 차트 및 그래프 구현
- [ ] 트렌드 분석 시스템
- [ ] 진단 알고리즘 구현

---

## 기술적 고려사항

### 1. 상태 관리 전략
```typescript
// lib/store/auth.ts
interface AuthState {
  user: User | null
  isAuthenticated: boolean
  login: (credentials: LoginCredentials) => Promise<void>
  logout: () => void
}

// lib/store/closet.ts
interface ClosetState {
  items: ClosetItem[]
  categories: Category[]
  addItem: (item: ClosetItem) => void
  updateItem: (id: string, updates: Partial<ClosetItem>) => void
  deleteItem: (id: string) => void
}

// lib/store/community.ts
interface CommunityState {
  feed: FeedItem[]
  experts: Expert[]
  challenges: Challenge[]
  loadFeed: () => Promise<void>
  addPost: (post: Post) => void
}
```

### 2. API 구조 개선
```typescript
// lib/api/base.ts
const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  timeout: 10000,
})

// Interceptors for auth, error handling, loading states
apiClient.interceptors.request.use(authInterceptor)
apiClient.interceptors.response.use(responseInterceptor, errorInterceptor)
```

### 3. 결제 시스템 아키텍처
```typescript
// lib/payment/types.ts
interface ConsultationPayment {
  expertId: string
  amount: number
  currency: 'KRW' | 'USD'
  platformFee: number
  consultationType: 'wardrobe' | 'shopping' | 'styling'
}

// lib/payment/stripe.ts
export async function processConsultationPayment(
  payment: ConsultationPayment
): Promise<PaymentResult>
```

### 4. 실시간 기능 고려사항
```typescript
// lib/websocket/consultation.ts
interface ConsultationSession {
  sessionId: string
  expertId: string
  userId: string
  status: 'active' | 'completed' | 'cancelled'
  messages: Message[]
}

// WebSocket or Server-Sent Events for real-time updates
```

---

## 마이그레이션 계획

### 1. 데이터 마이그레이션
- 기존 Mock 데이터 → 실제 데이터베이스 스키마
- 사용자 데이터 구조 정의
- 옷장 아이템 데이터 구조 개선

### 2. 컴포넌트 마이그레이션
- 기존 컴포넌트 재사용성 검토
- 새로운 디자인 시스템 적용
- 일관된 인터페이스 구현

### 3. 라우팅 마이그레이션
- 기존 URL 구조 → 새로운 3탭 구조
- 리다이렉트 규칙 정의
- SEO 최적화

---

## 품질 보증

### 1. 테스트 전략
- [ ] 단위 테스트 (Jest + React Testing Library)
- [ ] API 모킹 테스트 (MSW)
- [ ] E2E 테스트 (Playwright)
- [ ] 결제 플로우 테스트 (Frontend만)

### 2. 성능 모니터링
- [ ] Core Web Vitals 모니터링
- [ ] 번들 크기 모니터링
- [ ] API 응답 시간 모니터링 (Frontend)
- [ ] 사용자 행동 분석 (Frontend)

### 3. 보안 고려사항
- [ ] 결제 토큰 보안 (Frontend)
- [ ] 사용자 데이터 보호 (Frontend)
- [ ] API 토큰 관리
- [ ] XSS/CSRF 방어 (Frontend)

---

## 예상 개발 일정

### 총 개발 기간: 17-21주

0. **Phase 0**: OpenAPI 사양 정의 (1주)
1. **Phase 1**: 네비게이션 구조 변경 (2주)
2. **Phase 2**: Style 탭 구현 (3주)
3. **Phase 3**: Community 탭 구현 (4주)
4. **Phase 4**: 전문가 컨설팅 시스템 (5주)
5. **Phase 5**: 마켓플레이스 강화 (3주)
6. **Phase 6**: 성능 최적화 (3주)
7. **Phase 7**: 고급 기능 (선택적, 4주)

### 인력 구성 제안
- **Frontend 개발자**: 2-3명
- **UI/UX 디자이너**: 1명
- **QA 엔지니어**: 1명

### 우선순위 기준
1. **High**: OpenAPI 사양 정의 및 사용자 경험 핵심 개선 (Phase 0-3)
2. **Medium**: 수익화 모델 구현 (Phase 4-5)
3. **Low**: 고급 기능 및 최적화 (Phase 6-7)

---

## 성공 지표

### 1. 개발 성공 지표
- [ ] 모든 기존 기능 유지
- [ ] 3탭 구조 완전 구현
- [ ] 전문가 컨설팅 시스템 완성 (Frontend)
- [ ] 결제 플로우 안정화 (Frontend)
- [ ] OpenAPI 사양 완전 구현

### 2. 비즈니스 성공 지표
- [ ] 사용자 체류 시간 30% 증가
- [ ] 컨설팅 전환율 5% 달성
- [ ] 마켓 거래량 50% 증가
- [ ] 앱 이탈률 20% 감소

### 3. 기술 성공 지표
- [ ] 페이지 로드 시간 2초 이내
- [ ] 모바일 성능 점수 90+ 달성
- [ ] 에러율 1% 미만 유지
- [ ] 테스트 커버리지 80% 이상

---

## 결론

이 로드맵은 현재 잘 구축된 기반 위에서 UX를 개선하는 점진적 접근 방식을 제안합니다. 기존의 Shadcn/ui 컴포넌트 시스템과 Next.js App Router 구조를 최대한 활용하면서, 사용자 경험을 중심으로 한 3탭 구조로 전환합니다.

**핵심 변경사항:**
1. **OpenAPI 우선 개발**: 백엔드와 독립적으로 API 사양을 먼저 정의
2. **Frontend 전용 범위**: 결제는 Stripe SDK, 실시간 기능은 WebSocket 클라이언트만
3. **Mock 서버 활용**: 백엔드 개발과 병렬로 진행 가능한 구조

특히 전문가 컨설팅 시스템의 Frontend 부분과 Stripe 결제 연동이 수익화의 핵심이므로, 이 부분에 가장 많은 리소스를 할당하는 것이 중요합니다. OpenAPI 사양 정의가 선행되어야 효율적인 개발이 가능합니다.

성공적인 구현을 위해서는 단계별 개발과 지속적인 사용자 피드백 수집이 필수적입니다.