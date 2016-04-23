title: Node.js
date: 2013-08-07 22:00:00
tags:
---
###Node.js


####Socket.IO

* io.sockets.emit(all client)
* socket.broadcast.emit(all client except you)
* socket.emit(only you)

socket.io now allows rooms/groups. for a client to join and leave a room:
socket.io 现在原生支持join和leave with room or group

* socket.join('room1');
* socket.leave('room1');



======


	io.sockets.on('connection', function (socket) {
  		//these should do the same thing  
  		io.sockets.emit('this', { receivers: 'everyone'});

  		socket.broadcast.emit('this', { receivers: 'everyone but socket'}); 
  		//emits to everyone but socket
  		socket.emit('this', { receivers: 'socket'}); //emits to socket
	});
	

Request URL: ws://localhost:8080/socket.io/1/websocket/Jr7emUQyeh2cm8vwZYCc

Request Method: GET

Status Code: 101 Switching Protocols



