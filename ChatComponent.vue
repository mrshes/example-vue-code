<template>
    <v-row class="pt-0">
        <v-col cols="12" class="elevation-2 pl-10 pb-6 pt-6"><span class="f-24 color_green bold" v-text="room_name">Служба поддержки</span>
        </v-col>
        <v-col cols="12" class="dialog_block" ref="dialog_block">
            <MessageComponent v-for="(item, key) in messages" :key="key"
                              :value="item"></MessageComponent>
        </v-col>
        <v-form @submit.prevent="onSubmit" class="col col-12 mt-5 pl-10 d-flex align-center">
            <v-textarea :value="value" @input="$emit('input', $event)"
                        auto-grow
                        rows="1"
                        label="Введите Ваше сообщение пользователю ..."
                        prepend-icon="comment"
                        @focus="onFocus"

            ></v-textarea>
            <v-btn class="mx-2" fab elevation="0">
                <img src="~/assets/images/svg/icon__paperclip.svg" alt="кнопка прикрепить">
            </v-btn>
            <v-btn class="mx-2" fab elevation="0" type="submit">
                <img src="~/assets/images/svg/icon__button_send.svg" alt="кнопка отправить">
            </v-btn>
        </v-form>
        <v-col cols="12" class="pl-10">
            <span class="warningUser" v-html="warningUser"></span>
        </v-col>
    </v-row>
</template>

<script>
    import {mapActions} from 'vuex';
    import MessageComponent from "./MessageComponent";

    export default {
        name: "ChatComponent",
        components: {MessageComponent},
        data() {
            return {}
        },
        props: {
            value: String,
            messages: Array,
            room_name: String,
            roomId: Number,
        },
        computed: {
            warningUser() {
                let regexPhone = /^((8|\+7)[\- ]?)?(\(?\d{3}\)?[\- ]?)?[\d\- ]{7,10}$/gi;
                if (regexPhone.exec(this.value)) return;
                    return `Обмен личными контактами запрещен правилами сайта.<br> Мы фиксируем такие нарушение, и принимаем незамедлительные меры.<br>
                   Пожалуйста соблюдайте правила сайта для Вашей безопасности.`;

            }
        },
        mounted() {
            this.onScroll();
        },
        methods: {
            ...mapActions({
                getRoomMessages: 'chat/getRoomMessages',
                readMessages: 'chat/readMessages',
                getUnreadRooms: 'chat/getUnreadRooms',

            }),
            onSubmit() {
                this.$emit('submit', {
                    message: this.value,
                });
            },
            onScroll() {
                let block = this.$refs['dialog_block'];
                setTimeout(function () {
                    block.scrollTop = block.scrollHeight;
                }, 100);
            },
            onFocus(e) {
                this.$emit('focus', e);
                this.readMessages({
                    room_id: this.roomId,
                })
                    .then(value => {
                        if (value.status) {
                            this.getRoomMessages(this.roomId);
                            this.getUnreadRooms();
                        }
                    });
            }
        },

        watch: {
            messages(newVal) {
                this.onScroll();
            }
        }
    }
</script>

<style scoped>

</style>
