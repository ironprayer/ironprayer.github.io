---

layout: single
title: "[NLP] SheepTalk With DFSA"
category: "tech"
tags: NLP

---

### Dterministic-Finite-State Automata(DFSA)

- 컴퓨터 프로그램과 전자 논리 회로를 설계하는 데에 쓰이는 수학적 모델 (상태기계)
- DFSA Model 은 다음과 같은 5원소 쌍 (quintuple) 으로 정의된다.
- - - **M = (Q, Σ, δ, q0, F)**

    - ​	**Q** 는 내부 상태 (internal state) 들의 유한 집합이다.
      ​	**Σ** 는 문자들의 유한 집합이며, 입력 알파벳 (input alphabet) 으로 불린다.
      ​	**δ** : Q × Σ → Q 는 전체 함수(total function)이며, 전이 함수 (transition function) 라 불린다.
      ​	**q0 ∈ Q** 는 초기 상태 (initial state) 를 의미한다.
      ​	**F ⊆ Q** 는 종료 상태 (final state) 들의 집합을 의미한다

#### DFSA를 통한 Sheeptalk 인식 Python 구현

1. DFSA 클래스 선언 

~~~python
class FSA :
    def __init__(self, states, alphabet, start_state, final_state, transition_table, state_map, alphabet_map)
~~~

- DFSA Model 기본 구성요소 초기화
  - **Q** : states
  - **Σ** : alphabet
  - **δ** : transition_table
  - **q0** : start_state
  - **F** : final_state

- state와 alphabet을 transition_table에 사용할 인덱스로 변환에 사용 요소 초기화
  - state_map : mapping state with index number
  - alphabet_map : mapping alphabet wtih index number

2. Algorithm for deterministic recognition of FSAs 구현

~~~python
def d_recognize(tape, machine:FSA) :
    index = 0
    current_state = machine.start_state
    transition_table = machine.transition_table
    state_number_map = machine.state_number_map
    alphabet_number_map = machine.alphabet_number_map
    accept = "ACCPET"
    reject = "REJECT"

    while True :
        isTapeEnd = (len(tape) <= index)
        isAcceptState = (current_state == machine.final_state)
        if isTapeEnd :
            if isAcceptState :
                return accept
            else :
                return reject
        elif tape[index] not in machine.alphabet or transition_table[state_number_map[current_state]][alphabet_number_map[tape[index]]] == "0" :
            return reject
        else :
            current_state = transition_table[state_number_map[current_state]][alphabet_number_map[tape[index]]]
            index = index + 1
~~~

- input : tape(문자열), machine(상태기계)
- output : ACCEPT, REJECT
- logic 
  - end of tape
    - accept state일 경우 ACCEPT 반환
    - accept state아닌 경우 REJECT 반환
  - transition_table에 없는 alphabet이거나 next_state가 "0"인 경우
    - REJECT 반환
  - 그 이외의 경우
    - transition_table을 통한 state 이동
    - tape index 1 증가 

3. SheepTalk FSA 생성

~~~python
if __name__ == "__main__" :    
  	sheeptalk_states = ["q0","q1","q2","q3"]
    sheeptalk_alphabet = ["a", "b", "!"]
    sheeptalk_start_state = "q0"
    sheeptalk_final_state = "q4"
    sheeptalk_transition_table = [["q1","0","0"],
                                ["0","q2","0"],
                                ["0","q3","0"],
                                ["0","q3","q4"],
                                ["0","0","0"]]
    sheeptalk_state_map = {"q0":0, "q1":1, "q2":2, "q3":3, "q4":4, "fail":5}
    sheeptalk_alphabet_map = {"a":1, "b":0, "!":2}

    machine = FSA(sheeptalk_states, sheeptalk_alphabet, sheeptalk_start_state, sheeptalk_final_state, sheeptalk_transition_table, sheeptalk_state_map, sheeptalk_alphabet_map)
    tape = "baaaaaa!"
    print(d_recognize(tape, machine))
~~~

- Sheeptalk states, alphabet, start_state, final_state, transition_table, state_map, alphabet_map 변수 선언 및 초기화
- Sheeptalk 관련 변수 사용해 DFAS 생성
-  tape와 machine 변수를 d_recognize 입력값으로 사용해 문자 인식
