---
layout: single
title: "[NLP] English Money With NDFSA"
category: "tech"
tags: NLP

---

### English Money FSA Diagram

- English Money FSA 모델은 아래와 같이 구성했다.

  **M = (Q, Σ, δ, q0, F)**

  ​	**Q** = {q0, q1, q2, q3, q4, q5, q6, q7, q8, q9}

  ​	**Σ** = {one, two, three ..., eleven, twenty, thirty, ..., ninety, cent, cents, dollar, dollars}

  ​	**δ** : Q × Σ → Q

  ​	**q0** = q0

  ​	**F** = {q4, q5, q9}

<img src="https://ironprayer.github.io/assets/images/english_money_diagram.png">



#### Money English 인식 Python 구현

- Alphabet의 [twenty, thirty, ..., ninety]으로부터 상태전이가 2개이므로 기존에 작성해둔 Non-Determic Recognize 사용

  ~~~python
  if __name__ == "__main__" :
      english_money_states = ["q0", "q1", "q2", "q3", "q4", "q5", "q6", "q7", "q8", "q9"]
      english_money_alphabet = ["one", "two", "three", "four", "five", "six", "seven", "eight", "nine", "ten",
                          "eleven", "twelve", "thirteen", "fourteen", "fifteen", "sixteen", "seventeen", "eighteen", "nineteen",
                          "twenty", "thirty", "forty", "fifty", "sixty", "seventy", "eighty", "ninety",
                          "cent", "cents", "dollar", "dollars"]
      english_money_start_state = "q0"
      english_money_final_states = ["q4", "q5", "q9"] 
      english_money_transition_table = [["q3","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q2","q1,q2","q1,q2","q1,q2","q1,q2","q1,q2","q1,q2","q1,q2","q1,q2","0","0","0","0"],
                                      ["q2","q2","q2","q2","q2","q2","q2","q2","q2","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0"],
                                      ["0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","q4","0","q5"],
                                      ["0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","q4","0","q5","0"],
                                      ["0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0"],
                                      ["q8","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q7","q6,q7","q6,q7","q6,q7","q6,q7","q6,q7","q6,q7","q6,q7","q6,q7","0","0","0","0"],
                                      ["q7","q7","q7","q7","q7","q7","q7","q7","q7","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0"],
                                      ["0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","q9","0","0"],
                                      ["0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","q9","0","0","0"],
                                      ["0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0","0"],
                                      ]
      english_money_state_map = {"q0":0, "q1":1, "q2":2, "q3":3, "q4":4, "q5":5, "q6":6, "q7":7, "q8":8, "q9":9}
      english_money_alphabet_map = {"one":0, "two":1, "three":2, "four":3, "five":4, "six":5, "seven":6, "eight":7, "nine":8, "ten":9,
                              "eleven":10, "twelve":11, "thirteen":12, "fourteen":13, "fifteen":14, "sixteen":15, "seventeen":16, "eighteen":17, "nineteen":18,
                              "twenty":19, "thirty":20, "forty":21, "fifty":22, "sixty":23, "seventy":24, "eighty":25, "ninety":26,
                              "cent":27, "cents":28, "dollar":29, "dollars":30}
  
      machine = FSA(english_money_states, english_money_alphabet, english_money_start_state, english_money_final_states, english_money_transition_table, english_money_state_map, english_money_alphabet_map)
  ~~~

  - English Money의 states, alphabet, start_state, final_state, transition_table, state_map, alphabet_map 변수 선언 및 초기화 
  - English Money 관련 변수 사용해 NDFAS 생성

#### Money English 인식 테스트 결과

- 14개의 케이스 테스트 

  - "one dollars", "one dollar", "one cent", "one cents", "twenty one dollar", "twenty one dollars", "sixty dollars", "twenty cents", "twenty cent", "ninety one dollars one cent", "ninety one dollars one cents", "eighty three dollars twenty cent","eighty three dollars twenty cents"

- 테스트 결과

  <img src="https://ironprayer.github.io/assets/images/english_nd_result.png" align="left">

  

