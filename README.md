## 소개
- monorepo 및 turborepo 학습용 repository

## 학습 포인트 !
### monorepo의 개념  

- 2개 이상의 프로그램 코드들이 동일 저장소에서 관리됨
  - 통합된 버전, 의존성 관리
  - 공통 규칙 적용으로 인한 일관성 유지 용이
  - 디자인 시스템, 공통 util 함수들 등을 다양한 프로젝트에서 관리한다면 모노레포 구성이 좀더 적합하지 않을까 ?
  - 추가적으로 우리 회사 같은 개발자 인원수가 적거나, 목적조직이 아닌 기능조직으로 운영되는 개발팀인 경우에도 각 프로그램들의 유지보수를 위해서는 더 적합할 것 같음
    
- 참고
  - 모노리스(monolith): 하나의 어플리케이션 내에 여러 기능들의 코드들이 모듈화되지 않은 채로 관리되고 있는 구조
    - 프로젝트의 규모가 점점 커지면 관리하기 힘듦
    - 즉각적인 버그 픽스 등이 용이 
  - 멀티레포(multirepo): 각 프로젝트별로 각각의 저장소에서 관리됨
    - 버전 통일이 어려움
    - 다른 저장소의 히스토리에 대해 알기 어려움
    - 하지만 각 저장소에 대한 배포가 빠르고 책임소재가 분명함  

#### monorepo로 전환하기 위해선 어떻게 해야 할까 
- apps, packages 등 각각의 목적에 따라 각각의 폴더로 이동시키기
- monorepo의 관리는 root에서 관리를 해준다
- root의 workspace에서 관리해줄 폴더들의 위치를 명시해주며, 각 프로젝트를 모듈화 할 수 있도록 해준다
- packages에는 공통으로 사용되는 규칙들(prettier, eslint 등의 규칙들)이 관리되기 때문에 index.js를 사용해서 modules.exports를 사용하여 모듈화 작업을 진행한다.
- turbo를 설치하여 모노레포 관리 라이브러리로서 turborepo를 설정한다.

### turborepo

#### 왜 turborepo 하는가 
- 빌드 최적화(캐싱 > FULL TURBO)
- 빌드의 병렬처리
- 시각화 기능 제공

#### turborepo의 원리
- turbo build 실행
- 각 패키지 하단의 .turbo 폴더 내부의 로그 파일 생성 (빌드에 대한 hash 저장)
- node_modules > .cache > turbo 에 hash값이 일치하는 폴더와 매칭됨 (캐싱작업)
- 로컬캐싱 뿐만 아니라 vercel 계정이 있다면 원격 캐싱도 가능

#### turbo.json과 package.json
- tasks
  - 이전의 pipelines에 해당됨
  - turbo 명령어들에 대해 정의함
    - dependsOn: ["^build"] 각 패키지의 명령어들을 그대로 실행함
    - outputs: 해당 디렉토리가 존재하는 경우에는 명령어가 실행되지 않고 캐시된 결과물들을 사용함
    - inputs: 해당 작업을 실행할때 대상이 되는 파일들의 리스트

### 추가적으로 더 발전시켜볼 수 있는 것
#### turborepo vs yarn berry && pnp vs lerna ?
- yarn berry
  - yarn v1은 classic (레거시 코드)
    - yarn v1의 의존성 탐색 알고리즘의 비효율
    - node_modules의 저장공간이 너무 큼
    - 유령의존성 (실제로 설치되지는 않았지만, require(패키지)가 작동함)
  - .yarn/cache에 패키지의 압축파일을 "수평적으로" 저장함 = pnp(plug n play)
    - 빠른 의존성 검색, 빠른 설치, 유령 의존성 방지
    - yarn/cache를 하나의 git으로 관리한다면 설치과정을 "제거"할 수 있는데 이를 zero-install이라고 함
    - 오프라인 캐시
- lerna
  -  ![image](https://github.com/okcorn/turborepo-test/assets/81691954/01802089-7609-4a30-b846-3ec02b2b1954)
  - yarn, npm보다 더 고수준의 레이어
  - yarn, npm만으로도 workspaces를 관리할 수 있지만 각각의 명령어를 입력해야 하는 반면, lerna는 이를 최적화 시켜줌
#### npm > pnpm으로 migration ?

### 참고
- [각 패키지별 비교](https://d2.naver.com/helloworld/7553804)
- [turborepo 실습 사이트](https://www.pixelmatters.com/blog/how-to-manage-multiple-front-end-projects-with-a-monorepo)를 참고하여 단계별로 따라 만듦
