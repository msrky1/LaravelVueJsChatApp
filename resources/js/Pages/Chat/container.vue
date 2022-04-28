

<template>
    <AppLayout title="Dashboard">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
             
             <chat-room-selection
             
               v-if = "currentRoom.id"
               :rooms = "chatRooms"
               :currentRoom = "currentRoom"
               v-on:roomchanged = "setRoom($event) "
              /> 
             
            </h2>
        </template>
        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
            <message-container :messages = "messages"/>
            <input-message
             v-on:messagesent = "getMessages()"
             :room ="currentRoom"/>
                </div>
            </div>
        </div>
    </AppLayout>
</template>
<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import chatRoomSelection from '@/Pages/Chat/chatRoomSelection.vue'
import MessageContainer from '@/Pages/Chat/messageContainer.vue';
import InputMessage from '@/Pages/Chat/inputMessage.vue';

 
</script>

<script>
 

 export default {
 



     data: function() {

             return {

                 chatRooms: [],
                 currentRoom: [],
                 messages: [],
             }

     },

     watch: {
       

       currentRoom() {


           this.connect();
       }

     },

     methods: {
            
            connect(){
 
             if(this.currentRoom.id) {

                 let vm = this;
                 this.getMessages();
                 window.Echo.private("chat." + this.currentRoom.id)
                 .listen('.message.new' , e => {


                     vm.getMessages();
                 });
            }
            },

            getRooms() {


                axios.get('/chat/rooms')
                .then(response => {
              
                    this.chatRooms =  response.data;
                    this.setRoom( response.data[0] );

                })
                .catch ( error => {

                       console.log(error);
                })
            },
            

            setRoom ( room ) {

                this.currentRoom = room;
                this.getMessages();
            },

            getMessages() {


                axios.get('/chat/room/' + this.currentRoom.id + '/messages')

                .then(response => {


                    this.messages = response.data;
                })
                 .catch ( error => {

                       console.log(error);
                })
            }


     },


     created() {


         this.getRooms();
     }
 



 }

</script>