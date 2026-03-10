<template>
    <h1>Hello World!</h1>
</template>
<script>
    import stompjs from 'stompjs';

    export default{
        name: 'Hello',
        mounted(){

            // const socket = new WebSocket('ws://192.168.0.38:9030/stomp/websocket');
            // const stompClient = stompjs.over(socket);
            let url = 'ws://192.168.0.38:9030/stomp/websocket';
            let ws = stompjs.client(url);
            ws.connect({}, frame => {
                console.log('Connected :' + frame);
                ws.subscribe('/topic/robot', message => {
                    console.log('message: '+ message.body);
                });
            }, error => {
                console.error("error: "+ error);
            })
        },
    };
</script>