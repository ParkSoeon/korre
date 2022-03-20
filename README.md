# KORean Relation Extraction (korre)
한국어 문장 내에 존재하는 개체 사이의 관계를 추출하는 모듈입니다.

## Requirements

## Quick Start
먼저 `korre` 패키지를 다음과 같이 import 합니다.
```python
>>> from korre import KorRE
```

패키지를 import 한 후에는, 다음과 같이 관계 추출을 수행할 수 있는 객체를 만들어줍니다. 

객체가 만들어지면서, 학습된 모델을 자동으로 로드합니다.
```python
>>> korre = KorRE()
```

## Usage
### 1. Named Entity Recognition
문장 내에 내포되어 있는 관계를 추출하기 위해 개체명 인식이 필요합니다. 

한국어 개체명 인식 모듈로는 `pororo`(https://github.com/kakaobrain/pororo) 모듈을 그대로 사용하였고 `korre`에서는 다음과 같이 사용 가능합니다.
```python
>>> korre.pororo_ner('갤럭시 플립2는 삼성에서 만든 스마트폰이다.')
[('갤럭시 플립2', 'ARTIFACT'),
 ('는', 'O'),
 (' ', 'O'),
 ('삼성', 'ORGANIZATION'),
 ('에서', 'O'),
 (' ', 'O'),
 ('만든', 'O'),
 (' ', 'O'),
 ('스마트폰', 'TERM'),
 ('이다.', 'O')]
```
`pororo` 모듈을 통해 개체명 인식을 수행한 후 관계를 추출하기 위한 개체를 추출하여 다음과 같이 문장에서의 인덱스를 함께 나타낼 수 있습니다.

이를 통해 관계를 추출하고자 하는 개체의 인덱스를 입력에 사용하여 관계 추출에 사용하게 됩니다.
```python
>>> korre.ner('갤럭시 플립2는 삼성에서 만든 스마트폰이다.')
[('갤럭시 플립2', 'ARTIFACT', [0, 7]),
 ('삼성', 'ORGANIZATION', [9, 11]),
 ('스마트폰', 'TERM', [17, 21])]
```

### 2. Inference (Relation Extraction)
`korre` 모듈을 통해서는 다음의 3가지 형태로 관계 추출을 수행할 수 있습니다.

1. 사용자가 입력한 문장에 관계를 알고자 하는 개체 쌍의 앞뒤에 직접 **entity marker token**을 붙인 경우
2. 문장과 관계를 알고자 하는 두 개체의 위치 인덱스를 직접 입력하는 경우
3. 문장만 입력하여 내포되어 있는 모든 관계를 알고자 하는 경우

#### 1. **entity marker token**이 존재하는 문장이 입력된 경우
- 입력 예시
```python
>>> korre = KorRE()
>>> korre.infer('[E1] 갤럭시 플립2 [/E1] 는 [E2] 삼성 [/E2] 에서 만든 스마트폰이다.', entity_markers_included=True)
```
- 출력 예시
```python
[('갤럭시 플립2', '삼성', '해당 개체의 제조사(manufacturer)')]
```