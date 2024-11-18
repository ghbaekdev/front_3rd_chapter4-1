## 프론트엔드 배포 파이프라인

<!-- 1. 기본적인 마크다운 이미지 문법 -->

![WorkFlow](/public/workflow.png)

### GitHub Actions 배포 워크플로우

GitHub Actions를 통해 다음과 같은 자동화된 배포 프로세스를 구현합니다:

1. **저장소 체크아웃**

   - GitHub Actions runner에 프로젝트 소스 코드를 가져옵니다.

2. **의존성 설치**

   - `npm ci` 명령어를 통해 프로젝트에 필요한 패키지들을 설치합니다.

3. **프로젝트 빌드**

   - Next.js 프로젝트를 빌드하여 배포 가능한 정적 파일들을 생성합니다.

4. **AWS 자격 증명 구성**

   - AWS CLI를 사용하기 위한 인증 정보를 설정합니다.
   - GitHub Secrets에 저장된 AWS 접근 키를 활용합니다.

5. **S3 버킷 동기화**

   - 빌드된 정적 파일들을 AWS S3 버킷에 업로드합니다.
   - 기존 파일들과 동기화하여 최신 상태를 유지합니다.

6. **CloudFront 캐시 무효화**
   - 배포된 콘텐츠의 즉각적인 반영을 위해 CloudFront의 캐시를 초기화합니다.

### 관련 링크

- 🔗 [배포된 S3 Bucket](http://hanghae3-2.s3-website-us-east-1.amazonaws.com)
- 🏗️ [Cloud Front 배포 URL](http://d1v0z4rxx6cnh9.cloudfront.net)

### 주요 개념

- **S3**: 정적 웹 페이지를 저장하고 제공하는 스토리지 서비스
- **CloudFront**: 정적 콘텐츠를 제공하는 콘텐츠 전송 네트워크
- **GitHub Actions**: 소스 코드 저장소에서 자동화된 빌드, 테스트 및 배포 작업을 수행하는 플랫폼

## 기술 스택 주요 설명

### GitHub Actions과 CI/CD 도구

GitHub Actions는 저희 프로젝트의 자동화된 배포 파이프라인을 구축하는 데 사용됩니다. main 브랜치에 코드가 push될 때마다 자동으로 빌드 및 배포 프로세스가 시작됩니다.

- 자동화된 빌드 및 테스트 실행
- AWS S3 업로드 및 CloudFront 캐시 무효화 자동화
- GitHub 저장소와 완벽한 통합

### S3와 스토리지

Amazon S3는 정적 웹 사이트 호스팅을 위한 스토리지로 활용됩니다. 빌드된 Next.js 프로젝트의 정적 파일들이 저장되는 공간입니다.

- 정적 웹사이트 호스팅 기능 활성화
- 높은 가용성과 내구성 보장
- 비용 효율적인 스토리지 솔루션

### CloudFront와 CDN

CloudFront는 전세계 사용자에게 빠른 콘텐츠 전송을 제공하는 CDN 서비스로 활용됩니다.

- 전역 엣지 로케이션을 통한 빠른 콘텐츠 전송
- HTTPS 보안 통신 지원
- 사용자 지역 기반 최적화된 라우팅 및 트래픽 부하 분산 유도

### 캐시 무효화(Cache Invalidation)

CloudFront의 캐시 무효화는 새로운 콘텐츠가 배포될 때 즉시 반영되도록 하는 중요한 프로세스입니다.

- 배포 시 자동 캐시 무효화 실행
- 실시간 콘텐츠 업데이트 보장
- 효율적인 캐시 관리 전략

### Repository Secret과 환경변수

Secret Keys들은 GitHub Repository Secrets를 통해 안전하게 관리됩니다.

![github_secrets](/public/github_secrets.png)

### 환경 변수

| 변수명                         | 설명                                   |
| ------------------------------ | -------------------------------------- |
| **AWS_ACCESS_KEY_ID**          | IAM 계정 생성시 발급받은 엑세스키      |
| **AWS_SECRET_ACCESS_KEY**      | IAM 계정 생성시 발급받은 비밀 엑세스키 |
| **AWS_REGION**                 | 버킷 생성시 설정한 리전                |
| **S3_BUCKET_NAME**             | 버킷 이름                              |
| **CLOUDFRONT_DISTRIBUTION_ID** | CloudFront 배포 ID                     |

<br />

---

<br />

## CDN를 사용한 성능 분석

### [S3 Bucket](http://hanghae3-2.s3-website-us-east-1.amazonaws.com) 정적 파일 배포

![S3 Bucket](/public/bucket_network.png)

<br />

---

<br />

### [Cloud Front](http://d1v0z4rxx6cnh9.cloudfront.net)를 활용한 CDN 배포

![Cloud Front](/public/cf_network.png)

## <br />

<br />

### 1-1. 네트워크 요청 시간 비교 (Chrome DevTools)

| 요소                  | 설명   |
| --------------------- | ------ |
| HTML 파일 요청 시간   | 0.59ms |
| CSS 파일 요청 시간    | 0.15ms |
| JS 파일 요청 시간     | 0.15ms |
| 이미지 파일 요청 시간 | 0.15ms |
