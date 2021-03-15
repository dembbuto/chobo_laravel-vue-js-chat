<template>
    <div class="flex h-full">
        <chat-user-list
            :current-user="currentUser"
            :chat-with="chatWith"
            @updatedChatWith="updateChatWith"
        >
        </chat-user-list>

        <div v-if="chatWith" class="w-4/5 flex flex-col">
            <chat-area
                :chat-id="chatWith"
                :messages="messages"
            >
            </chat-area>
            <div class="flex-initial p-2">
                <textarea
                    class="border-2 border-solid rounded border-gray-600 w-full p-3 resize-none"
                    type="text"
                    v-model="text"
                    @keyup.enter="submit"
                >
                </textarea>
            </div>
        </div>
        <div v-else class="p-3">
            채팅 상대를 선택해주세요
        </div>
    </div>
</template>

<script>
    import ChatUserList from './ChatUserList';
    import ChatArea from './ChatArea';

    export default {
        props: {
            currentUser: {
                type: Number,
                required: true
            }
        },
        components: {
            ChatUserList,
            ChatArea
        },
        data() {
            return {
                chatWith: null,
                text: '',
                messages: []
            }
        },
        created() {
            window.Echo.private('chats').listen('MessageSent', e => {
                if(e.message.to === this.currentUser && e.message.from === this.chatWith) {
                    this.messages.push(e.message);
                }
            });
        },
        mounted() {
            console.log('Component mounted.')
        },
        methods: {
            updateChatWith(value) {
                this.chatWith = value;
                this.getMessages();
            },
            getMessages() {
                axios.get('/api/messages', {
                    params: {
                        to: this.chatWith,
                        from: this.currentUser
                    }
                }).then(res => {
                    this.messages = res.data.messages;
                })
            },
            submit() {
                if(this.text) {
                    axios.post('/api/messages', {
                        text: this.text,
                        to: this.chatWith,
                        from: this.currentUser
                    }).then(res => {
                        this.messages.push(res.data.message);
                    });
                }
                this.text = '';
            }
        }
    }
</script>

<style>

</style>
