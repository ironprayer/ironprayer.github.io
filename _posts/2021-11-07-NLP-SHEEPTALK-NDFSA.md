---

layout: single
title: "[NLP] SheepTalk With NDFSA"
category: "tech"
tags: NLP

---

### Non-Dterministic-Finite-State Automata(NDFSA)

- 컴퓨터 프로그램과 전자 논리 회로를 설계하는 데에 쓰이는 수학적 모델 (상태기계)

- NDFSA Model 은 다음과 같은 5원소 쌍 (quintuple) 으로 정의된다.

  ​		**M = (Q, Σ, δ, q0, F)**

  ​			**Q** 는 내부 상태 (internal state) 들의 유한 집합이다.

  ​			**Σ** 는 문자들의 유한 집합이며, 입력 알파벳 (input alphabet) 으로 불린다.

  ​			**δ** : Q × Σ → Q 는 전체 함수(total function)이며, 전이 함수 (transition function) 라 불린다.

  ​			**q0 ∈ Q** 는 초기 상태 (initial state) 를 의미한다.

  ​			**F ⊆ Q** 는 종료 상태 (final state) 들의 집합을 의미한다

- DFSA와 동치 관계에 있다.

-  ε-transition 존재합니다.

-  input에 따라 전이되는 상태가 2개 이상일 수 있습니다. (q1 -> q1, q2)

#### NDFSA를 통한 Sheeptalk 인식 Python 구현

1. FSA 클래스 선언 

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

2. Algorithm for non deterministic recognition of FSAs 구현

   2-1. nd_recognize 함수 

   - nd_recognize는 DFS 또는 BFS로 구현될 수 있다.
   - 해당 구현에서는 queue 기반으로 한 BFS로 구현했다.
   - agenda는 tape에 따라 이동할 수 있는 모든 경로를 (state, index) 튜플 형식으로 담은 리스트이다.
   - reject 시점은 더 이상 진행할 수 없는 상황, agneda가 비어있을 때이다.  

~~~python
def nd_recognize(tape, machine:FSA) :
    agenda = [(machine.start_state, 0)]
    current_state = next(agenda)
    tape_end = len(tape)
    accept_state = machine.final_state
    accept = "ACCEPT"
    reject = "REJECT"

    while True :
        if isAcceptState(current_state, tape_end, accept_state) :
            return accept
        else :
            agenda = agenda + generateNewStates(current_state, machine, tape)

        isAgendaEmpty = ( len(agenda) == 0 )
        if isAgendaEmpty :
            return reject
        else :
            current_state = next(agenda)
~~~

- input : tape(문자열), machine(상태기계)

- output : ACCEPT, REJECT

- logic 
  - isAcceptState 함수가 true인 경우
    - ACCEPT 반환
    
  - 그 이외의 경우

    - agenda에 새로운 경로 탐색해서 추가

    ---

  - isAgendaEmpty 가 true인 경우
    - REJECT 반환
    
  - 그 이외의 경우

    - 현재 state에 agenda 첫 번째 원소 할당

  

  2-2. isAcceptState 함수

  - Accept 상태인지 확인하는 함수

~~~python
def isAcceptState(current_state, tape_end, accept_state) :
    current_node = current_state[0]
    index = current_state[1]

    if index == tape_end and current_node == accept_state :
        return True
    else :
        return False 
~~~

  - input : current_state(현재 상태), tape_end(입력 문자열 길이), accept_state(FSA의 final_state)

  - ouput : True, False

  - logic 

      - 조건1(입력문자열 마지막 문자)과 조건2(현재 상태과 수락 상태)이 참일 경우

        		-	True 반환

      - 그 이외의 경우

          - False 반환 

            

    2-3. generateNewStates 함수

    -	진행 가능한 모든 상태 리스트를 찾아서 반환해주는 함수

~~~python
def generateNewStates(current_state, machine:FSA, tape) :
    current_node = current_state[0]
    index = current_state[1]
    new_states = getTransitionAgendaList(machine, current_node, "e", index) + getTransitionAgendaList(machine, current_node, tape[index], index + 1)
    return new_states
~~~

- input : current_stae(현재 상태), machine(상태 기계), tape(입력 문자열)

- ouput : new_states(이동 가능한 상태 리스트)

  

  2-4. getTransitionAgendaList 함수

  - 현재 상태와 입력 문자를 기반으로 한 이동 가능한 모든 상태 찾아서 반환 

~~~python
def getTransitionAgendaList(machine:FSA, current_node, current_tape, index) :
    transition_table = machine.transition_table
    state_number_map = machine.state_number_map
    alphabet_number_map = machine.alphabet_number_map
    results = []
    next_node = "0"

    if current_tape in alphabet_number_map :
        next_node = transition_table[state_number_map[current_node]][alphabet_number_map[current_tape]]

    if next_node == "0" : return results

    if "," in next_node :
        nodes = next_node.split(",")
        for node in nodes :
            results.append((node, index))
    else :
        results.append((next_node, index))

    return results 
~~~

- input : machine(상태 기계), current_node(현재 상태), current_tape(현재 문자열), index(현재 문자열이 가리키고 있는 인덱스)

- output : results(이동 가능한 상태 리스트)

- logic 

  - 이동 가능한 상태가 "0"인 경우

    - 빈 리스트 반환

  - 이동 가능한 상태 값이 2개인 경우

    - 각 상태 값을 (state, index) 형태로 리스트에 입력

  - 그 이외의 경우

    - 이동 가능한 상태를 (state, index) 형태로 리스트에 입력

    

  2-5. next 함수

  	- agenda에 담긴 다음 상태를 반환해주는 함수

~~~python
def next(agenda) :
    next_state = agenda.pop(0)
    return next_state
~~~

- input : agenda(모든 이동 가능한 상태 리스트)
- ouput : next_state(다음 상태)



3. SheepTalk FSA 생성

~~~python
if __name__ == "__main__" :    
    sheeptalk_states = ["q0","q1","q2","q3"]
    sheeptalk_alphabet = ["a", "b", "!", "e"]
    sheeptalk_start_state = "q0"
    sheeptalk_final_state = "q4"
    sheeptalk_transition_table = [["q1","0","0","0"],
                                ["0","q2","0","0"],
                                ["0","q2,q3","0","0"],
                                ["0","0","q4","0"],
                                ["0","0","0","0"]]
    sheeptalk_state_map = {"q0":0, "q1":1, "q2":2, "q3":3, "q4":4, "fail":5}
    sheeptalk_alphabet_map = {"a":1, "b":0, "!":2, "e":3}
    
    machine = FSA(sheeptalk_states, sheeptalk_alphabet, sheeptalk_start_state, sheeptalk_final_state, sheeptalk_transition_table, sheeptalk_state_map, sheeptalk_alphabet_map)
    tape = "baaaaac!"
    result = nd_recognize(tape, machine)
    print(result)
~~~

- Sheeptalk states, alphabet, start_state, final_state, transition_table, state_map, alphabet_map 변수 선언 및 초기화
- 기존 DFSA Sheeptalk 관련 변수에 ε-transition 값들 추가 
- Sheeptalk 관련 변수 사용해 NDFAS 생성
-  tape와 machine 변수를 nd_recognize 입력값으로 사용해 문자 인식
