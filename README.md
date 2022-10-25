# portfolio-gameserver-cpp-windows-iocp
C++ 게임서버 포트폴리오(Windows IOCP Server)

# 소개
C++ 게임서버 포트폴리오입니다.


IOCP 기반의 서버로 여러 클라이언트 세션이 접속해 게임룸에서 패킷을 송수신하는 기능까지 구현된 프로그램입니다.


(실제 인게임 로직은 향후  예정입니다)


# 기능
:heavy_check_mark: IOCP 코어


:heavy_check_mark: 서버 서비스


:heavy_check_mark: 게임 세션 관리


:heavy_check_mark: 패킷 처리


:heavy_check_mark: Job 큐


:heavy_check_mark: DB


# IOCP 코어
(캡쳐 필요)
### **IocpCore.cpp**
- CompletionPort를 생성하고 제어하는 코어 클래스
- Dispatch(uint32 timeoutMs) 함수로 네트워크 입출력 처리
### **GameServer.cpp**
- Main Thread에서 DoWorderJob(ServerServiceRef& service) 호출하여 서버 핵심 로직 실행
``` c++
void DoWorkerJob(ServerServiceRef& service)
{
	while (true)
	{
		LEndTickCount = ::GetTickCount64() + WORKER_TICK;

		// 네트워크 입출력 처리 -> 인게임 로직까지 (패킷 핸들러에 의해)
		service->GetIocpCore()->Dispatch(10);

		// 예약된 일감 처리
		ThreadManager::DistributeReservedJobs();

		// 글로벌 큐
		ThreadManager::DoGlobalQueueWork();
	}
}
```
### **IocpEvent.cpp**
### **ThreadManager.cpp**


# 서버 서비스
(캡쳐 필요)
### **Service.cpp**
- 리스너 소켓을 생성하여 클라이언트 접속 요청을 받는다.
- 클라이언트 접속 요청 시 클라이언트 세션 객체를 생성하고 접속 해제 시 까지 관리한다.
### **Listener.cpp**
- 클라이언트로부터의 요청을 수신하기 위한 리스너 소켓을 생성 및 관리하는 클래스
### **Session.cpp**
- 세션 클래스
### **SocketUtils.cpp**


# 게임 세션 관리
(캡쳐 필요)
게임세션(=게임룸)에 대한 관리
### **GameSessionManager.cpp**
### **GameSession.cpp**


# 패킷 처리
(캡쳐 필요)
### **ClientPacketHandler.cs**
- 클라이언트와의 패킷 송수신 처리
- 실제 개별 패킷에 대한 응답처리가 이 클래서에서 이루어진다.


# Job 큐
(캡쳐 필요)
### **JobQueue.cs**
### **JobTimer.cs**
### **Job.cs**


# DB
(캡쳐 필요)
### **DBConnection.cs**
### **DBConnectionPool.cs**
### **DBModel.cs**
### **DBSynchronizer.cs**
