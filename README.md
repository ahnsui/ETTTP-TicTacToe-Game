## ETTTP TicTacToe Game 프로젝트

##### 23-1 Ewha Womans University 

##### 이형준 교수님 - 정보통신공학

---
### 🖥코드 설명
**1. Server**

    먼저 서버는 0, 1 중에 랜덤으로 숫자를 골라 시작순서를 결정하고 클라이언트에게 인코딩하여 전송한다.
   
    시작 순서 0: 서버가 먼저 시작 
    
    -> 메시지에 'ME'를 포함하여 서버가 먼저 시작한다는 메시지를 전송
   
    시작 순서 1: 클라이언트가 먼저 시작 
    
    -> 메시지에 'YOU'를 포함하여 클라이언트가 먼저 시작한다는 메시지를 전송
   
    클라이언트 소켓으로부터 메시지가 알맞은 ETTTP 형식인지, 
    
    올바른 IP 주소를 포함하였는지 확인받고 ACK 메시지를 수신한 후 디코딩한다.
   
    클라이언트로부터 수신한 ACK 메시지가 ETTTP 형식에 맞는지 check_msg 함수를 사용하여 확인한다.
   
    만약 형식에 맞지 않으면 서버 소켓을 닫고 종료한다.

---

**2. Client**

    클라이언트는 서버가 전송한 start 변수를 받고 first_mover decision 메시지를 받는다.

    서버가 전송한 first_mover decision 메시지가 ETTTP 형식에 맞는 지, 
    
    알맞은 IP 주소를 포함했는지 확인하고 ACK을 보낸다.

    만약 형식에 맞지 않으면 클라이언트 소켓을 닫고 종료한다.
   
---

**3. TicTacToe**

***Get_move()***
    
    상대방이 선택한 위치를 메시지로 받아 ETTTP 형식인지 확인하고 맞다면 ACK을 보내고 위치를 저장한다.

    상대방으로부터 상대방의 new_move 메시지를 받는다.
    
    받은 메시지에서 peer의 new_move 좌표만 얻기 위해 메시지의 특수문자를 공백으로 대체하고 파싱하였다.
    
    파싱한 각각의 정보를 리스트에 저장한 후 이 리스트에서 new_move 좌표를 추출하여 move_line에 저장한다.
    
    move_line의 행과 열좌표를 추출하여 각각 저장하고 move_line을 포함한 ack 메시지를 작성한다.
    
    check_msg 함수를 통하여 ETTTP 형식에 맞는지, IP 주소가 자신의 IP 주소와 같은지 확인하고

    메시지가 유효하지 않으면 소켓을 닫고 종료한다.

    메시지가 유효하면 ACK을 전송하고 loc 변수에 상대방의 좌표를 0~8의 숫자로 저장한다.

***Send_debug()***

    보드를 클릭하지 않고 사용자로부터 ETTTP 메시지를 직접 입력받아서 메시지가 알맞은 형식인지,

    입력받은 좌표가 비어있는지 확인 후 peer에게 자신의 new_move에 관한 ETTTP 메시지를 작성하고 
    
    ack을 받은 후 보드를 업데이트하는 함수이다.

    Gui 창의 텍스트 박스에 사용자가 작성한 메시지를 d_msg에 저장하고 get_move에서와 같이

    d_msg의 특수문자들을 공백으로 대체하고 공백을 기준으로 메시지를 파싱하여 리스트에 저장한다.

    리스트로부터 행과 열좌표를 추출하여 user_move의 위치를 0~8의 숫자로 변환한다.

    그 위치가 이미 채워져 있다면 아무것도 실행하지 않고 리턴한다.

    위치가 비어 있다면 peer에게 메시지를 전송한다.

    peer가 메시지를 확인하고 ack을 보내면 ack이 ETTTP 형식에 맞는지 확인하고 
    
    문제가 없다면 loc에 위치를 저장하고 보드를 업데이트한다.

***Send_move()***

    버튼을 눌렀을 때 peer에게 메시지를 전송하고 peer가 메시지를 확인 후 ack을 보냈을 때 이 ack을 확인한다.

    divmod로 selection의 행과 열을 계산하고 ETTTP 메시지에 선택한 좌표를 포함하여 peer에게 전송한다.

    Peer가 메시지를 받고 확인한 뒤 ack을 보내면 ack이 형식에 맞는지 체크하여 true/false를 반환한다.

   
***Check_result()***
      
    게임이 끝났을 때 server와 client의 결과가 같은 지 확인하여 같으면 True, 틀리면 False를 반환한다.

    Get이 False인 경우, 이 유저는 승자이므로 자신의 결과를 상대방에게 
    
    Winner:ME를 포함하는 메시지 형태로 전송한다.
   
    상대방은 이 메시지를 받은 후 check_msg 함수를 통해 ETTTP형식이 맞는지, 
    
    자신의 IP주소와 같은 지 확인하고 맞다면 결과를 비교한다.

    만약 맞지 않다면 False를 반환한다.

    결과를 비교할 때 받은 메시지에서 winner만 얻기 위해 
    
    메시지의 특수문자를 공백으로 대체하고 파싱하였다.

    파싱한 정보를 리스트에 저장한 후 winner를 추출하여 peer_result에 저장한다.

    Peer_result와 결과가 같다면 자신의 결과를 상대방에게 소켓을 통해 
    
    Winner: YOU를 포함하는 메시지 형태로 전송한다.

    이 메시지를 받고 check_msg 함수를 통해 ETTTP형식이 맞는지, 
    
    자신의 IP주소와 같은 지 확인하고 맞다면 결과를 비교한다.

    만약 맞지 않다면 False를 반환한다.

    앞 경우와 마찬가지로 Peer_result에 저장하여 결과가 같은 지 확인한다.

    결과가 같다면 True를 반환하고 틀리면 False를 반환한다.

***Check_msg()***

    변수로 받은 msg에서 통신 프로토콜과 IP주소를 추출하여 올바른 ETTTP 형식인지,

    추출한 IP주소가 변수로 받은 recv_ip와 같은 지 확인 후 True/False를 반환한다.
 
    받은 메시지에서 통신 프로토콜과 IP주소만 얻기 위해 
    
    메시지의 특수문자를 공백으로 대체하고 파싱하였다.

    파싱한 정보를 리스트에 저장한 후 통신 프로토콜과 IP주소를 추출하여 versions와 ip_line에 저장한다.

    프로토콜이 ETTTP형식이 맞는 지, ip_line이 변수로 받은 recv_ip와 같은 지 확인 후 True/False를 반환한다.


    
