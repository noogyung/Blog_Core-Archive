---
topic: blogspot-custom-domain
title_kr: 블로그스팟 맞춤 도메인(Custom Domain) 연결 및 DNS 설정 가이드
category: Procedure
sub_category: DNS-Configuration
version: 2026-08-14
status: Verified
created_date: 2026-08-14
last_modified: 2026-08-14
language: KR
tags: [Blogspot, CustomDomain, DNS, CNAME, ARecord, Blogger, 도메인설정]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/blogspot-custom-domain-setup-guide.html
blog_draft_date: 2026-08-14
blog_id: core-archive
blog_published: true
series_id: null
series_title: null
series_part: null
series_prev_slug: null
---
# Knowledge File: blogspot-custom-domain
## Category: Procedure
## Date: 2026-08-14

===== KNOWLEDGE PACKAGE START =====

#### 💡 Core Concept (핵심 개념)
* **[Blogger 맞춤 도메인 (Custom Domain)]:** 구글 블로그스팟의 기본 서브도메인(`*.blogspot.com`) 대신 개인 소유의 독자 도메인(`www.example.com` 또는 `example.com`)을 연결하는 기능. [FACT]
* **[도메인 연결의 핵심 이점]:**
  * 독립적인 사이트 브랜딩 및 신뢰도 확보 [OPINION]
  * 루트 도메인 애드센스 승인 시 서브도메인 무심사 광고 송출 가능 [FACT]
  * 플랫폼 이전 시 기존 검색 순위(SEO) 및 백링크 유지 용이 [FACT]
* **[필요 DNS 레코드 구성]:**
  * CNAME 레코드 2개: `www -> ghs.google.com` 및 구글 소유권 확인용 고유 보안 토큰. [FACT]
  * A 레코드 4개: 루트 도메인(`@`) 접속을 위한 구글 고정 IP 4개 (`216.239.32.21`, `216.239.34.21`, `216.239.36.21`, `216.239.38.21`). [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: 블로그스팟 CNAME 토큰 생성]
1. 구글 블로그스팟 대시보드 접속 후 **[설정] > [게시] > [맞춤 도메인]** 클릭. [✅]
2. 연결할 도메인(예: `www.yourdomain.com`)을 입력 후 **[저장]** 클릭. [✅]
3. 화면에 표시되는 2개의 CNAME 정보(호스트 이름, 대상 주소)를 확인. [✅]

##### [2단계: 도메인 DNS 레코드 등록 (Cloudflare 기준)]
1. Cloudflare DNS 관리 대시보드 접속. [✅]
2. CNAME 1: 이름 `www` -> 대상 `ghs.google.com` (프록시: DNS 전용 / 회색 구름) 등록. [✅]
3. CNAME 2: 이름 `{구글 고유 문자열}` -> 대상 `{gv-...googlehosted.com}` (프록시: DNS 전용) 등록. [✅]
4. A 레코드: 이름 `@`(루트)에 구글 고정 IP 4개(`216.239.32.21`, `216.239.34.21`, `216.239.36.21`, `216.239.38.21`) (프록시: DNS 전용) 등록. [✅]
5. ⚠️ Cloudflare 주황색 구름(Proxied) 활성화 시 구글 도메인 검증 실패하므로 반드시 DNS 전용(회색 구름) 유지. [FACT]

##### [3단계: 블로그스팟 연결 및 HTTPS 활성화]
1. DNS 전파 대기 후 블로그스팟 **[맞춤 도메인]**에서 다시 **[저장]**. [✅]
2. **[도메인 리디렉션]** 활성화 (`yourdomain.com` -> `www.yourdomain.com`). [✅]
3. **[HTTPS 사용 가능 여부]** 활성화 후 인증서 발급 대기 (약 15분~1시간). [✅]
4. **[HTTPS 리디렉션]** 활성화하여 모든 트래픽을 HTTPS로 전환. [✅]

---

#### ⚠️ Errors & Solutions (오류 및 해결)

* **[맞춤 도메인 입력 시 에러 발생]**
  * 현상: `yourdomain.com`만 입력 시 저장이 거부됨.
  * 해결: 반드시 `www` 등의 서브도메인을 포함하여 입력해야 CNAME 정보가 정상 발급됨. [FACT]
