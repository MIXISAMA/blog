---
title: 通过 ctrl+c 优雅的结束 Golang TCP 服务
date: 2023-07-03T21:31:00+08:00
categories: 
- golang
tags:
- golang
---


草稿

```go

type Server struct {
	listener       *net.TCPListener
	distributeList []func(request *Request) error
	middlewareList []Middleware
	connectPipe    func(connPayload ConnectionPayloadMap) (*Conn, error)
	distributePipe func(request *Request) error
	connectionSet  sync.Map
	listenerMutex  sync.Mutex
	connWaitGroup  sync.WaitGroup
	quitChannel    chan os.Signal
}


func (server *Server) Run() {
	defer server.connWaitGroup.Wait()

	server.connWaitGroup.Add(1)
	go server.listenInterrupt()

	listener, err := net.Listen("tcp", "localhost:50000")
	if err != nil {
        log.Println(err.Error())
        return
    }

	for {

		server.listenerMutex.Lock()
		conn, err := listener.Accept()
		if err != nil {
			log.Println(err.Error())
			if err == net.ErrClosed {
				server.listenerMutex.Unlock()
				return
			}
			server.listenerMutex.Unlock()
			continue
		}
		server.connectionSet.Store(conn, struct{}{})
		server.connWaitGroup.Add(1)
		go server.requestPipe(conn)
		server.listenerMutex.Unlock()
	}

}

func (server *Server) requestPipe(conn *Conn) {
	defer func() {
		conn.Close()
		server.connectionSet.Delete(conn)
		server.connWaitGroup.Done()
	}()

	for {
        buf := make([]byte, 512)
        len, err := conn.Read(buf)
        if err != nil {
            log.Println(err.Error())
            return //终止程序
        }
		server.distributePipe(buf)
    }
}

func (server *Server) listenInterrupt() {
	defer server.connWaitGroup.Done()
	<-server.quitChannel
	log.Println("quit")

	server.listener.Close()

	server.listenerMutex.Lock()
	server.connectionSet.Range(func(conn, _ interface{}) bool {
		conn.(*Conn).Close()
		return true
	})
	server.listenerMutex.Unlock()

}


```