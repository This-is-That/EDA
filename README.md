# Why data collection and database construction
Why data collection and database construction

## 데이터 수집
포스터의 저작권을 위해 공연 정보와 포스터 이미지가 필요했기 때문에 KOPIS의 OPEN API를 사용해 최근 11년간의 공연 정보를 수집했습니다.
공연 포스터 이미지 저장을 위한 URL은 XML 데이터로 존재하여 XML파싱을 통해 데이터 추출과 수집을 진행했습니다. 

## 데이터 전처리
추출된 데이터에서 공연 제목과 KOPIS 내부에서 지정한 공연 ID를 가장 중요하게 생각했습니다. 
해당 컬럼은 추후 데이터베이스 구축까지 이어졌기 때문에 중복 제거는 필연적입니다.
파이썬에서는 중복으로 인식하지 못하는 글꼴, 특수문자들이 MySQL에서는 중복으로 인식하여 해당 공연 제목을 가진 데이터는 제거했습니다. 
중복이 제거된 데이터의 URL을 통해 이미지를 추출하고 약 6만장의 공연 포스터 이미지를 확보했습니다.

**Post-Co**의 가장 중요한 포인트는 이미지의 특징벡터를 바탕으로 코사인 값을 계산하여 FAISS ANN을 활용해 코사인 유사도를 비교하는 것입니다. 
이를 위해 6만장의 이미지를 **CLIP**모델을 활용해 특징벡터를 추출했습니다. 

## 데이터베이스 구축
**Post-Co**에서 사용자들에게 제공하는 서비스는 포스터 이미지의 유사도 뿐만 아니라 해당 공연의 정보를 포함합니다. 
따라서 제공해야할 정보들은 공연 포스터 이미지, 공연 제목 등을 포함한 공연 정보와 특징벡터로 추출된 유사도입니다. 

데이터베이스의 테이블은 3개로 이루어져있습니다. 
- 공연 ID, 공연 제목과 URL 컬럼으로 이루어진 main 테이블
- 공연 ID, 공연 제목과 특징 벡터로 이루어진 feature 테이블
- 공연 정보들로 이루어진 info 테이블

각 테이블은 공연 ID를 기본키로 하며, main 테이블의 공연 ID를 외래키로 참조하고 있습니다.
이는 데이터베이스에서 데이터 무결성을 유지하고, 테이블 간의 관계를 명확하게 정의하기 위해서입니다.