* **[DNS 레코드 등록 직후 저장/활성화 지연]**
  * 현상: DNS 레코드를 등록했는데도 블로그스팟에서 저장이 되지 않거나 도메인 연결이 지연됨.
  * 원인: Google Blogger 공식 지원 문서에 따르면 DNS 레코드 설정 후 시스템에서 활성화되기까지 **최대 1시간 이상** 소요될 수 있음. [FACT] (출처: [Google Blogger 고객센터](https://support.google.com/blogger/answer/1233387))
  * 해결: 설정을 변경하지 말고 최소 1시간 이상 대기한 후 맞춤 도메인 저장을 다시 시도함. [FACT]
* **[DNS 등록 직후 SSL handshake failed (Error 525) 오류]**
  * 현상: 도메인 연결 직후 HTTPS 접속 시 Cloudflare Error 525(SSL 핸드셰이크 실패) 발생.
  * 원인: 원본 서버(구글 블로그스팟)의 SSL 인증서 발급 및 바인딩이 아직 완료되지 않음.
  * 해결: 블로그스팟 설정에서 'HTTPS 사용 가능 여부'가 '사용 가능'으로 전환될 때까지 대기(약 15분~1시간). Cloudflare 사용 시 초기에는 프록시를 끄고 DNS Only로 설정. [FACT]
* **[Cloudflare 프록시 활성화 시 보안 연결 실패 및 터널 사용 여부]**
  * 현상: Cloudflare에서 프록시(주황색 구름)를 활성화하면 도메인 접속 시 "보안 연결 실패"가 발생하고, 프록시를 비활성화(DNS 전용 회색 구름)하면 정상 연결됨.
  * 원인: 블로그스팟은 구글 자체 CDN 및 무료 SSL을 기본 운용하므로, Cloudflare 프록시 활성화 시 구글의 자체 SSL 인증서 발급/검증이 차단되거나 Cloudflare SSL 암호화 모드와 충돌함.
  * 해결:
    * **Cloudflare Tunnel 사용 불필요:** Cloudflare Tunnel은 개인 로컬/홈서버용 기술이며, 구글 호스팅 SaaS인 블로그스팟에는 적용 불가 및 불필요함. [FACT]
    * **DNS 전용(회색 구름) 유지 (권장):** 블로그스팟 자체가 글로벌 구글 CDN과 무료 SSL을 제공하므로 프록시를 끄고 DNS 전용으로 운용하는 것이 표준 공식 세팅임. [FACT]
    * **프록시 사용 희망 시:** 블로그스팟 [설정] > [HTTPS]에서 'HTTPS 사용 가능' 상태가 완료된 후, Cloudflare SSL/TLS 모드를 'Full(전체)'로 설정해야 함. [FACT]
* **[DNS/블로그스팟 설정 변경 시 전파 지연 (1시간 대기)]**
  * 현상: DNS 레코드를 수정하거나 블로그스팟 맞춤 도메인 설정을 변경한 직후 접속 오류 또는 SSL 에러 발생.
  * 해결: DNS 레코드나 블로그스팟 도메인 설정을 변경한 후에는 구글 글로벌 프록시 인프라 전파 및 SSL 활성화에 **최소 1시간 이상** 소요되므로, 설정을 반복 변경하지 말고 1시간 이상 대기해야 함. [FACT]

---

#### 🧠 Experiences & Tips (경험 및 팁)

* [FACT] Google Blogger 공식 문서에 따르면 DNS 설정 후 활성화까지 최대 1시간 이상 소요될 수 있으므로, DNS 등록 및 블로그스팟 설정 변경 직후 오류가 발생하더라도 설정을 지우지 말고 최소 1시간 이상 기다려야 한다.
* [FACT] 블로그스팟 커스텀 도메인 운용 시 Cloudflare Tunnel은 전혀 사용할 필요가 없으며, 프록시(주황 구름) 대신 'DNS 전용(회색 구름)'으로 두는 것이 구글 SSL 충돌 없이 가장 안정적이다.
* [OPINION] 도메인 연결 직후 HTTPS 인증서가 발급되기 전에는 '안전하지 않은 사이트' 경고가 발생할 수 있으므로, 상태가 '사용 가능'으로 변경된 후 HTTPS 리디렉션을 켜는 것이 안전하다.

---

#### 📋 Feedback History
* **2026-08-14:** 사용자 피드백 접수 1 — Google 공식 고객센터 문서(answer/1233387)를 인용하여 DNS 설정 활성화 대기 시간(최대 1시간 이상) 정보 보강.
* **2026-08-14:** 사용자 피드백 접수 2 — Cloudflare DNS Only 설정 및 블로그스팟 맞춤 도메인 저장 완료 직후 발생하는 '사이트에 연결할 수 없음' 오류 케이스 및 1시간 대기 가이드 보강 완료 (Verified).
* **2026-08-14:** 사용자 피드백 접수 3 — Cloudflare 프록시 활성화 시 보안 연결 실패 원인 분석, Cloudflare Tunnel 적용 불가/불필요 확인, DNS 전용 권장 이유 및 설정 변경 시 1시간 대기 필요성 검증 완료 (Verified).

---

#### 🏷️ Tags

구글 블로그스팟, Blogger, 커스텀도메인, DNS설정, 블로그운영

===== KNOWLEDGE PACKAGE END =====
