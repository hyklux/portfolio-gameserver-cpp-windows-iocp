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
- Dispatch(uint32 timeoutMs) 함수로 큐에 쌓여있는 네트워크 입출력 작업을 처리
``` c++
IocpCore::IocpCore()
{
	_iocpHandle = ::CreateIoCompletionPort(INVALID_HANDLE_VALUE, 0, 0, 0);
	ASSERT_CRASH(_iocpHandle != INVALID_HANDLE_VALUE);
}

IocpCore::~IocpCore()
{
	::CloseHandle(_iocpHandle);
}

bool IocpCore::Dispatch(uint32 timeoutMs)
{
	DWORD numOfBytes = 0;
	ULONG_PTR key = 0;	
	IocpEvent* iocpEvent = nullptr;

	//작업 실행 가능한 스레드 확인
	if (::GetQueuedCompletionStatus(_iocpHandle, OUT &numOfBytes, OUT &key, OUT reinterpret_cast<LPOVERLAPPED*>(&iocpEvent), timeoutMs))
	{
		IocpObjectRef iocpObject = iocpEvent->owner;
		iocpObject->Dispatch(iocpEvent, numOfBytes);
	}
	else
	{
		int32 errCode = ::WSAGetLastError();
		switch (errCode)
		{
		case WAIT_TIMEOUT:
			return false;
		default:
			//실행
			IocpObjectRef iocpObject = iocpEvent->owner;
			iocpObject->Dispatch(iocpEvent, numOfBytes);
			break;
		}
	}

	return true;
}
```
### **GameServer.cpp**
- Main Thread에서 DoWorkerJob(ServerServiceRef& service) 함수를 호출하여 서버 핵심 로직 실행
``` c++
void DoWorkerJob(ServerServiceRef& service)
{
	while (true)
	{
		LEndTickCount = ::GetTickCount64() + WORKER_TICK;

		// 네트워크 입출력 처리 -> 인게임 로직까지 (패킷 핸들러에 의해)
		service->GetIocpCore()->Dispatch(10);

		// Job 큐에 예약된 일감 저장
		ThreadManager::DistributeReservedJobs();

		// Job 큐에 쌓인 일감 처리
		ThreadManager::DoGlobalQueueWork();
	}
}
```
### **ThreadManager.cpp**
- 쓰레드를 관리하는 클래스
- 큐에 쌓인 작업을 실행하거나, 아직 큐에 쌓이지 못하고 대기 중인 작업들을 큐에 보관
``` c++
void ThreadManager::Launch(function<void(void)> callback)
{
	LockGuard guard(_lock);

	_threads.push_back(thread([=]()
		{
			InitTLS();
			callback();
			DestroyTLS();
		}));
}

void ThreadManager::DoGlobalQueueWork()
{
	while (true)
	{
		uint64 now = ::GetTickCount64();
		if (now > LEndTickCount)
			break;

		JobQueueRef jobQueue = GGlobalQueue->Pop();
		if (jobQueue == nullptr)
			break;

		jobQueue->Execute();
	}
}

void ThreadManager::DistributeReservedJobs()
{
	const uint64 now = ::GetTickCount64();

	GJobTimer->Distribute(now);
}
```
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
### **ClientPacketHandler.cpp**
- 클라이언트와의 패킷 송수신 처리
- 실제 개별 패킷에 대한 응답처리가 이 클래서에서 이루어진다.


# Job 큐
(캡쳐 필요)
### **JobQueue.cpp**
### **JobTimer.cpp**
### **Job.cpp**


# DB
(캡쳐 필요)
### **DBConnection.cpp**
### **DBConnectionPool.cpp**
### **DBModel.cpp**
### **DBSynchronizer.cpp**
