<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo-small.svg" width="120" alt="Nest Logo" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

  <p align="center">A progressive <a href="http://nodejs.org" target="_blank">Node.js</a> framework for building efficient and scalable server-side applications.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://opencollective.com/nest#backer" target="_blank"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor" target="_blank"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
  <a href="https://paypal.me/kamilmysliwiec" target="_blank"><img src="https://img.shields.io/badge/Donate-PayPal-ff3f59.svg" alt="Donate us"/></a>
    <a href="https://opencollective.com/nest#sponsor"  target="_blank"><img src="https://img.shields.io/badge/Support%20us-Open%20Collective-41B883.svg" alt="Support us"></a>
  <a href="https://twitter.com/nestframework" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow" alt="Follow us on Twitter"></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

# IDS Web Backend

## 프로젝트 개요

해충 관리 시스템의 백엔드 API 서버입니다. NestJS 프레임워크를 기반으로 구축되었으며, JWT 인증, PostgreSQL 데이터베이스, Prisma ORM을 사용합니다.

## 주요 기능

- **사용자 인증**: JWT 기반 로그인/회원가입 시스템
- **역할 기반 접근 제어**: 곤충 함정 구매자, 끈끈이 구매자, 관리자 역할
- **QR코드 시스템**: 포인트 충전을 위한 QR코드 생성 및 사용
- **포인트 관리**: 구매자 포인트 시스템 및 거래 기록
- **포충기 라이센스 관리**:
  - Auto Increment ID 및 UUID 이름 기반 라이센스 생성 및 관리
  - 탐지 모델, 만료 기간, 일일 최대 탐지 횟수 설정
  - 포충기 판매자의 라벨 시스템
  - 위치 정보 (지리 좌표 및 상세 주소) 관리

## QR코드 사용 방법

### 1. QR코드 생성 (백엔드)

```bash
POST /qr-codes
{
  "points": 150
}
```

### 2. QR코드 이미지 생성 (프론트엔드)

백엔드에서 받은 `serial_number`를 사용하여 프론트엔드에서 QR코드 이미지를 생성합니다:

```javascript
// QR코드 데이터 형식
const qrCodeData = {
  serial_number: 'QR20250709198584',
  url: `https://yourapp.com/qr/use/${serial_number}`,
};

// QR코드 라이브러리 사용 예시 (qrcode.js)
import QRCode from 'qrcode';

const generateQRCode = async (serialNumber) => {
  const qrData = `https://yourapp.com/qr/use/${serialNumber}`;
  const qrCodeImageUrl = await QRCode.toDataURL(qrData);
  return qrCodeImageUrl;
};
```

### 3. QR코드 사용 (포인트 충전)

끈끈이 구매자만 QR코드를 스캔하여 포인트를 충전할 수 있습니다:

```bash
POST /qr-codes/use/:serialNumber
```

**응답 예시:**

```json
{
  "message": "150 포인트가 충전되었습니다.",
  "points": 150,
  "balance": 1500
}
```

### 4. 권한 제한

- **QR코드 생성**: 모든 인증된 사용자
- **QR코드 사용**: 끈끈이 구매자(`sticky_trap_buyer`) 역할만 가능
- **QR코드 삭제**: 생성자만 가능 (issued 상태만)

## 프로젝트 설정

### 환경 변수 설정

```bash
cp env.example .env
```

필요한 환경 변수를 `.env` 파일에 설정하세요.

### 의존성 설치

```bash
npm install
```

### 데이터베이스 설정

```bash
npx prisma migrate dev
npx prisma generate
```

## 애플리케이션 실행

```bash
# 개발 모드
npm run start:dev

# 프로덕션 모드
npm run start:prod
```

## 테스트

```bash
# 단위 테스트
npm run test

# E2E 테스트
npm run test:e2e

# 테스트 커버리지
npm run test:cov
```

## API 문서

애플리케이션 실행 후 다음 URL에서 Swagger API 문서를 확인할 수 있습니다:

- 로컬: `http://localhost:3000/api`

## 기술 스택

- **프레임워크**: NestJS
- **언어**: TypeScript
- **데이터베이스**: PostgreSQL
- **ORM**: Prisma
- **인증**: JWT
- **문서화**: Swagger/OpenAPI
- **테스트**: Jest

## 라이센스

MIT 라이센스
