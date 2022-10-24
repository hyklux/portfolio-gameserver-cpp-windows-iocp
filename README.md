# portfolio-gameserver-cpp-windows-iocp
C++ 게임서버 포트폴리오(Windows IOCP Server)

# 소개
C++ 게임서버 포트폴리오입니다.


IOCP 기반의 메인서버를 실행하고, 여러 클라이언트 세션이 접속해 게임룸에서 패킷을 주고 받는 코어 시스템만 구현된 프로그램입니다.
(실제 인게임 로직은 추가 예정입니다)


# 기능
:heavy_check_mark: IOCPCore


:heavy_check_mark: ServerService


:heavy_check_mark: SessionManager


:heavy_check_mark: PacketHandler


:heavy_check_mark: JobQueue


:heavy_check_mark: DB

# IOCPCore
### **IocpCore.cs**
### **IocpEvent.cs**
### **SocketUtils.cs**
### **ThreadManager.cs**
### **Listener.cs**


# ServerService
### **IocpCore.cs**
### **IocpEvent.cs**
### **SocketUtils.cs**
### **ThreadManager.cs**


# SessionManager
### **GameSessionManager.cs**
### **IocpEvent.cs**
### **SocketUtils.cs**
### **ThreadManager.cs**


# PacketHandler
### **ClientPacketHandler.cs**
### **IocpEvent.cs**
### **SocketUtils.cs**
### **ThreadManager.cs**


# JobQueue
### **JobQueue.cs**
### **JobTimer.cs**
### **Job.cs**
### **ThreadManager.cs**



# DB
### **DBConnection.cs**
### **DBConnectionPool.cs**
### **DBModel.cs**
### **DBSynchronizer.cs**
