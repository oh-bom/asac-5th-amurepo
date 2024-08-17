# 무신사 판매량 예측 분류 모델 

## 1. 데이터 수집 및 전처리
### 수집 데이터
- 상품 정보: 제품 번호, 제품명, 총 조회수, 총 구매수,  원가 및 할인가, 사이즈 개수, 스타일샷과 상품의 메인 이미지 <br>
- 상품 리뷰:제품 구매 성별과 리뷰의 별점, 핵심 키워드, 본문 텍스트, 포토 리뷰 이미지 <br>
- 브랜드 정보: 브랜드 카테고리, 제품별 색상 정보 <br>
<img width="515" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/266bad6d-0aa1-43cb-9074-ca9af8b10f38">

전체 데이터는 다음과 같습니다<br>
<img width="559" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/fa3344fa-1026-4fc7-bd50-603a898c37ea">

### 전처리<br>
   **이미지** <br>
   -  사진에 사람이 있으면 1, 없으면 0으로 값을 추출
   -  사진 배경 제거, 셔츠 인식, 이미지 색상 픽셀 클러스터링 
   -  주요 색상이 전체에서 차지하는 비율 확인 -> 주요 색상 r,g,b 추출
   -  r,g,b 값 -> hex 코드 변환 -> 10진수 변환<br>

  <br> &nbsp;단색 셔츠인 경우
  - 주요 색상이 전체에서 차지하는 비율 0.5 이상인 경우 -> r,g,b color 1가지 추출
   <img width="400" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/87964d94-5579-4d9b-aedf-56dc00544a2e">

  <br> &nbsp;체크 셔츠인 경우
  - 주요 색상이 전체에서 차지하는 비율 0.5 이하인 경우 -> r,g,b color 2가지 추출
   <img width="400" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/825029f2-5559-4da1-9077-c64e2eb9efec">
 
 **리뷰 및 태그**

- 중복 데이터 확인 및 삭제, pykospacing 띄어쓰기 교정
- 단어사전 등재되어있지 않는 단어 추가 및 불용어 처리, 다중공백 삭제, 숫자 및 기호 제거
- okt 라이브러리를 이용하여 명사 추출
- 명사 추출 빈도수 top3를 뽑아 대표 단어 선정 및 TF-IDF score 합 벡터화

 **브랜드별 데이터**
 - 제품별 조회수, 리뷰수
 - 성별에 따른 조회수 및 구매수
 - 라벨 인코딩으로 변환

### 최종 사용 변수
<img width="400" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/64e7da9c-4fa1-485f-8d22-04bba478a516">
<img width="385" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/0745e871-00e7-468f-ba6f-a9530ae8c972">


## 2. 분석
- 변수 간 상관관계 : 브랜드 관련 변수가 상관성이 높음
  <img width="291" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/6120f896-e527-4881-8bae-0a6ee096fd10">
- 판매량 등급화
  <img width="601" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/5d6d91fe-0cf1-4a51-8190-247a5e33c7f8">
  n-분위수 등급화 문제점: 전체 데이터중 1000개 이하 판매량에 집중되어 있음-> 1000개 이하 구간만 세분화된 등급이 부여됨
  <img width="700" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/896bb2e7-a66e-4864-9c11-6c3734e01787">
   k-means 클러스터링을 이용: 50~10000개의 판매량에 대하여 6등급으로 나눈 후, 10000개 이상의 판매량은 최상 등급인 0등급, 
      50개 미만의 판매량에 대해서는 최하 7등급으로 정의하여 2개의 등급 추가-> 총 8개의 등급으로 분류


## 3. 모델링
- (+) 성능 향상을 위해 브랜드 관련 통계값 처리를 한 파생 변수 추가
- (-) 가장 하위 중요도가 나온 6개의 변수 제거
- Random Forest, XGBoost, LightGBM, CatBoost
  <img width="619" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/a364d69d-1627-4908-b9de-73f167647d4c">

## 4. 결론
- 가장 성능이 높은 모델: LGBM, accuracy 0.639
- 주요 변수 요인: 컬러, 제목, 리뷰수, 가격
 <img width="316" alt="image" src="https://github.com/oh-bom/asac-5th-amurepo/assets/108965609/9c185309-1c9f-45f2-b1b8-5c6945b524bb">





